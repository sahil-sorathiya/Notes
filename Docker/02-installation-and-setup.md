# 02 — Installation & Setup

## 2.1 The two things called "Docker"

Before installing, understand what you're installing. People say "Docker" to mean two different products:

- **Docker Engine** — the core open-source runtime: the daemon (`dockerd`), the CLI (`docker`), `containerd`, and `runc`. It runs natively on **Linux**. This is what runs on servers and in CI.
- **Docker Desktop** — a commercial GUI application for **macOS, Windows, and Linux** that bundles Docker Engine *inside a managed Linux VM*, plus extras: a dashboard, Kubernetes, volume management, extensions, and automatic updates.

> **Licensing note:** Docker Desktop is free for personal use, education, and small businesses, but **requires a paid subscription for commercial use in larger organizations** (the threshold has been around 250+ employees or $10M+ annual revenue). Docker Engine itself (on Linux) is Apache-2.0 licensed and free. Always check current terms before deploying at a company.

On macOS and Windows you essentially *must* use a VM-backed solution (Docker Desktop, or alternatives like Rancher Desktop, Podman, Colima) because Linux containers need a Linux kernel.

## 2.2 Installing on Linux (Docker Engine)

The recommended method is Docker's official `apt`/`dnf` repository, not the distro's bundled `docker.io` package (which is often outdated). Below is the typical flow for **Ubuntu/Debian**; consult the official docs for your exact distro.

```bash
# 1. Remove any old/conflicting packages
sudo apt-get remove docker docker-engine docker.io containerd runc

# 2. Set up Docker's official APT repository (add GPG key + repo)
#    (Docker provides a convenience script and manual steps; manual is preferred for production.)

# 3. Install the Engine, CLI, containerd, BuildKit (buildx) and the Compose plugin
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io \
                     docker-buildx-plugin docker-compose-plugin

# 4. Verify
sudo docker run hello-world
```

A **convenience script** also exists for quick (non-production) setups:

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

Use the script for personal machines/labs; for servers, install from the repo so updates flow through your package manager.

## 2.3 Post-installation steps (Linux)

These small steps save a lot of friction.

**Run Docker without `sudo`.** By default the daemon socket is owned by `root`, so every command needs `sudo`. Add your user to the `docker` group:

```bash
sudo groupadd docker            # usually already exists
sudo usermod -aG docker $USER   # add yourself
newgrp docker                   # apply in current shell (or log out/in)
docker run hello-world          # should now work without sudo
```

> **Security caveat:** membership in the `docker` group is effectively **root-equivalent** — anyone in it can mount the host filesystem into a container and escalate. Treat the docker group like sudo access. For a stronger posture, use **rootless mode** (section 2.6).

**Start Docker on boot** (systemd):

```bash
sudo systemctl enable docker.service
sudo systemctl enable containerd.service
sudo systemctl start docker
```

**Check status / logs:**

```bash
systemctl status docker
journalctl -u docker            # daemon logs
```

## 2.4 Installing on macOS

Options:

- **Docker Desktop for Mac** — the standard choice. Native installers for Apple Silicon (M-series) and Intel. Provides a VM (using Apple's Virtualization framework), a GUI, and an embedded Kubernetes.
- **Colima** / **Rancher Desktop** / **Podman** — lighter or license-free alternatives that also spin up a Linux VM and expose a Docker-compatible CLI.

On Apple Silicon, images are `arm64` by default. You can still run `amd64` images via emulation (Rosetta/QEMU), but it's slower — prefer multi-arch images (section 07.9).

> Docker Desktop on macOS offers **VirtioFS** file sharing, which dramatically speeds up bind-mounted source code compared to older mechanisms — worth enabling for large codebases.

## 2.5 Installing on Windows

Two container worlds exist on Windows:

- **Linux containers** (the common case) run inside a lightweight Linux VM. The recommended backend is **WSL 2** (Windows Subsystem for Linux), which gives near-native performance and tight integration with WSL distros.
- **Windows containers** run native Windows processes and require Windows base images. These are a niche used for legacy .NET Framework apps and need matching Windows host/kernel versions.

Setup essentials:

1. Enable **WSL 2** and install a Linux distro (e.g. Ubuntu) from the Microsoft Store.
2. Install **Docker Desktop for Windows** and select the **WSL 2 backend**.
3. In Docker Desktop settings, enable integration with your WSL distros so `docker` works from inside WSL shells.

For real development on Windows, do your work *inside* WSL 2 (your code and the Docker engine share the same Linux filesystem, avoiding slow cross-OS file access).

## 2.6 Rootless mode (hardened setup)

By default the Docker daemon runs as `root`. **Rootless mode** runs the daemon and containers as an unprivileged user, using user namespaces so that "root inside the container" maps to an ordinary user on the host. This greatly reduces the blast radius of a container escape.

```bash
# Provided helper sets up a per-user rootless daemon
dockerd-rootless-setuptool.sh install
```

Trade-offs to know:

- Some features need extra configuration (e.g. exposing ports below 1024, certain networking and storage drivers, cgroup-based limits depend on cgroup v2 delegation).
- It's the safest default for multi-tenant or untrusted workloads on shared machines.

## 2.7 Verifying and understanding your installation

```bash
docker --version          # CLI version
docker version            # detailed client + server (daemon) versions
docker info               # full daemon config: storage driver, image store,
                          # cgroup version, runtimes, registry, # of containers/images
docker run hello-world    # end-to-end smoke test (pulls + runs a tiny image)
```

`docker info` is the most informative command after install. Things to look for:

- **Storage Driver** — modern installs use the **containerd image store** (default since Engine v29) or `overlay2`.
- **Cgroup Version** — prefer **cgroup v2** (better resource control, required for some rootless features).
- **Default Runtime** — usually `runc`.
- **Logging Driver** — default is `json-file`.

## 2.8 The Docker config & key file locations (Linux)

| Path | Purpose |
|------|---------|
| `/var/run/docker.sock` | The Unix socket the CLI talks to the daemon over |
| `/etc/docker/daemon.json` | Daemon configuration (registry mirrors, log driver, default address pools, storage options) |
| `/var/lib/docker/` | Everything the daemon stores: images, container layers, volumes, networks |
| `~/.docker/config.json` | Per-user CLI config: registry credentials, CLI plugins, contexts |

Example `daemon.json` (apply with `sudo systemctl restart docker`):

```json
{
  "log-driver": "json-file",
  "log-opts": { "max-size": "10m", "max-file": "3" },
  "default-address-pools": [
    { "base": "172.30.0.0/16", "size": 24 }
  ],
  "registry-mirrors": ["https://my-mirror.example.com"]
}
```

The `log-opts` above is one of the most valuable changes you can make on any host: it caps container log file growth so logs can't silently fill the disk.

## 2.9 Docker contexts (managing multiple daemons)

A **context** tells the CLI *which* Docker daemon to talk to — local, a remote server over SSH, or a cloud endpoint.

```bash
docker context ls                                   # list contexts
docker context create remote --docker "host=ssh://user@server"
docker context use remote                           # switch target daemon
docker --context default ps                         # one-off override
```

This lets one CLI drive many engines without copying certs around — very handy for managing remote hosts.

---
**Previous:** [01 — Introduction](01-introduction-and-containerization.md) | **Next:** [03 — Architecture](03-architecture.md)
