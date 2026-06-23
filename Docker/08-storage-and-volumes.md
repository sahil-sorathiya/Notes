# 08 — Storage & Volumes

## 8.1 The core problem: containers are ephemeral

Recall from section 04: a container's writable layer is destroyed when the container is removed. So **any data written inside the container is lost on `docker rm`.** That's fine for stateless apps, but databases, uploads, and logs need to **outlive** the container. Docker solves this with three persistence mechanisms.

## 8.2 The three mount types

| Type | Where data lives | Managed by Docker? | Best for |
|------|------------------|--------------------|----------|
| **Volume** | `/var/lib/docker/volumes/...` (Docker-managed area) | Yes | **Persistent app data** (databases, uploads) — the default choice |
| **Bind mount** | Any path on the host you choose | No (you point at a host path) | **Development** (live-editing source), mounting host config |
| **tmpfs mount** | Host RAM only (never written to disk) | Yes (Linux only) | Sensitive/temporary data (secrets, scratch space) |

```
        CONTAINER
   ┌──────────────────┐
   │  /app   (volume) ─┼────► Docker-managed volume   (survives container removal)
   │  /code  (bind)  ──┼────► /home/me/project on host (your real files)
   │  /tmp   (tmpfs) ──┼────► RAM                       (vanishes, never on disk)
   └──────────────────┘
```

## 8.3 Volumes (the recommended mechanism)

Volumes are storage objects fully managed by Docker. They decouple data lifetime from container lifetime, can be shared between containers, are portable across hosts (with drivers), and don't depend on the host's directory structure.

```bash
docker volume create app-data
docker volume ls
docker volume inspect app-data        # see mountpoint, driver, labels
docker volume rm app-data
docker volume prune                   # remove all unused volumes (careful!)
```

Using a volume with a container — two syntaxes:

```bash
# -v / --volume   (short syntax: SOURCE:TARGET[:OPTIONS])
docker run -d -v app-data:/var/lib/app myapp

# --mount   (explicit, verbose, preferred for clarity & in scripts)
docker run -d \
  --mount type=volume,source=app-data,target=/var/lib/app \
  myapp
```

**Named vs anonymous volumes:**
- **Named** (`app-data:/path`) — you give it a name; easy to find, reuse, back up.
- **Anonymous** (`-v /path` with no name, or via `VOLUME` in a Dockerfile) — Docker assigns a random ID. These pile up invisibly and are a common source of disk bloat. Prefer named volumes.

**Pre-populating a volume:** when you mount an *empty* volume onto a path that already has files in the image, Docker copies the image's files into the volume on first use. (Bind mounts do **not** do this — they hide whatever was at the target.)

## 8.4 Bind mounts

A bind mount maps a specific **host path** straight into the container. The container sees the host's actual files; changes flow both ways instantly.

```bash
docker run -d \
  -v "$(pwd)":/app \                       # short syntax
  myapp

docker run -d \
  --mount type=bind,source="$(pwd)",target=/app,readonly \
  myapp
```

Best for **development**: edit code on your host, the container sees changes immediately (combine with a hot-reloading dev server). Also used to inject host config files (e.g. an `nginx.conf`).

Caveats:
- The mount **shadows** anything already at the target path in the image.
- **Permissions/UID mismatches** between host and container users are a frequent pain point.
- **Performance** on macOS/Windows was historically poor because files cross the VM boundary; modern Docker Desktop mitigates this (VirtioFS), but bind mounts are still slower there than native volumes.
- Tying containers to host paths hurts portability — avoid bind mounts for production data.

## 8.5 tmpfs mounts

An in-memory filesystem; data never hits disk and disappears when the container stops. Good for secrets you don't want persisted, or high-churn scratch files. Linux only.

```bash
docker run -d --tmpfs /app/cache:size=64m myapp
docker run -d --mount type=tmpfs,target=/app/cache,tmpfs-size=64m myapp
```

## 8.6 Read-only mounts and read-only containers

Defense-in-depth: run the whole root filesystem read-only and only allow writes where explicitly needed.

```bash
docker run -d \
  --read-only \                       # entire container FS is read-only
  --tmpfs /tmp \                       # except an in-memory /tmp
  -v app-data:/var/lib/app \           # and a writable data volume
  myapp
```

This blocks a class of attacks and catches apps that write where they shouldn't.

## 8.7 Sharing data between containers

Multiple containers can mount the same named volume to share files:

```bash
docker run -d -v shared:/data --name writer myapp
docker run -d -v shared:/data --name reader otherapp
```

You can also reuse another container's mounts with `--volumes-from <container>` (less common now; explicit named volumes are clearer).

## 8.8 Backing up and restoring volumes

Volumes don't have a one-command backup, but the standard idiom is a throwaway container that tars the volume to a host file:

```bash
# Backup volume "app-data" to ./backup.tar.gz
docker run --rm \
  -v app-data:/data:ro \
  -v "$(pwd)":/backup \
  alpine tar czf /backup/backup.tar.gz -C /data .

# Restore into a (new) volume
docker run --rm \
  -v app-data:/data \
  -v "$(pwd)":/backup \
  alpine sh -c "cd /data && tar xzf /backup/backup.tar.gz"
```

The pattern: mount the volume + a host directory into a tiny container, and copy between them.

## 8.9 Volume drivers and remote/network storage

By default volumes use the `local` driver (data on the local host). **Volume plugins** let volumes live on network/cloud storage (NFS, cloud block storage, distributed filesystems), so data follows a container across hosts in a cluster.

```bash
# Example: a local-driver volume backed by an NFS export
docker volume create --driver local \
  --opt type=nfs \
  --opt o=addr=10.0.0.5,rw \
  --opt device=:/exports/data \
  nfs-data
```

In clustered/orchestrated setups, networked volume drivers (or the orchestrator's own storage abstraction) are what allow a stateful container to be rescheduled onto another node without losing its data.

## 8.10 Storage drivers vs volumes (don't confuse them)

Two different "storage" topics:

- **Storage driver / image store** (section 03.4 — `overlay2` or the containerd image store) manages the **image layers and the container's writable layer**. This is about *how images are stored on disk*.
- **Volumes/bind mounts** (this section) manage **persistent application data** that lives *outside* the writable layer.

They operate at different levels. You rarely touch the storage driver; you touch volumes constantly.

## 8.11 Disk usage and cleanup

Volumes are a top cause of mysterious disk consumption (especially anonymous ones from databases recreated many times).

```bash
docker system df                 # overview: images, containers, volumes, build cache
docker system df -v              # detailed, per-object
docker volume ls -f dangling=true   # volumes not attached to any container
docker volume prune              # remove unused (anonymous + unattached named) volumes
docker system prune --volumes    # broad cleanup INCLUDING volumes — use with care
```

> ⚠️ `docker volume prune` and `system prune --volumes` can **delete database data** if those volumes aren't currently attached to a running container. Always check `docker volume ls` and know what you're removing before pruning volumes.

## 8.12 Practical guidance

- **Production stateful data → named volumes** (or a network volume driver in a cluster). Never rely on the writable layer.
- **Local development source code → bind mounts** for live editing.
- **Secrets/scratch → tmpfs.**
- **Name your volumes**; avoid anonymous ones.
- **Back up** important volumes on a schedule (the tar idiom or your storage provider's snapshots).
- For databases specifically, prefer a **dedicated managed database** or a volume with a tested backup/restore process — losing the volume means losing the data.

---
**Previous:** [07 — Building & Optimizing Images](07-building-and-optimizing-images.md) | **Next:** [09 — Networking](09-networking.md)
