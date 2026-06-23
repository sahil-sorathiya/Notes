# 03 — Docker Architecture

Understanding the architecture removes most of the "magic" from Docker. Once you know which component does what, error messages and behaviour stop being surprising.

## 3.1 The client–server model

Docker uses a **client–server** architecture. The pieces:

- **Docker Client (`docker`)** — the CLI you type into. It does *almost nothing itself*; it translates your commands into HTTP requests to the daemon's REST API.
- **Docker Daemon (`dockerd`)** — a long-running background server that does the real work: building images, running containers, managing networks, volumes, and so on. It listens on a socket (Unix socket `/var/run/docker.sock` by default, or a TCP port).
- **Registry** — a remote store of images (e.g. Docker Hub). The daemon pulls from and pushes to it.

```
        ┌──────────────┐        REST API over          ┌─────────────────────────────┐
        │ docker CLI   │  ───  /var/run/docker.sock ──► │        dockerd (daemon)      │
        │ (client)     │                                │                              │
        └──────────────┘                                │  builds images               │
                                                        │  runs/stops containers       │
        ┌──────────────┐                                │  manages volumes & networks  │
        │  Registry    │  ◄──── pull / push ──────────► │                              │
        │ (Docker Hub) │                                └──────────────┬───────────────┘
        └──────────────┘                                               │
                                                          delegates running of containers
                                                                       ▼
                                                        ┌─────────────────────────────┐
                                                        │ containerd  ──►  runc        │
                                                        └─────────────────────────────┘
```

Because the client and daemon talk over a network API, the client can control a **remote** daemon (this is what `docker context` and `DOCKER_HOST` do). The CLI on your laptop can manage Docker on a server far away.

> **Security note:** anything that can reach the Docker socket can fully control the host. Never expose the daemon over an unauthenticated TCP port, and treat `docker.sock` as a crown-jewel secret (mounting it into a container hands that container root-equivalent power).

## 3.2 The modular runtime stack: dockerd → containerd → runc

Modern Docker is *not* one monolith. Over the years it was broken into layered, standardized components:

1. **`dockerd`** — the high-level Docker daemon. Handles the Docker API, image building, networking, volumes, and the developer-facing features. It does **not** itself execute containers.
2. **`containerd`** — a CNCF-graduated, OCI-compliant **container runtime daemon**. It manages the complete container lifecycle: pulling/storing images, supervising container processes, and handling low-level storage and networking primitives. Kubernetes also uses containerd directly (without Docker), which is why it's a shared industry standard.
3. **`containerd-shim`** — a tiny per-container process that sits between containerd and the running container. Its job is to keep the container alive and reattachable even if `containerd` or `dockerd` restarts (so upgrading the daemon doesn't kill your containers).
4. **`runc`** — the low-level **OCI runtime**. This is the program that actually creates the container: it sets up the namespaces and cgroups, applies the configuration, and `exec`s your process. After startup, `runc` exits — it's a one-shot tool, not a long-running supervisor.

The flow when you run a container:

```
docker run ... → dockerd → containerd → containerd-shim → runc → your process
```

**Why split it up?** Modularity (each layer is replaceable), standardization (OCI compliance), and resilience (the shim decouples container lifetime from daemon lifetime). It also means you can swap `runc` for alternative runtimes:

- **`crun`** — a faster C implementation of the OCI runtime.
- **gVisor (`runsc`)** — adds a user-space kernel for stronger isolation.
- **Kata Containers** — runs each container in a lightweight micro-VM for VM-grade isolation.

## 3.3 The kernel features that make containers possible

Docker doesn't isolate anything itself — it asks the **Linux kernel** to do it. Two families of features do the heavy lifting.

### Namespaces — "what a process can see"

Each namespace type gives a process a private view of one kind of system resource:

| Namespace | Isolates |
|-----------|----------|
| **PID**   | Process IDs — container sees its own process tree, its main process is PID 1 |
| **NET**   | Network stack — own interfaces, IPs, routing tables, ports |
| **MNT**   | Mount points — own filesystem view |
| **UTS**   | Hostname and domain name |
| **IPC**   | Inter-process communication (shared memory, semaphores) |
| **USER**  | User and group IDs — enables "root in container ≠ root on host" |
| **CGROUP**| The view of the cgroup hierarchy |
| **TIME**  | System clocks (newer kernels; recent Docker can enable a private time namespace) |

Two containers each think they're alone on the machine because each lives in its own set of namespaces.

### cgroups — "what a process can use"

Control groups meter and limit resource consumption:

- **CPU** — shares, quotas, pinning to specific cores.
- **Memory** — hard limits; exceed it and the kernel OOM-kills the process.
- **Block I/O** — read/write bandwidth and IOPS.
- **PIDs** — cap the number of processes (defends against fork bombs).

This is what `--memory`, `--cpus`, `--pids-limit`, etc. configure. **cgroup v2** (the modern unified hierarchy) is preferred and required for some rootless features.

### Supporting features

- **Union/overlay filesystems** (e.g. `overlay2`) — stack read-only image layers under a thin writable layer (section 04).
- **Capabilities** — Linux splits root's powers into ~40 fine-grained capabilities; Docker drops most by default, keeping containers far less privileged than full root (section 13).
- **Seccomp** — filters which syscalls a container may make; Docker ships a default profile blocking dangerous ones.
- **AppArmor / SELinux** — mandatory access control layered on top for defense in depth.

## 3.4 The image store (storage drivers)

The daemon needs to store image layers and the container's writable layer on disk. Historically this was handled by a **graph driver** (most commonly `overlay2`).

**Change to note:** since **Docker Engine v29**, new installations default to the **containerd image store** rather than the legacy graph drivers. Practical benefits:

- Native **multi-platform image** support (hold `amd64` and `arm64` under one tag locally).
- Better alignment with the rest of the ecosystem (Kubernetes uses containerd too).
- A single, consistent content/layer model.

You can see which store/driver is active via `docker info` under "Storage Driver." Legacy graph drivers still work but are now deprecated.

## 3.5 The objects the daemon manages

Everything Docker does revolves around a small set of objects:

- **Images** — read-only templates (section 04).
- **Containers** — runnable instances of images (section 05).
- **Volumes** — persistent, Docker-managed storage (section 08).
- **Networks** — virtual networks connecting containers (section 09).
- **Plugins** — extensions for volume/network/auth drivers.

The CLI is organized around these nouns: `docker image …`, `docker container …`, `docker volume …`, `docker network …`. (Older shortcuts like `docker ps`, `docker run`, `docker rmi` still exist for convenience.)

## 3.6 The build / ship / run pipeline

Tie it together with Docker's own three-word slogan:

- **Build** — `dockerd` (via **BuildKit**) reads a Dockerfile and produces an image. BuildKit is the modern build engine: it builds layers in parallel, caches aggressively, and supports advanced features like build secrets and cache mounts (section 07).
- **Ship** — `docker push` uploads the image to a registry; `docker pull` downloads it elsewhere. Images are addressed by name + tag, and uniquely by **digest** (a content hash).
- **Run** — `docker run` turns an image into a live container via the containerd→runc chain described above.

## 3.7 Where everything lives on disk (Linux)

```
/var/lib/docker/
├── image/            # image metadata and layer references
├── overlay2/         # (if using overlay2) actual layer filesystems
├── containers/       # per-container metadata and (default) log files
├── volumes/          # named volume contents
└── network/          # network state
```

You generally **don't** edit these by hand — let the daemon manage them. But knowing they exist explains where disk space goes and why `docker system prune` (section 15) can reclaim gigabytes.

---
**Previous:** [02 — Installation](02-installation-and-setup.md) | **Next:** [04 — Images](04-images.md)
