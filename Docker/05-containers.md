# 05 — Docker Containers

A container is a running (or stopped) instance of an image. This section covers its full lifecycle and the commands and flags you'll use daily.

## 5.1 The container lifecycle

A container moves through a small set of states:

```
            docker create                docker start                docker pause
  (image) ───────────────► CREATED ──────────────────► RUNNING ──────────────────► PAUSED
                                │                          │  ▲                        │
                                │      docker start        │  │ docker unpause         │
                                └──────────────────────────┘  └────────────────────────┘
                                                           │
                                          docker stop / kill│ / process exits
                                                           ▼
                                                        EXITED ──── docker rm ────► (removed)
                                                           │
                                                 docker start (restart it)
```

- **Created** — container exists (filesystem + config ready) but no process is running.
- **Running** — the main process is executing.
- **Paused** — processes frozen via cgroup freezer (memory retained, CPU stopped).
- **Exited / Stopped** — the main process has ended (cleanly or via stop/kill). Its filesystem and logs persist until removed.
- **Removed** — gone; the writable layer is destroyed.

`docker run` is a convenience that does **`create` + `start`** in one step (and `pull` first if needed).

## 5.2 `docker run` — the workhorse, flag by flag

```bash
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

A realistic example exercising the common flags:

```bash
docker run \
  -d \                          # detached: run in background, print container ID
  --name web \                  # human-friendly name (else Docker invents one)
  -p 8080:80 \                  # publish host:container port
  -e NODE_ENV=production \      # set an environment variable
  --env-file ./app.env \        # ...or load many from a file
  -v app-data:/var/lib/app \    # mount a named volume for persistence
  --restart unless-stopped \    # restart policy
  --memory 512m --cpus 1.5 \    # resource limits
  --health-cmd 'curl -f http://localhost/ || exit 1' \  # healthcheck
  myorg/webapp:1.4.2 \          # the image
  --some-app-arg                # args passed to the container's process
```

The most important flags, grouped:

**Run mode**
- `-d, --detach` — run in background; without it the container runs in the foreground and your terminal is attached to its output.
- `-it` — `-i` keeps STDIN open, `-t` allocates a pseudo-TTY. Together they give you an interactive shell (`docker run -it ubuntu bash`).
- `--rm` — automatically remove the container when it exits (great for one-off/throwaway runs).

**Identity & metadata**
- `--name NAME` — name the container.
- `-l, --label key=value` — attach metadata labels.

**Networking** (details in section 09)
- `-p HOST:CONTAINER` — publish a port to the host.
- `-P` — publish *all* `EXPOSE`d ports to random high host ports.
- `--network NAME` — attach to a specific network.
- `--hostname`, `--dns`, `--add-host` — networking niceties.

**Environment & config**
- `-e KEY=VALUE`, `--env-file FILE` — environment variables.
- `-w, --workdir DIR` — working directory inside the container.
- `-u, --user UID[:GID]` — run as a specific (non-root) user.

**Storage** (details in section 08)
- `-v` / `--volume`, `--mount` — volumes and bind mounts.
- `--tmpfs PATH` — mount an in-memory filesystem.

**Lifecycle & resources**
- `--restart {no|on-failure[:N]|always|unless-stopped}` — restart policy (section 5.6).
- `--memory`, `--memory-swap`, `--cpus`, `--cpuset-cpus`, `--pids-limit` — resource limits (section 5.7).
- `--entrypoint` — override the image's entrypoint.

> **Foreground vs detached:** if you run without `-d`, pressing `Ctrl-C` sends SIGINT to the container's process and usually stops it. To detach from a foreground container *without* stopping it, use the escape sequence `Ctrl-P Ctrl-Q` (only works when started with `-it`).

## 5.3 Listing, inspecting, and finding containers

```bash
docker ps                       # running containers only
docker ps -a                    # all containers, including stopped/exited
docker ps -q                    # only IDs (handy for scripting)
docker ps --filter "status=exited" --filter "name=web"
docker ps --format '{{.Names}}\t{{.Status}}\t{{.Ports}}'

docker inspect web              # full JSON: config, mounts, network, state
docker inspect --format '{{.State.Status}}' web
docker inspect --format '{{.NetworkSettings.IPAddress}}' web

docker top web                  # processes running inside the container
docker port web                 # show port mappings
```

`docker inspect` with `--format` (Go templates) is the precise way to extract a single field for scripts.

## 5.4 Controlling running containers

```bash
docker stop web        # graceful: SIGTERM, then SIGKILL after a grace period (default 10s)
docker stop -t 30 web  # give it 30s before the kill
docker kill web        # immediate SIGKILL (or --signal to send another signal)
docker restart web     # stop then start
docker pause web        # freeze all processes
docker unpause web      # resume
docker rename web api   # rename
docker rm web          # remove a stopped container
docker rm -f web       # force-remove a running container (stop + remove)
docker update --memory 1g web   # change some limits on a running container
```

**SIGTERM vs SIGKILL** matters for graceful shutdown: `stop` lets your app finish in-flight work and clean up; `kill` yanks the plug. Your container's PID 1 should handle SIGTERM (section 06 on `ENTRYPOINT` and signal handling).

## 5.5 Executing commands and getting a shell

```bash
# Run a new process inside an already-running container
docker exec -it web bash          # interactive shell (if the image has bash)
docker exec -it web sh            # fallback for minimal/alpine images
docker exec web env               # run a one-off command, see its output
docker exec -u root -it web sh    # exec as root for debugging

# Attach to the container's MAIN process (PID 1) stdio — different from exec!
docker attach web                 # be careful: Ctrl-C here may kill the container
```

**`exec` vs `attach`:** `exec` starts a *new* process inside the container (the usual way to "get in"); `attach` connects your terminal to the *existing* PID 1's streams. For debugging you almost always want `exec`.

For images with no shell (distroless/scratch) you can't `exec` a shell at all — use a debugging sidecar or a `docker debug`-style tool, or temporarily build with a shell-containing base.

## 5.6 Restart policies

Control what the daemon does when a container exits or the host reboots:

| Policy | Behaviour |
|--------|-----------|
| `no` (default) | Never restart automatically |
| `on-failure[:N]` | Restart only on non-zero exit; optional max retry count `N` |
| `always` | Always restart; also starts on daemon/host boot |
| `unless-stopped` | Like `always`, but if you manually stopped it, it stays stopped across reboots |

`unless-stopped` is the most common choice for long-running services on a single host. (In Swarm/Kubernetes the orchestrator handles restarts instead — section 12.)

## 5.7 Resource limits (cgroups in practice)

Unlimited containers can starve the host. Constrain them:

```bash
# Memory: container is OOM-killed if it exceeds this hard limit
docker run --memory 512m --memory-swap 512m myapp   # 512m RAM, no swap

# CPU: fractional cores
docker run --cpus 1.5 myapp                          # up to 1.5 cores
docker run --cpuset-cpus 0,1 myapp                   # pin to cores 0 and 1
docker run --cpu-shares 512 myapp                    # relative weight under contention

# Process count (fork-bomb protection)
docker run --pids-limit 200 myapp

# Block I/O
docker run --device-read-bps /dev/sda:10mb myapp
```

Notes:
- `--memory-swap` equal to `--memory` disables swap for the container.
- `--cpus` is the friendly knob; under the hood it sets cgroup CPU quota/period.
- Setting limits is a **production must** — it's how you prevent one misbehaving container from taking down the whole host.

## 5.8 Logs

```bash
docker logs web              # dump all logs from PID 1's stdout/stderr
docker logs -f web           # follow (stream) live
docker logs --tail 100 web   # last 100 lines
docker logs --since 10m web  # last 10 minutes
docker logs -t web           # show timestamps
```

This reads the logs the **logging driver** captured (default `json-file`). The golden rule: your app should log to **stdout/stderr**, not to files inside the container. Docker captures the standard streams; files in the writable layer vanish with the container. Logging drivers and rotation are covered in section 14.

## 5.9 Copying files in and out

```bash
docker cp ./local.conf web:/etc/app/local.conf   # host → container
docker cp web:/var/log/app.log ./app.log         # container → host
```

Useful for grabbing artifacts or logs, but don't build workflows on it — for ongoing file exchange use volumes/bind mounts.

## 5.10 Inspecting changes and committing (use sparingly)

```bash
docker diff web        # show files added(A)/changed(C)/deleted(D) vs the image
docker commit web myimage:debug   # snapshot a container into a new image
```

`docker commit` exists but is an **anti-pattern** for building production images — it produces opaque, non-reproducible images. Use a Dockerfile (section 06) instead. `commit` is acceptable only for quick experiments or capturing a debugging state.

## 5.11 Common container debugging workflow

When a container won't behave, work through this:

1. `docker ps -a` — is it running, or exited? Note the **exit code**.
2. `docker logs <c>` — what did it print before dying?
3. `docker inspect <c>` — check `State` (OOMKilled? error?), mounts, env, the actual command/entrypoint, and network settings.
4. `docker exec -it <c> sh` — get inside (if it's still running) and poke around: is the file there? can it reach the database? what does `env` show?
5. If it exits instantly, override the entrypoint to get a shell: `docker run -it --entrypoint sh myimage`.
6. Check resources: an exit code of **137** typically means SIGKILL — often an **OOM kill** (look for `"OOMKilled": true` in inspect).

Common exit codes: `0` clean, `1` general app error, `125` Docker run failed (bad flag), `126` command not executable, `127` command not found, `137` SIGKILL/OOM, `143` SIGTERM.

---
**Previous:** [04 — Images](04-images.md) | **Next:** [06 — Dockerfile](06-dockerfile.md)
