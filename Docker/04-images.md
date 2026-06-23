# 04 — Docker Images

## 4.1 What an image actually is

A Docker image is a **read-only, layered filesystem snapshot plus metadata**, packaged so it can be stored, shared, and run anywhere. Concretely an image is:

1. A stack of **filesystem layers** (each a set of file changes).
2. A **configuration object** (JSON) describing how to run it: the default command, entrypoint, environment variables, exposed ports, working directory, user, etc.
3. A **manifest** that ties the layers and config together and is addressed by a **digest** (a SHA-256 content hash).

An image is **immutable**. Once built, it never changes. If you need a change, you build a *new* image. This immutability is what makes deployments reproducible — the bytes you tested are exactly the bytes that run in production.

## 4.2 Layers and the union filesystem

This is the most important concept in this section.

Images are built in **layers**. Each instruction in a Dockerfile that changes the filesystem (`RUN`, `COPY`, `ADD`) creates a new layer on top of the previous one. A layer records only the *difference* from the layer beneath it.

When you run a container, Docker uses a **union (overlay) filesystem** to stack all the read-only image layers and add a thin **writable container layer** on top:

```
        ┌───────────────────────────────┐
        │  Writable container layer      │  ← per-container, ephemeral
        ├───────────────────────────────┤
        │  Layer: COPY app source        │ ┐
        ├───────────────────────────────┤ │
        │  Layer: RUN pip install ...    │ │  read-only
        ├───────────────────────────────┤ │  IMAGE
        │  Layer: base OS files          │ │  layers
        └───────────────────────────────┘ ┘
```

Key properties that follow from this design:

- **Sharing / deduplication:** layers are content-addressed and shared between images. If ten images all build on `python:3.12-slim`, that base is stored **once** on disk and downloaded **once**. Pulling a new image only downloads layers you don't already have.
- **Copy-on-write (CoW):** the running container sees a merged view. When it *reads* a file, it comes from whichever layer has it. When it *writes* or *modifies* a file, the file is first **copied up** into the writable layer, and the change is made there. The underlying image layers are never touched.
- **Ephemerality of the writable layer:** when the container is removed, its writable layer is destroyed. **Anything written inside a container that isn't in a volume is lost when the container is deleted.** This is *the* reason volumes exist (section 08).
- **Caching during builds:** because each layer is deterministic, Docker can reuse cached layers when rebuilding (section 07).

### A subtlety: deletions don't shrink images

Removing a file in a later layer doesn't reclaim the space from the earlier layer — the file still exists below; the upper layer just marks it as deleted (a "whiteout"). So `RUN apt-get install …` followed by `RUN apt-get remove …` in a *separate* layer makes the image **bigger**, not smaller. To truly save space you must add and remove within the **same** `RUN` (section 07.6).

## 4.3 Naming: repositories, tags, and digests

An image reference has the structure:

```
[registry-host[:port]/][namespace/]repository[:tag][@digest]
```

Examples:

```
nginx                                   # → docker.io/library/nginx:latest
python:3.12-slim                        # official image, specific tag
ghcr.io/acme/api:1.4.2                   # GitHub Container Registry
myregistry.example.com:5000/team/app:dev # private registry on a custom port
ubuntu@sha256:abc123...                  # pinned by immutable digest
```

Defaults to be aware of:

- If you omit the registry, Docker assumes **Docker Hub** (`docker.io`).
- Official Docker Hub images live under the `library/` namespace (so `nginx` = `library/nginx`).
- If you omit the tag, Docker assumes **`:latest`** — which is just a conventional tag name, **not** a guarantee of the newest version. Treating `latest` as "current" is a common trap.

**Tags vs digests:**

- A **tag** (`:1.4.2`, `:latest`) is a *mutable, human-friendly pointer*. The same tag can be re-pushed to point at different content over time.
- A **digest** (`@sha256:…`) is an *immutable content hash*. The same digest always means exactly the same bytes.

For reproducible, secure deployments, pin by digest (or at least by a specific immutable tag) rather than relying on `latest`.

## 4.4 Image manifests and multi-architecture images

A single tag can actually point to a **manifest list** (a.k.a. image index) — a small document that maps platforms to per-platform image manifests:

```
nginx:latest  ──►  manifest list
                     ├── linux/amd64  → manifest A → layers...
                     ├── linux/arm64  → manifest B → layers...
                     └── linux/arm/v7 → manifest C → layers...
```

When you `docker pull nginx`, the daemon automatically selects the manifest matching your platform. This is why the same `docker run nginx` works on an Intel server and an Apple Silicon laptop. Building such images yourself is covered in section 07.9.

## 4.5 Working with images: the essential commands

```bash
# Pull an image from a registry
docker pull python:3.12-slim
docker pull nginx@sha256:<digest>          # pull an exact, immutable image

# List local images
docker images
docker image ls
docker image ls --digests                  # show digests too

# Inspect everything about an image (config, layers, env, entrypoint…)
docker inspect python:3.12-slim
docker image inspect python:3.12-slim --format '{{.Config.Entrypoint}}'

# View the layer-by-layer history (and which command created each layer)
docker history nginx

# Tag an image (creates another name pointing to the same image ID)
docker tag myapp:latest myregistry.example.com/team/myapp:1.0.0

# Remove an image
docker rmi python:3.12-slim
docker image rm <image-id>

# Remove dangling (untagged) images
docker image prune
docker image prune -a                       # remove ALL unused images
```

> **Tip:** `docker history <image>` is underused. It shows the size each layer contributes, which immediately reveals where your image is fat.

## 4.6 Saving, loading, importing, exporting

These move images and filesystems around without a registry — useful for air-gapped environments.

| Command | What it does |
|---------|--------------|
| `docker save -o app.tar myapp:1.0` | Export an **image** (all layers + metadata) to a tar archive |
| `docker load -i app.tar` | Import an image tar produced by `save` |
| `docker export <container> -o fs.tar` | Export a **container's filesystem** as a flat tar (no layers/history) |
| `docker import fs.tar myflat:1.0` | Create an image from a flat filesystem tar |

The distinction matters: **save/load** preserve layers, tags, and history (image ↔ archive); **export/import** flatten everything into a single layer (container filesystem ↔ image), losing history and metadata.

## 4.7 Image IDs vs digests vs tags (clearing up the confusion)

You'll see several identifiers; here's how they relate:

- **Tag** — human pointer (`myapp:1.0`). Mutable.
- **Image ID** — the local content hash of the image *config*; what `docker images` shows in the IMAGE ID column. Local concept.
- **Digest** — the content hash of the image *manifest* in a registry; what you pin with `@sha256:…`. Stable across machines.

Multiple tags can share one Image ID (they're aliases). `docker tag` never copies data — it just adds another name.

## 4.8 Base images: choosing wisely

The base image (the `FROM` line) determines size, security surface, and compatibility. Common families:

- **Full distro** (`ubuntu`, `debian`) — familiar, lots of tooling, larger (100+ MB), more packages = more potential CVEs.
- **`-slim` variants** (`debian:slim`, `python:3.12-slim`) — stripped-down distro; a good default balance.
- **Alpine** (`alpine`, `python:3.12-alpine`) — tiny (~5 MB base) using `musl` libc and `apk`. Great size, but `musl` (vs `glibc`) occasionally causes subtle incompatibilities, slower DNS in some cases, and harder debugging of native dependencies.
- **Distroless** (Google's `gcr.io/distroless/*`) — contains only your app and its runtime, **no shell or package manager**. Smallest attack surface, but you can't `exec` a shell into it to debug.
- **`scratch`** — the empty image. Used for fully static binaries (e.g. Go). Nothing inside but your binary.

Rule of thumb: start with a `-slim` or language-specific image; move toward `distroless`/`scratch` for production once things are stable and you value the security/size wins more than debug convenience.

## 4.9 How a container relates to its image (recap)

```
image  =  read-only layers + config        (the template)
container  =  image  +  thin writable layer + isolated runtime  (the running instance)
```

You can run many containers from one image; each gets its own writable layer and its own isolated process/network/PID namespaces, but they all *share* the same underlying read-only layers on disk.

---
**Previous:** [03 — Architecture](03-architecture.md) | **Next:** [05 — Containers](05-containers.md)
