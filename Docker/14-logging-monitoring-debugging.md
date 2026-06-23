# 14 — Logging, Monitoring & Debugging

## 14.1 The logging philosophy: log to stdout/stderr

The container-native rule: **your application should write logs to standard output and standard error**, not to files inside the container. Docker captures the standard streams via a **logging driver**, and you read them with `docker logs`. Files written inside the container live in the ephemeral writable layer and vanish when the container is removed (section 04.2) — so file-based logging inside a container is an anti-pattern.

```bash
docker logs <container>            # all captured logs
docker logs -f <container>         # follow (stream)
docker logs --tail 200 <container> # last 200 lines
docker logs --since 15m <container># last 15 minutes
docker logs -t <container>         # with timestamps
```

(For Compose: `docker compose logs -f [service]`; for Swarm: `docker service logs <service>`.)

## 14.2 Logging drivers

The **logging driver** determines where captured stdout/stderr goes. The default is `json-file` (stored under `/var/lib/docker/containers/<id>/`).

| Driver | Destination |
|--------|-------------|
| `json-file` | Local JSON files (default). Readable by `docker logs`. |
| `local` | Local, more efficient binary format with built-in rotation. |
| `journald` | systemd journal (`journalctl`). |
| `syslog` | A syslog server. |
| `fluentd` | Fluentd/Fluent Bit collector (then on to Elasticsearch, Loki, etc.). |
| `gelf` | Graylog Extended Log Format. |
| `awslogs`, `gcplogs`, `splunk` | Cloud/SaaS log services. |
| `none` | Disable logging for the container. |

Set globally in `/etc/docker/daemon.json`, or per container with `--log-driver`/`--log-opt`.

> **Note:** with some remote drivers, `docker logs` won't work because the logs aren't stored locally — they've been shipped elsewhere.

## 14.3 Log rotation — the disk-fills-up trap

With the default `json-file` driver and **no limits**, container logs grow without bound and can **fill the host disk** — a classic production incident. Always cap them:

```json
// /etc/docker/daemon.json  (applies to all new containers)
{
  "log-driver": "json-file",
  "log-opts": { "max-size": "10m", "max-file": "3" }
}
```

or per container:

```bash
docker run --log-opt max-size=10m --log-opt max-file=3 myapp
```

This keeps at most 3 files of 10 MB each (30 MB) per container, rotating old logs out. The `local` driver does rotation by default.

## 14.4 Centralized logging (beyond one host)

On a single dev machine `docker logs` is enough. In production with many containers across many hosts, you ship logs to a central system:

- **EFK / ELK** — Fluentd/Fluent Bit (or Logstash) → Elasticsearch → Kibana.
- **Loki + Grafana** — lightweight, label-based log aggregation.
- **Cloud** — CloudWatch Logs, Google Cloud Logging, Datadog, Splunk.

A common pattern: containers log to stdout → the `fluentd`/`gelf` driver (or a node-level agent like Fluent Bit, often run as a **global**/DaemonSet service) → central store → dashboards and alerts.

## 14.5 Monitoring container metrics

Quick, built-in metrics:

```bash
docker stats                  # live CPU%, mem usage/limit, net I/O, block I/O, PIDs (all containers)
docker stats web db           # specific containers
docker stats --no-stream      # one snapshot (good for scripts)
```

For real monitoring stacks:

- **cAdvisor** — exposes per-container resource metrics.
- **Prometheus** — scrapes metrics (from cAdvisor, node-exporter, your app) and stores time-series.
- **Grafana** — dashboards and alerting on top of Prometheus.

The daemon itself can also expose metrics in Prometheus format (enabled via `daemon.json`). A typical setup: cAdvisor + node-exporter + Prometheus + Grafana gives you per-container and per-host visibility.

## 14.6 The events stream

The daemon emits a real-time stream of everything happening (containers starting/stopping/dying, images pulled, volumes created, etc.):

```bash
docker events                                   # live stream
docker events --since 1h --filter 'event=die'   # recent container deaths
docker events --filter 'container=web'
```

Great for understanding *why* a container restarted or when something changed.

## 14.7 Healthchecks (operational signal, not just metadata)

A `HEALTHCHECK` (defined in the Dockerfile, section 06.2, or in Compose, section 10.13) makes the container report `healthy`/`unhealthy`. This:

- Shows in `docker ps` (the `STATUS` column).
- Gates Compose `depends_on: condition: service_healthy`.
- Lets Swarm/K8s **replace** unhealthy instances automatically.

```bash
docker ps                                         # see (healthy)/(unhealthy)
docker inspect --format '{{json .State.Health}}' web   # full health history
```

Design healthchecks to test *real readiness* (can it serve a request / reach its DB?), not just "is the process alive."

## 14.8 Debugging a misbehaving container (a workflow)

Combine the tools above into a systematic approach:

1. **State & exit code:** `docker ps -a` — running or exited? Note the exit code.
   - `137` ≈ SIGKILL, often **OOM** (check `docker inspect --format '{{.State.OOMKilled}}' <c>`).
   - `143` ≈ SIGTERM; `127` ≈ command not found; `126` ≈ not executable; `125` ≈ `docker run` itself failed.
2. **Logs:** `docker logs <c>` — what did it print before dying?
3. **Inspect:** `docker inspect <c>` — verify the actual entrypoint/cmd, env vars, mounts, network, restart count, and `State`.
4. **Get inside (if running):** `docker exec -it <c> sh` — check files, env (`env`), connectivity (`nc -zv db 5432`, `getent hosts db`).
5. **If it crashes instantly:** override the entrypoint to get a shell and poke around:
   `docker run -it --entrypoint sh myimage`.
6. **Resource issues:** `docker stats <c>` for live CPU/mem; correlate with OOM kills.
7. **Networking issues:** see the section 09.9 checklist (same network? right name? app bound to `0.0.0.0`?).
8. **Filesystem drift:** `docker diff <c>` shows what changed vs the image.
9. **Distroless/scratch images** (no shell): use `docker debug` (Docker Desktop feature) or attach a debugging sidecar container sharing the target's namespaces.

## 14.9 Inspecting build problems

```bash
docker build --progress=plain --no-cache .   # full, uncached build output
docker history <image>                        # which layer added what / sizes
docker build --target <stage> -t debug .      # stop at a stage and inspect it
docker run -it --rm debug sh                  # explore the intermediate image
```

Building only up to a stage and running a shell in it is the fastest way to debug "the build succeeds but the artifact is wrong."

## 14.10 Cleaning up to keep a host healthy

Disk exhaustion from accumulated images, stopped containers, volumes, and build cache is the most common Docker host problem.

```bash
docker system df                  # what's using space
docker system df -v               # detailed breakdown
docker container prune            # remove stopped containers
docker image prune                # remove dangling images
docker image prune -a             # remove all unused images
docker builder prune              # clear build cache
docker volume prune               # remove unused volumes (⚠ data loss)
docker system prune               # containers + networks + dangling images + build cache
docker system prune -a --volumes  # aggressive: also unused images AND volumes (⚠⚠)
```

Schedule conservative pruning on long-lived hosts, but be deliberate about volumes — pruning them can destroy database data (section 08.11).

---
**Previous:** [13 — Security](13-security.md) | **Next:** [15 — Best Practices & Production](15-best-practices-and-production.md)
