# 07 — Building & Optimizing Images

Writing a Dockerfile is half the job; building it efficiently and keeping images small, secure, and fast is the other half. This is where BuildKit, caching, multi-stage builds, and multi-arch come together.

## 7.1 `docker build` and `docker buildx`

```bash
docker build -t myapp:1.0 .                 # build from ./Dockerfile, context = .
docker build -t myapp:1.0 -f docker/Prod.Dockerfile .   # custom filename
docker build --build-arg VERSION=2.0 -t myapp:2.0 .
docker build --no-cache -t myapp:1.0 .      # ignore cache, rebuild everything
docker build --target build -t myapp:build .   # stop at a named stage
docker build --pull -t myapp:1.0 .          # always re-pull the base image
```

`docker buildx` is the extended, BuildKit-powered build command (it ships as the default builder in modern Docker). It adds multi-platform builds, advanced caching, and multiple output types:

```bash
docker buildx build --platform linux/amd64,linux/arm64 -t myorg/app:1.0 --push .
```

## 7.2 BuildKit: the modern builder

BuildKit is the default build engine. Compared to the old builder it gives you:

- **Parallelism** — independent build stages run concurrently.
- **Better caching** — it understands the dependency graph and only rebuilds what changed; it can import/export cache to a registry for CI.
- **Build secrets and SSH forwarding** — pass credentials at build time *without* baking them into layers.
- **Cache mounts** — persistent caches for package managers across builds.
- **Heredocs** — cleaner multi-line `RUN` blocks.

It's enabled by default; if you ever need to force it on an old setup: `DOCKER_BUILDKIT=1 docker build …`.

## 7.3 How the build cache works (and how to not waste it)

Each instruction produces a layer keyed by the instruction text **and** its inputs (e.g. the contents of files being `COPY`d). On rebuild:

- If the key matches a previous build → **cache hit**, layer reused instantly.
- First miss → that layer and **all subsequent layers** rebuild.

Implications:

1. **Order from least- to most-frequently-changing.** Base image and system packages first; dependency manifests next; application source last.
2. **Copy dependency files separately from source** (the `package.json` then `npm ci` then `COPY .` pattern). Source edits then don't invalidate the dependency-install layer.
3. **Pin versions** so a cached layer doesn't silently become stale (`apt-get install curl=7.*` etc., where practical).
4. `--no-cache` forces a full rebuild; `--pull` refreshes the base image (the cache can otherwise keep an old base).

## 7.4 BuildKit cache mounts

A cache mount keeps a directory (like a package manager's download cache) *persistent across builds* without storing it in the final image:

```dockerfile
# syntax=docker/dockerfile:1
FROM python:3.12-slim
RUN --mount=type=cache,target=/root/.cache/pip \
    pip install -r requirements.txt
```

```dockerfile
RUN --mount=type=cache,target=/var/cache/apt \
    --mount=type=cache,target=/var/lib/apt/lists \
    apt-get update && apt-get install -y curl
```

The `# syntax=docker/dockerfile:1` directive on the first line opts into the latest Dockerfile frontend features (cache mounts, secrets, heredocs).

## 7.5 Multi-stage builds (the biggest single win)

Multi-stage builds let you use a heavy "builder" image with all your compilers/SDKs, then copy *only the final artifact* into a clean, tiny runtime image. The build tools never ship to production.

```dockerfile
# Stage 1: build (big, has the toolchain)
FROM golang:1.22 AS build
WORKDIR /src
COPY go.* ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 go build -o /bin/app ./cmd/app

# Stage 2: runtime (tiny, just the binary)
FROM gcr.io/distroless/static-debian12
COPY --from=build /bin/app /app
USER nonroot:nonroot
ENTRYPOINT ["/app"]
```

Benefits:
- **Dramatically smaller images** (a Go binary on `scratch`/distroless can be a few MB vs hundreds).
- **Smaller attack surface** — no compilers, shells, or package managers in production.
- **One file, one build** — no brittle scripts juggling two images.

You can have many stages, copy between them with `COPY --from=<stage>`, and build only up to a stage with `--target`.

## 7.6 Reducing image size (a checklist)

1. **Pick a lean base** — `-slim`, Alpine, distroless, or `scratch` (section 04.8).
2. **Use multi-stage builds** to leave build tooling behind.
3. **Combine `RUN`s and clean up in the same layer:**
   ```dockerfile
   RUN apt-get update && apt-get install -y --no-install-recommends pkg \
       && rm -rf /var/lib/apt/lists/*
   ```
   (Cleaning in a *separate* `RUN` doesn't shrink the image — section 04.2.)
4. **`--no-install-recommends`** (apt) / `--no-cache` (apk) to skip optional extras.
5. **Don't install dev/test dependencies** in the runtime stage (`npm ci --omit=dev`, `pip install --no-cache-dir`).
6. **Use `.dockerignore`** to keep junk out of the context and out of `COPY . .`.
7. **Remove caches**: pip (`--no-cache-dir`), npm (`npm cache clean`), apt lists, etc.
8. **Inspect with `docker history`** and tools like **dive** to find fat layers.

## 7.7 Build secrets (don't bake credentials into images)

Never `COPY` a private key or `ARG` a token — they persist in image layers/history. BuildKit secrets mount a secret only for one `RUN`, leaving no trace:

```dockerfile
# syntax=docker/dockerfile:1
RUN --mount=type=secret,id=npmtoken \
    NPM_TOKEN=$(cat /run/secrets/npmtoken) npm ci
```
```bash
docker build --secret id=npmtoken,src=$HOME/.npmtoken -t myapp .
```

For Git/SSH access during build, use `--mount=type=ssh` with `--ssh default`.

## 7.8 `.dockerignore`

A `.dockerignore` file (next to the Dockerfile) excludes paths from the **build context**. This speeds up builds (less data sent to the daemon), avoids cache busting, and prevents secrets/junk from sneaking into the image via `COPY . .`.

```
.git
node_modules
dist
*.log
.env
.env.*
Dockerfile
docker-compose.yml
**/__pycache__
coverage/
```

Always exclude `.git`, local dependency directories, build outputs, and any secret files.

## 7.9 Multi-architecture images

To run the same tag on `amd64` servers and `arm64` machines (Apple Silicon, AWS Graviton, Raspberry Pi), build a **multi-platform image** with buildx:

```bash
# one-time: create a builder that supports multiple platforms
docker buildx create --name multi --use

docker buildx build \
  --platform linux/amd64,linux/arm64 \
  -t myorg/app:1.0 \
  --push .                      # must push: a manifest list lives in a registry
```

Under the hood buildx builds each platform (using emulation via QEMU if your host isn't that arch) and publishes a **manifest list** so `docker pull` auto-selects the right variant (section 04.4). Cross-arch builds via emulation are slower; native build farms or cloud builders speed this up.

## 7.10 Tagging strategy

- **Never deploy only `latest`.** It's mutable and ambiguous.
- Tag with a **specific version** (`1.4.2`) and/or the **Git SHA** (`app:sha-3f9a2c1`) so every deploy is traceable.
- It's fine to *also* push `latest` (or `stable`) as a convenience pointer, alongside the immutable tag.
- For maximum reproducibility, deploy by **digest** (`@sha256:…`).

A common CI pattern: build once, tag with the commit SHA, push; promote the *same digest* through environments rather than rebuilding per environment.

## 7.11 Inspecting and analyzing builds

```bash
docker history myapp:1.0           # per-layer size and the command that made it
docker image inspect myapp:1.0     # full config
docker buildx build --progress=plain .   # verbose build output (debugging)
docker scout cves myapp:1.0        # scan for known vulnerabilities (section 13)
```

External tool worth knowing: **dive** — an interactive TUI that shows what each layer adds and flags wasted space.

## 7.12 Reproducibility and provenance (modern concerns)

Supply-chain security has pushed the ecosystem toward:

- **SBOMs (Software Bill of Materials)** — a manifest of everything in an image. Modern Docker can generate these at build time; `docker scout sbom` inspects them. (Note: the older `docker sbom` command is deprecated in favor of `docker scout sbom`.)
- **Provenance attestations** — signed metadata about *how* an image was built (buildx can attach these).
- **Image signing** — proving an image came from you (Cosign/Sigstore; Docker Content Trust historically). See section 13.

For most learners, the takeaway is: builds aren't just "make it work" — increasingly they must be *traceable and verifiable*, and the tooling to do that is built into buildx/Scout.

---
**Previous:** [06 — Dockerfile](06-dockerfile.md) | **Next:** [08 — Storage & Volumes](08-storage-and-volumes.md)
