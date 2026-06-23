# Docker — Complete Study Notes

A structured, in-depth set of notes for learning Docker from the ground up. Each file is a self-contained section. Read them in order if you're new, or jump to a topic if you're revising.

> These notes reflect modern Docker (Docker Engine **v29+**, Compose **v2**, BuildKit as the default builder). Where behaviour changed recently, it's called out.

## Table of contents

| #  | File | What it covers |
|----|------|----------------|
| 01 | [Introduction & Containerization](01-introduction-and-containerization.md) | What containers are, why they exist, containers vs VMs, the OCI standard, history |
| 02 | [Installation & Setup](02-installation-and-setup.md) | Installing Docker on Linux/Mac/Windows, Docker Desktop vs Engine, post-install, rootless mode |
| 03 | [Architecture](03-architecture.md) | Client–daemon model, containerd, runc, the build/ship/run pipeline, namespaces & cgroups |
| 04 | [Images](04-images.md) | Image layers, the union filesystem, tags & digests, pulling/inspecting, the image store |
| 05 | [Containers](05-containers.md) | Container lifecycle, `run` flags in depth, exec, logs, resource limits, restart policies |
| 06 | [Dockerfile](06-dockerfile.md) | Every instruction explained, with semantics, gotchas, and examples |
| 07 | [Building & Optimizing Images](07-building-and-optimizing-images.md) | BuildKit, build cache, multi-stage builds, `.dockerignore`, slimming images, multi-arch |
| 08 | [Storage & Volumes](08-storage-and-volumes.md) | Volumes vs bind mounts vs tmpfs, drivers, backup/restore, the writable layer |
| 09 | [Networking](09-networking.md) | Bridge/host/overlay/macvlan/none, DNS, port publishing, container communication |
| 10 | [Docker Compose](10-docker-compose.md) | The Compose file in depth, services, networks, volumes, profiles, healthchecks, lifecycle |
| 11 | [Registries & Distribution](11-registries-and-distribution.md) | Docker Hub, private registries, push/pull, auth, content trust, mirrors |
| 12 | [Orchestration & Swarm](12-orchestration-and-swarm.md) | Why orchestration, Swarm mode, services, stacks, scaling, and where Kubernetes fits |
| 13 | [Security](13-security.md) | Threat model, least privilege, capabilities, user namespaces, secrets, scanning, hardening |
| 14 | [Logging, Monitoring & Debugging](14-logging-monitoring-debugging.md) | Logging drivers, stats, events, healthchecks, debugging a broken container |
| 15 | [Best Practices & Production](15-best-practices-and-production.md) | Image, Dockerfile, runtime, and CI/CD best practices; common anti-patterns |
| 16 | [CLI Cheat Sheet](16-cli-cheat-sheet.md) | Every command you'll actually use, grouped by task |

## How to use these notes

1. **Skim 01–03 first.** They give you the mental model. Everything else makes more sense once you understand *why* containers exist and *how* the pieces fit together.
2. **Type the commands.** Docker is a muscle-memory skill. Run a `hello-world`, break things, read the errors.
3. **Build one real thing.** Pick a small app, write a Dockerfile (06–07), wire it up with Compose (10). That single exercise teaches you 80% of day-to-day Docker.
4. **Come back to 13 and 15** once the basics feel comfortable — security and production hygiene are what separate "it runs on my machine" from "it runs in production."

## A one-paragraph summary of all of it

Docker packages an application together with its dependencies into a portable **image** — a read-only, layered filesystem snapshot plus metadata. You run an image to get a **container**, an isolated process on the host that *shares the host kernel* (unlike a VM, which virtualizes a whole OS). Images are built declaratively from a **Dockerfile**, stored and shared via **registries**, and run by the **Docker daemon** (which delegates to `containerd` and `runc`). Persistent data lives in **volumes**; containers talk to each other over **networks**; and multiple containers are coordinated with **Compose** (single host) or an **orchestrator** like Swarm or Kubernetes (many hosts).
