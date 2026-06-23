# 01 — Introduction & Containerization

## 1.1 The problem Docker solves

Software doesn't run in a vacuum. An application depends on a specific language runtime, libraries of specific versions, system packages, environment variables, file paths, and OS-level configuration. Historically, getting all of that lined up identically on a developer's laptop, a CI server, and a production machine was painful. The result was the infamous **"but it works on my machine"** problem: code that runs in one environment fails in another because something in the surrounding environment differs.

The traditional fixes were imperfect:

- **Documentation / setup scripts** drift out of date and depend on the starting state of the machine.
- **Configuration management** (Ansible, Chef, Puppet) configures *existing* machines but doesn't truly package the app.
- **Virtual machines** isolate completely but are heavy (each VM ships a full guest OS, takes gigabytes, and boots in tens of seconds to minutes).

**Containers** solve this by packaging the application *and everything it needs to run* into a single, portable, immutable artifact (an **image**) that behaves identically wherever a container runtime exists.

## 1.2 What is a container?

A container is an **isolated process** (or group of processes) running on a host, with its own private view of the filesystem, network, and process tree — but **sharing the host's operating system kernel**.

The key insight: a container is *not* a lightweight virtual machine. There is no guest OS, no hypervisor, no emulated hardware. A container is just a normal Linux process that the kernel has been told to isolate using two built-in kernel features:

- **Namespaces** — control what a process can *see* (its own PID tree, network interfaces, mount points, hostname, users). Different namespace = different view of the system.
- **cgroups (control groups)** — control what a process can *use* (CPU, memory, I/O, number of processes). This enforces resource limits and accounting.

A container also gets its own **filesystem**, assembled from read-only image layers plus a thin writable layer on top (covered in section 04).

So conceptually: **container = (isolated process) + (private filesystem from an image) + (resource limits)**.

## 1.3 Containers vs Virtual Machines

This comparison is the single most important mental model when starting out.

|                         | Virtual Machine                                  | Container                                       |
|-------------------------|--------------------------------------------------|-------------------------------------------------|
| **Isolation boundary**  | Hardware-level, via a hypervisor                 | Process-level, via kernel namespaces/cgroups    |
| **Guest OS**            | Full OS per VM (its own kernel)                  | None — shares the host kernel                    |
| **Size**                | Gigabytes                                        | Megabytes (often 10–200 MB)                      |
| **Startup time**        | Seconds to minutes                               | Milliseconds to seconds                          |
| **Density per host**    | Tens                                             | Hundreds to thousands                            |
| **Overhead**            | High (full OS + virtualized hardware)            | Near-native                                      |
| **Isolation strength**  | Very strong (separate kernels)                   | Strong but weaker (shared kernel = bigger attack surface) |
| **Portability**         | Portable but bulky                               | Highly portable, small                           |

A useful picture:

```
   VIRTUAL MACHINES                        CONTAINERS
 ┌──────┐ ┌──────┐ ┌──────┐            ┌──────┐ ┌──────┐ ┌──────┐
 │ App  │ │ App  │ │ App  │            │ App  │ │ App  │ │ App  │
 │ Bins │ │ Bins │ │ Bins │            │ Bins │ │ Bins │ │ Bins │
 │GuestOS││GuestOS││GuestOS│           └──────┘ └──────┘ └──────┘
 └──────┘ └──────┘ └──────┘            ┌────────────────────────┐
 ┌────────────────────────┐           │   Container runtime     │
 │       Hypervisor        │           ├────────────────────────┤
 ├────────────────────────┤           │      Host OS kernel      │
 │      Host OS / HW       │           ├────────────────────────┤
 └────────────────────────┘           │       Hardware           │
                                       └────────────────────────┘
```

Note the missing "Guest OS" boxes on the container side — that's the entire difference, and the source of every advantage and every limitation.

**Practical consequence of the shared kernel:** Linux containers need a Linux kernel. On macOS and Windows, Docker Desktop quietly runs a lightweight Linux VM and runs your containers *inside it*. So even "Docker on a Mac" is ultimately Linux containers on a Linux kernel — just hidden from you.

## 1.4 Why containers became dominant

- **Consistency:** the same image runs identically across dev, test, staging, and prod.
- **Speed:** start in milliseconds, enabling fast scaling and quick iteration.
- **Density & cost:** pack far more workloads per machine than VMs.
- **Immutability:** images don't change once built; you ship a new image instead of patching a running server. This makes deployments reproducible and rollbacks trivial.
- **Microservices fit:** small, independently deployable services map naturally onto small, independent containers.
- **Ecosystem:** a huge library of ready-made images (databases, web servers, language runtimes) on registries means you rarely start from scratch.

## 1.5 Docker vs containers vs the OCI

It's worth separating the *concept* from the *product*:

- **Containers** are a general Linux capability (namespaces + cgroups existed before Docker).
- **Docker** is the product/toolchain that made containers easy: a friendly CLI, an image format, a build system (Dockerfile), and a registry (Docker Hub). Docker popularized containers; it didn't invent the underlying kernel features.
- The **OCI (Open Container Initiative)** is the vendor-neutral standards body that now defines:
  - the **OCI Image Specification** (how images are structured),
  - the **OCI Runtime Specification** (how a runtime should run a container),
  - the **OCI Distribution Specification** (how registries serve images).

Because of OCI, Docker images are interchangeable with other tools (Podman, containerd, Kubernetes, etc.). An image you build with Docker can run on a Kubernetes cluster that has never heard of Docker. This standardization is why learning Docker transfers directly to the broader cloud-native world.

## 1.6 A brief history (just enough context)

- **2013** — Docker released as open source; containers go mainstream.
- **2015** — OCI founded to standardize image and runtime formats.
- **2017** — Docker donates **containerd** (its core runtime) to the CNCF; the engine is split into modular components.
- **2017+** — **Kubernetes** wins the orchestration war; Docker Swarm remains for simpler use cases.
- **2020–2022** — Docker Desktop becomes a paid product for larger companies; **Compose V2** (the `docker compose` plugin) replaces the old Python `docker-compose`.
- **2021+** — **BuildKit** becomes the default image builder, bringing faster, parallel, cache-efficient builds.
- **2024–2026** — Tooling matures: **Docker Scout** for supply-chain/vulnerability analysis, the **containerd image store** becoming the default in Docker Engine v29, and growing emphasis on rootless operation and image provenance.

## 1.7 The mental model to carry forward

When you read the rest of these notes, keep this loop in your head — it's the spine of everything Docker does:

```
   Dockerfile  ──build──►  Image  ──push──►  Registry  ──pull──►  Image  ──run──►  Container
   (recipe)                (artifact)       (warehouse)                            (running process)
```

- You **write** a Dockerfile (the recipe).
- You **build** it into an image (the immutable artifact).
- You **push** the image to a registry (shared storage).
- Elsewhere, you **pull** the image and **run** it as a container (the live process).

Every Docker subcommand you'll learn fits somewhere on this line.

---
**Next:** [02 — Installation & Setup](02-installation-and-setup.md)
