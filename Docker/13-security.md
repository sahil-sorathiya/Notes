# 13 — Docker Security

Containers are convenient but **not magically secure**. Because they share the host kernel, a container compromise can be more dangerous than a VM compromise. This section covers the threat model and concrete hardening steps.

## 13.1 The threat model in one picture

The big risk is **container escape**: code inside a container breaking out to affect the host or other containers. Since all containers share the **host kernel**, a kernel exploit or a misconfiguration (excess privileges, mounted host paths, the Docker socket) can lead to full host compromise. Security work is about shrinking what a container *can do* and what an attacker *gains* if they break in.

Three layers to defend:
1. **The image** — what's inside (vulnerable packages, secrets, bloat).
2. **The runtime** — what the container is allowed to do (privileges, capabilities, mounts).
3. **The host & daemon** — the daemon runs as root and controls everything.

## 13.2 Don't run as root inside the container

By default, container processes run as **root** (UID 0). If an attacker escapes, they're root on the host (unless user namespaces remap it). **Run as a non-root user.**

```dockerfile
RUN useradd -r -u 1001 appuser
USER appuser
```
or at runtime: `docker run -u 1001:1001 myapp`.

Many official images already provide a non-root user (e.g. the `node` user in Node images). Use it.

## 13.3 Linux capabilities — drop what you don't need

Linux splits root's powers into ~40 **capabilities** (e.g. `NET_BIND_SERVICE`, `SYS_ADMIN`, `CHOWN`). Docker already **drops most** by default, but you should drop *all* and add back only what's required:

```bash
docker run --cap-drop ALL --cap-add NET_BIND_SERVICE myapp
```

Never add `SYS_ADMIN` or similar broad capabilities unless absolutely necessary — they're close to full root.

## 13.4 Never use `--privileged` casually

`--privileged` disables almost all isolation: it grants all capabilities, access to all host devices, and removes seccomp/AppArmor restrictions. It's effectively root on the host. Avoid it. If a container needs one device, mount just that device (`--device /dev/foo`) instead of going privileged.

## 13.5 Protect the Docker socket

`/var/run/docker.sock` is the daemon's control channel. **Anything that can talk to it can fully control the host** (it can launch a container that mounts `/` and chroots into it). Therefore:

- **Don't** mount `docker.sock` into containers unless you fully trust them and have no alternative (this is a common CI/"Docker-in-Docker" pattern that is genuinely dangerous).
- **Don't** expose the daemon on an unauthenticated TCP port.
- Remember the **`docker` group is root-equivalent** (section 02.3) — guard membership like sudo.

## 13.6 Read-only filesystem and no new privileges

Reduce what a compromised process can do:

```bash
docker run \
  --read-only \
  --tmpfs /tmp \
  --security-opt no-new-privileges \   # process can't gain privileges via setuid binaries
  --cap-drop ALL \
  -u 1001 \
  myapp
```

`no-new-privileges` blocks setuid escalation; `--read-only` prevents tampering with the filesystem; dropping caps and running non-root shrink the attack surface further. Stacking these is cheap and effective.

## 13.7 Seccomp, AppArmor, SELinux

- **Seccomp** filters which **syscalls** a container may make. Docker applies a sensible **default profile** that blocks dangerous syscalls. Don't disable it (`--security-opt seccomp=unconfined`) without good reason; you can supply a custom profile to tighten further.
- **AppArmor** (Debian/Ubuntu) and **SELinux** (RHEL/Fedora) provide **mandatory access control** — an extra policy layer constraining what processes can touch, independent of file permissions. Keep them enabled; Docker ships a default AppArmor profile.

These are "defense in depth": even if the app is compromised, these layers limit the damage.

## 13.8 Resource limits as a security control

Unbounded containers enable **denial-of-service** (a single container starving the host of CPU/RAM/PIDs). Always set limits (section 5.7): `--memory`, `--cpus`, `--pids-limit`. `--pids-limit` specifically thwarts fork bombs.

## 13.9 Image security

**Use trusted, minimal base images.**
- Prefer Official Images / Verified Publishers, or images you build yourself.
- Smaller bases (slim/distroless/scratch) = fewer packages = fewer vulnerabilities and a smaller attack surface (section 04.8).

**Keep images patched.** Vulnerabilities are discovered constantly. Rebuild regularly to pull in updated base images and dependencies; don't let images rot.

**Scan for vulnerabilities.** Use a scanner in your pipeline:

```bash
docker scout cves myapp:1.0        # known CVEs in the image
docker scout quickview myapp:1.0   # summary + base-image recommendations
docker scout recommendations myapp:1.0
```

Other scanners: **Trivy**, **Grype**, **Clair**, and registry-integrated scanning (Harbor, ECR). Gate deployments on scan results for critical CVEs.

**Pin versions and prefer digests** (section 04.3 / 11.7) so you know exactly what you're shipping, and an upstream tag can't silently change under you.

## 13.10 Secrets management — never bake secrets into images

This is one of the most common real-world mistakes.

**Don't:**
- `ENV API_KEY=...` or `ARG TOKEN=...` in a Dockerfile (visible in image history).
- `COPY` private keys or `.env` files into the image.
- Hardcode credentials in the image at all.

**Do:**
- **Build-time secrets:** use BuildKit `--mount=type=secret` (section 07.7) so secrets aren't stored in layers.
- **Runtime secrets:** inject via environment variables from an orchestrator/secret store, or mount files. In **Swarm** use `docker secret` (mounted at `/run/secrets/...`, encrypted at rest — section 12.9); in **Kubernetes** use Secrets; in cloud, use the provider's secrets manager (AWS Secrets Manager, Vault, etc.).
- Add secret files to `.dockerignore` and `.gitignore`.

Audit existing images for leaked secrets with tools like `docker history`, and secret scanners.

## 13.11 Network security

- **Don't publish ports you don't need.** Containers talk to each other over the internal network without publishing (section 09.3).
- **Bind sensitive published ports to `127.0.0.1`**, not `0.0.0.0` — recall the UFW-bypass gotcha (section 09.4): a published port can be internet-exposed even when your host firewall seems to block it.
- **Segment networks**: put your database on a backend network not shared with public-facing services.
- Use **TLS** for registry connections and any service-to-service traffic that leaves the host.

## 13.12 Daemon and host hardening

- Run **rootless mode** where feasible (section 2.6) so the daemon and containers aren't root.
- Enable **user namespace remapping** (`userns-remap` in `daemon.json`) so container-root maps to an unprivileged host user.
- Keep the **host kernel and Docker Engine patched** (kernel CVEs are container-escape vectors; the Engine itself periodically ships security fixes — e.g. several `docker cp` and BuildKit CVEs were patched in recent v29 releases).
- Restrict who can reach the daemon socket / who is in the `docker` group.
- Turn on **live audit logging**/monitoring of the daemon where compliance requires it.

## 13.13 Supply-chain security (modern emphasis)

The ecosystem now treats images as auditable software artifacts:

- **SBOMs** — generate and inspect a bill of materials (`docker scout sbom`) so you know every component in an image (section 07.12). (The old `docker sbom` command is deprecated in favor of `docker scout sbom`.)
- **Provenance attestations** — signed records of how an image was built (buildx).
- **Image signing & verification** — Sigstore/**Cosign** (or historically Docker Content Trust, `DOCKER_CONTENT_TRUST=1`) to ensure images come from a trusted source and weren't tampered with (section 11.8).
- **Verify before run** — enforce that only signed, scanned images can be deployed (admission policies in K8s; registry policies in Harbor).

## 13.14 A practical hardening checklist

- [ ] Use a **minimal, trusted base image**; pin by version/digest.
- [ ] **Scan** images for CVEs in CI; fail on criticals.
- [ ] Run as a **non-root user** (`USER`).
- [ ] **Drop all capabilities**, add back only what's needed.
- [ ] **Never** `--privileged`; **never** mount `docker.sock` into untrusted containers.
- [ ] Run with `--read-only`, `--tmpfs` for writable paths, and `--security-opt no-new-privileges`.
- [ ] Keep **seccomp/AppArmor/SELinux** enabled (don't run `unconfined`).
- [ ] Set **resource limits** (`--memory`, `--cpus`, `--pids-limit`).
- [ ] Keep **secrets out of images**; use build secrets + a runtime secret store.
- [ ] **Publish only necessary ports**; bind sensitive ones to localhost; segment networks.
- [ ] Patch **host kernel + Docker Engine**; prefer **rootless** / userns-remap.
- [ ] Add **SBOMs, signing, provenance** for supply-chain assurance.

---
**Previous:** [12 — Orchestration & Swarm](12-orchestration-and-swarm.md) | **Next:** [14 — Logging, Monitoring & Debugging](14-logging-monitoring-debugging.md)
