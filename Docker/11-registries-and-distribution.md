# 11 — Registries & Distribution

## 11.1 What a registry is

A **registry** is a server that stores and distributes Docker images. It's the "ship" in build-ship-run. A registry holds **repositories**, each of which holds tagged versions (and digests) of an image.

- **Registry** — the server (e.g. `docker.io`, `ghcr.io`, your private one).
- **Repository** — a named collection of related images (e.g. `library/nginx`, `myorg/api`).
- **Tag** — a label for a specific version within a repository (`:1.4.2`, `:latest`).

Registries implement the **OCI Distribution Specification**, so they're interoperable: an image pushed to one OCI registry can be pulled by any OCI-compliant client.

## 11.2 Docker Hub

**Docker Hub** (`docker.io`) is the default registry — when you `docker pull nginx`, it comes from here. It hosts:

- **Official Images** — curated, maintained images for common software (`nginx`, `postgres`, `python`, `redis`…). These live in the `library/` namespace and are a safe starting point.
- **Verified Publisher** images — from vetted vendors.
- **Community images** — anyone's public (or private) repositories under `username/repo`.

Things to know about Docker Hub:
- **Rate limits** apply to anonymous and free-tier pulls. In CI especially, unauthenticated pulls can hit limits and fail builds; authenticate (`docker login`) or use a pull-through mirror to avoid this.
- Free accounts get limited private repositories; paid tiers add more.

## 11.3 Authenticating, pushing, and pulling

```bash
# Log in (prompts for credentials; stores a token in ~/.docker/config.json)
docker login                                  # Docker Hub
docker login ghcr.io                          # another registry
docker login myregistry.example.com:5000      # a private registry

# Tag your image for the target repository, then push
docker tag myapp:1.0 myusername/myapp:1.0
docker push myusername/myapp:1.0

# Pull elsewhere
docker pull myusername/myapp:1.0

docker logout                                 # remove stored credentials
```

The rule people forget: **the image's name *is* its destination.** To push to `myusername/myapp`, the image must be **tagged** `myusername/myapp:tag` first. You can't push an image named `myapp:1.0` to your Hub account without retagging it with your namespace.

For private registries on a custom host, the registry hostname is part of the tag:

```bash
docker tag myapp:1.0 myregistry.example.com:5000/team/myapp:1.0
docker push myregistry.example.com:5000/team/myapp:1.0
```

> **Credential security:** by default `docker login` stores a base64-encoded token in `~/.docker/config.json` (not encrypted). Use a **credential helper** (OS keychain, `docker-credential-*`) to store secrets securely, and in CI prefer short-lived tokens.

## 11.4 Other common registries

- **GitHub Container Registry** — `ghcr.io/owner/image`. Tightly integrated with GitHub Actions.
- **GitLab Container Registry** — bundled with GitLab projects.
- **Cloud provider registries** — AWS **ECR**, Google **Artifact Registry**, Azure **ACR**. These integrate with cloud IAM for auth and are the usual choice when running on that cloud.
- **Self-hosted** — the open-source `registry` image (Distribution), or full-featured platforms like **Harbor** (adds RBAC, scanning, replication, signing).

All of these are OCI registries; the workflow (`login`/`tag`/`push`/`pull`) is identical — only the hostname and auth differ.

## 11.5 Running your own registry

The simplest private registry is the official `registry` image:

```bash
docker run -d -p 5000:5000 --name registry \
  -v registry-data:/var/lib/registry \
  registry:2

# Use it
docker tag myapp:1.0 localhost:5000/myapp:1.0
docker push localhost:5000/myapp:1.0
docker pull localhost:5000/myapp:1.0
```

For anything beyond a toy, you must add **TLS** and **authentication**. By default the daemon refuses plain-HTTP registries; you'd otherwise have to mark it "insecure" in `daemon.json` (only acceptable on trusted private networks). For production, use TLS + auth, or run **Harbor** for a managed experience with scanning, RBAC, and image signing built in.

## 11.6 Registry mirrors / pull-through cache

To reduce external bandwidth and dodge Docker Hub rate limits, you can run a **pull-through cache** (a registry configured to mirror Docker Hub). Configure the daemon to use it:

```json
// /etc/docker/daemon.json
{ "registry-mirrors": ["https://mirror.example.com"] }
```

The first pull of an image fetches from upstream and caches it; subsequent pulls across your network are served locally and fast. Very common in CI farms and air-gapped/locked-down environments.

## 11.7 Image references recap (because pushing depends on it)

From section 04, the full form is:

```
registry-host[:port]/namespace/repository:tag@sha256:digest
```

- Omit the registry → defaults to Docker Hub.
- Omit the tag → defaults to `:latest`.
- The **digest** uniquely and immutably identifies content; **deploy by digest** for guaranteed reproducibility.

## 11.8 Content trust, signing, and provenance

A registry just stores bytes; you should verify those bytes are what you expect.

- **Image signing** proves an image was published by a trusted party and hasn't been tampered with. Historically Docker offered **Docker Content Trust (DCT)** (`export DOCKER_CONTENT_TRUST=1` to enforce signed pulls/pushes). The broader industry has largely moved to **Sigstore/Cosign** for signing OCI artifacts.
- **Provenance attestations & SBOMs** (section 07.12) can be attached to images so consumers can verify *how* and *from what* an image was built.
- **Vulnerability scanning** at the registry (Harbor, ECR, `docker scout`) flags known CVEs before images are deployed (section 13).

The trend: treat images like signed, auditable software artifacts, not anonymous blobs.

## 11.9 Distribution without a registry

For air-gapped or quick transfers you can skip a registry entirely (section 04.6):

```bash
docker save myapp:1.0 | gzip > myapp.tar.gz      # on the source machine
# ...copy the file across...
gunzip -c myapp.tar.gz | docker load             # on the target machine
```

This moves the full image (layers + metadata) as a file. Handy, but you lose registry conveniences (dedup across pulls, access control, scanning).

## 11.10 A typical CI/CD distribution flow

1. CI checks out code and runs `docker build` (or `buildx` for multi-arch).
2. Image is tagged with the **Git commit SHA** (immutable, traceable) and optionally a semantic version.
3. CI runs `docker login` with a short-lived token and `docker push` to the registry.
4. (Optional) the registry/scanner runs vulnerability scanning; the image is **signed**.
5. Deployment pulls the image **by digest** (or the SHA tag) and runs it — the *exact same bytes* that were tested are what run in production.

This "build once, promote the same artifact" pattern is the backbone of reliable container delivery.

---
**Previous:** [10 — Docker Compose](10-docker-compose.md) | **Next:** [12 — Orchestration & Swarm](12-orchestration-and-swarm.md)
