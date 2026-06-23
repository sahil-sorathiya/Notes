# 15 — Best Practices & Production

A consolidated, opinionated checklist. Many points reference earlier sections; this is the "what good looks like" summary you can return to.

## 15.1 Image & Dockerfile best practices

- **Use small, trusted base images.** Prefer `-slim`, distroless, or `scratch`; prefer Official/Verified images or your own. Smaller = faster, fewer CVEs (04.8).
- **Use multi-stage builds** to keep build tools out of the runtime image (07.5). This is the single biggest quality win.
- **Order instructions for cache efficiency.** Stable steps first; copy dependency manifests and install deps *before* copying source (06.3, 07.3).
- **One `RUN` for related steps, clean up in the same layer.** Deleting in a later layer doesn't shrink the image (04.2, 07.6).
- **Pin versions** of base images and packages; deploy by **digest** for reproducibility (04.3, 07.10).
- **Use `.dockerignore`** to shrink context and avoid leaking secrets/junk via `COPY .` (07.8).
- **Prefer exec form** for `CMD`/`ENTRYPOINT` so signals reach your app (06.4).
- **Run as non-root** (`USER`) (13.2).
- **Add a meaningful `HEALTHCHECK`** (06.2, 14.7).
- **Add OCI labels** (`org.opencontainers.image.source`, version) for traceability (06.2).
- **Never put secrets in images**; use BuildKit secrets at build time (07.7, 13.10).
- **One concern per image.** A container should run one primary process; don't cram a web server + cron + database into one image. Compose/orchestration wires multiple single-purpose containers together.

## 15.2 The "one process per container" principle

Containers are designed around a single main process (PID 1). Benefits of honoring this:

- **Independent scaling** — scale the web tier without scaling the DB.
- **Clear lifecycle & logging** — one process means clean stdout logs and predictable signals.
- **Smaller images, isolated failures.**

If you truly need multiple processes (rare), use a proper init/supervisor and understand the trade-offs — but the default answer is "split into multiple containers."

## 15.3 Statelessness & the 12-factor mindset

Design containers to be **disposable and stateless** wherever possible:

- **No important state in the writable layer** — externalize it to volumes or managed services (08).
- **Config via environment** (env vars / mounted config), not baked into the image — the *same image* runs in dev/staging/prod with different config (10.8).
- **Treat containers as cattle, not pets** — you should be able to kill and recreate any container without ceremony. This is what makes scaling, rolling updates, and self-healing possible.
- **Logs to stdout/stderr** (14.1).
- **Graceful shutdown** — handle SIGTERM, finish in-flight work, then exit (05.4, 06.5).

## 15.4 Runtime best practices

- **Set resource limits** (`--memory`, `--cpus`, `--pids-limit`) on every production container (5.7, 13.8).
- **Choose a restart policy** (`unless-stopped` on single hosts; let the orchestrator handle it in clusters) (5.6).
- **Configure log rotation** (`max-size`/`max-file`) so logs can't fill the disk (14.3).
- **Harden the runtime:** non-root, `--cap-drop ALL`, `--read-only` + `--tmpfs`, `no-new-privileges`, keep seccomp/AppArmor on (13.6–13.7).
- **Publish only necessary ports**, bind sensitive ones to `127.0.0.1`, segment networks (9.4, 13.11).
- **Use named volumes** for data you care about; back them up (8.3, 8.8).

## 15.5 CI/CD best practices

- **Build once, promote the same artifact.** Build the image in CI, tag with the **Git SHA**, push, then deploy that exact digest through environments — don't rebuild per environment (7.10, 11.10).
- **Scan images for vulnerabilities** in the pipeline and fail on criticals (`docker scout`, Trivy, Grype) (13.9).
- **Sign images / attach SBOMs & provenance** for supply-chain assurance (7.12, 11.8, 13.13).
- **Use BuildKit cache** (registry-backed cache import/export) to speed up CI builds (7.2, 7.4).
- **Authenticate to registries** with short-lived tokens; use a **pull-through mirror** to avoid Docker Hub rate limits in CI (11.2, 11.6).
- **Run tests in containers** (`docker compose run --rm app test`) so CI matches local (10.3).

## 15.6 Production deployment best practices

- **Use an orchestrator** for anything multi-host or HA (Swarm for simple, Kubernetes for scale) (12).
- **Zero-downtime rolling updates** with healthchecks gating progression, plus automatic rollback on failure (12.6).
- **Externalize secrets** to a secret store / orchestrator secrets, never the image (13.10).
- **Monitor** with metrics (Prometheus/Grafana/cAdvisor) and **centralize logs** (Loki/EFK/cloud) (14.4–14.5).
- **Health + readiness checks** so the platform only routes traffic to ready instances (14.7).
- **Patch regularly** — rebuild images to absorb base-image and dependency security fixes; keep host kernel and Docker Engine updated (13.9, 13.12).
- **Plan for state** — prefer managed databases or cluster-aware storage; have tested backups/restores (8.8, 12.10).

## 15.7 Common anti-patterns (and the fix)

| Anti-pattern | Why it's bad | Do instead |
|---|---|---|
| Deploying `:latest` | Ambiguous, mutable, non-reproducible | Pin a version/SHA, deploy by digest |
| Running as root | Escalation risk on escape | `USER` non-root |
| `docker commit` to build prod images | Opaque, non-reproducible | Write a Dockerfile |
| Secrets via `ENV`/`ARG`/`COPY` | Leak into image history | BuildKit secrets + runtime secret store |
| State in the writable layer | Lost on `rm`; blocks scaling | Volumes / managed services |
| `apt install` then `rm` in separate `RUN`s | Doesn't shrink the image | Combine into one `RUN` |
| `COPY . .` before installing deps | Busts dependency cache on every code change | Copy manifests first, install, then copy source |
| No resource limits | One container can take down the host | Set `--memory`/`--cpus`/`--pids-limit` |
| No log rotation | Disk fills, host dies | `max-size`/`max-file` |
| Mounting `docker.sock` into untrusted containers | Root-equivalent host access | Avoid; use a controlled build API |
| `--privileged` "to make it work" | Disables isolation | Grant the specific `--device`/cap needed |
| Shell-form `CMD` | PID 1 = shell, signals dropped, no graceful stop | Exec form |
| Multiple unrelated processes per container | Hard to scale/log/observe | One process per container |
| Huge build context (no `.dockerignore`) | Slow builds, cache busting, secret leaks | Add `.dockerignore` |

## 15.8 A "production-ready container" definition

Pulling it all together, a container is production-ready when:

1. Built from a **minimal, pinned base** via a **multi-stage** Dockerfile.
2. Runs **one process** as a **non-root user** with **dropped capabilities** and a **read-only** root FS.
3. **Stateless**, with state in **volumes/managed services** and config via **environment**.
4. Emits logs to **stdout/stderr**, with **rotation** configured.
5. Exposes a **healthcheck**; handles **SIGTERM** gracefully.
6. Has **resource limits** and a sensible **restart policy** (or orchestrator management).
7. **Scanned**, **signed**, **digest-pinned**, with an **SBOM** — and a CI pipeline that **builds once and promotes**.

If you can tick those, you've internalized essentially everything in these notes.

---
**Previous:** [14 — Logging, Monitoring & Debugging](14-logging-monitoring-debugging.md) | **Next:** [16 — CLI Cheat Sheet](16-cli-cheat-sheet.md)
