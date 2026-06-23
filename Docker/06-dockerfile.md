# 06 — Dockerfile

A **Dockerfile** is a plain-text recipe that tells Docker how to build an image, step by step. Each instruction is documented here with its semantics and the gotchas that trip people up.

## 6.1 How a Dockerfile is processed

- The build runs in a context: you run `docker build -t name:tag .` and the `.` is the **build context** — the directory whose contents are sent to the builder. (Keep it small; see `.dockerignore` in section 07.)
- Instructions are read **top to bottom**. Most filesystem-changing instructions create a new **layer**.
- The base is set by `FROM`. Everything stacks on top of it.
- Order matters enormously for **build caching** (section 07): put rarely-changing steps early, frequently-changing steps (like copying source code) late.

A minimal example for a Node app:

```dockerfile
FROM node:20-slim
WORKDIR /app
COPY package*.json ./
RUN npm ci --omit=dev
COPY . .
EXPOSE 3000
USER node
CMD ["node", "server.js"]
```

## 6.2 The instructions, one by one

### `FROM`
Sets the base image. Must be the first real instruction (after optional `ARG`s and parser directives). You can have **multiple** `FROM`s for multi-stage builds (section 07.5).

```dockerfile
FROM python:3.12-slim
FROM golang:1.22 AS builder      # named stage
FROM scratch                     # empty base for static binaries
```

### `RUN`
Executes a command **at build time**, in a new layer, on top of the current image. Used to install packages, compile code, etc.

Two forms:
- **Shell form:** `RUN apt-get update && apt-get install -y curl` — runs via `/bin/sh -c`, so shell features (pipes, `&&`, variable expansion) work.
- **Exec form:** `RUN ["executable", "arg1", "arg2"]` — no shell, exact args.

Best practice: **chain related commands in one `RUN`** with `&&` and clean up in the same layer, so you don't leave behind cache files that bloat the image:

```dockerfile
RUN apt-get update && \
    apt-get install -y --no-install-recommends curl ca-certificates && \
    rm -rf /var/lib/apt/lists/*
```

### `CMD`
Sets the **default command** the container runs when started. There can be only one effective `CMD` (the last wins). It's easily **overridden** at `docker run` time.

Three forms:
- **Exec form (preferred):** `CMD ["node", "server.js"]` — no shell, proper signal handling.
- **Shell form:** `CMD node server.js` — wrapped in `/bin/sh -c`, which becomes PID 1 and can swallow signals.
- **As default args to ENTRYPOINT:** `CMD ["--port", "8080"]` (see below).

### `ENTRYPOINT`
Sets the executable that **always** runs; arguments from `CMD` or the `docker run` command line are appended to it. Use it when the container *is* a specific program.

```dockerfile
ENTRYPOINT ["python", "app.py"]
CMD ["--help"]            # default args, overridable
```

- `docker run myimg` → runs `python app.py --help`
- `docker run myimg --port 9000` → runs `python app.py --port 9000`

**ENTRYPOINT vs CMD — the mental model:**
- `ENTRYPOINT` = *what to run* (the fixed program). Hard to override (needs `--entrypoint`).
- `CMD` = *default arguments* (or the default command if no ENTRYPOINT). Easy to override (just pass a new command).
- Use **exec form** for both so signals (SIGTERM) reach your process directly. The shell form makes `/bin/sh` PID 1, which often doesn't forward signals, breaking graceful shutdown.

### `COPY`
Copies files/directories **from the build context** into the image.

```dockerfile
COPY package.json package-lock.json ./
COPY src/ /app/src/
COPY --chown=node:node . /app          # set ownership while copying
COPY --from=builder /app/dist /app/dist # copy from another build stage
```

### `ADD`
Like `COPY` but with two extra "magic" behaviours:
1. It can fetch from a **remote URL**.
2. It **auto-extracts** local tar archives into the destination.

Because that magic is surprising, the guidance is: **prefer `COPY`**. Use `ADD` only when you specifically want tar extraction. (For remote downloads, an explicit `RUN curl …` is clearer and cacheable.)

### `WORKDIR`
Sets the working directory for subsequent `RUN`, `CMD`, `ENTRYPOINT`, `COPY`, `ADD`. Creates the directory if it doesn't exist. Use it instead of `RUN cd …` (which doesn't persist across instructions).

```dockerfile
WORKDIR /app
```

### `ENV`
Sets environment variables that persist into the running container and are available to later build steps.

```dockerfile
ENV APP_HOME=/app \
    NODE_ENV=production
```

### `ARG`
Defines a **build-time** variable, optionally with a default, settable via `--build-arg`. Unlike `ENV`, `ARG` values are **not** present in the final running container (unless copied into an `ENV`).

```dockerfile
ARG VERSION=1.0.0
ARG NODE_VERSION=20
FROM node:${NODE_VERSION}-slim       # ARG can even parameterize FROM
```
```bash
docker build --build-arg VERSION=2.3.1 -t myapp .
```

> **Security:** never pass secrets via `ARG` — build args can be seen in image history. Use BuildKit **secrets** instead (section 07.7).

### `EXPOSE`
**Documents** which ports the container listens on. It does **not** publish them — it's purely informational/metadata (and what `-P` uses). You still need `-p` at run time to actually expose a port to the host.

```dockerfile
EXPOSE 8080
```

### `VOLUME`
Declares a mount point that should be backed by a volume. At run time, if nothing is mounted there, Docker creates an anonymous volume. Useful to signal "data here should persist," but be cautious: anonymous volumes accumulate and `VOLUME` can interfere with later `COPY`s into that path.

```dockerfile
VOLUME /var/lib/postgresql/data
```

### `USER`
Sets the user (and optionally group) for subsequent instructions and the running container. **Running as non-root is a key security practice** (section 13).

```dockerfile
RUN useradd -r -u 1001 appuser
USER appuser
# or: USER 1001
```

### `HEALTHCHECK`
Tells Docker how to test whether the container is healthy. Status shows in `docker ps` as `healthy`/`unhealthy` and can gate Compose dependencies.

```dockerfile
HEALTHCHECK --interval=30s --timeout=3s --start-period=10s --retries=3 \
  CMD curl -f http://localhost:8080/health || exit 1
HEALTHCHECK NONE        # disable a healthcheck inherited from the base image
```

### `LABEL`
Adds metadata key/value pairs (maintainer, version, source repo). The OCI standard defines conventional label keys like `org.opencontainers.image.source`.

```dockerfile
LABEL org.opencontainers.image.source="https://github.com/acme/app" \
      org.opencontainers.image.version="1.4.2"
```

### `ONBUILD`
Registers an instruction to run **when this image is used as a base** for another build. Niche; used by some "builder" base images. Can surprise downstream users, so use sparingly.

### `SHELL`
Changes the default shell used for shell-form `RUN`/`CMD` (e.g. to PowerShell on Windows, or `bash -o pipefail`).

```dockerfile
SHELL ["/bin/bash", "-o", "pipefail", "-c"]
```

### `STOPSIGNAL`
Sets which signal is sent to stop the container (default SIGTERM).

```dockerfile
STOPSIGNAL SIGQUIT
```

## 6.3 The build cache and instruction order

Docker caches each layer. On rebuild, it reuses a cached layer as long as the instruction **and its inputs** are unchanged. The moment one layer's inputs change, that layer and **every layer after it** are rebuilt.

This drives the single most important Dockerfile pattern — **copy dependency manifests and install dependencies before copying the rest of your source**:

```dockerfile
# GOOD: deps cached unless package files change
COPY package*.json ./
RUN npm ci
COPY . .            # source changes don't bust the npm cache

# BAD: any source change reruns npm install every time
COPY . .
RUN npm ci
```

More on caching, BuildKit cache mounts, and multi-stage builds is in section 07.

## 6.4 Shell form vs exec form (the recurring footgun)

```dockerfile
CMD npm start            # shell form → /bin/sh -c "npm start"; sh is PID 1
CMD ["npm", "start"]     # exec form → npm is PID 1, receives signals directly
```

Consequences of shell form:
- An extra `sh` process becomes PID 1 and may **not forward SIGTERM**, so `docker stop` times out and your app gets SIGKILL'd (no graceful shutdown).
- Variable expansion happens (sometimes wanted, sometimes surprising).

**Default to exec form** for `CMD` and `ENTRYPOINT`. If you genuinely need shell features in exec form, invoke a shell explicitly: `ENTRYPOINT ["sh", "-c", "exec myapp"]` (the `exec` replaces the shell so your app becomes PID 1).

## 6.5 PID 1, signals, and `--init`

In a container, your main process is **PID 1**, which has special kernel responsibilities: it must reap zombie child processes and handle signals. Many app runtimes don't do this well, leading to zombie buildup or unresponsive shutdown. Two fixes:

- Use a tiny init like **tini** (`docker run --init`, or bake it in) to be PID 1 and forward signals/reap children.
- Or ensure your app handles SIGTERM and you use exec form so it *is* PID 1 cleanly.

## 6.6 A well-structured production Dockerfile (annotated)

```dockerfile
# ---- Build stage ----
FROM node:20-slim AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci                       # full deps incl. dev, for building
COPY . .
RUN npm run build                # produces /app/dist

# ---- Runtime stage ----
FROM node:20-slim AS runtime
ENV NODE_ENV=production
WORKDIR /app
COPY package*.json ./
RUN npm ci --omit=dev && npm cache clean --force
COPY --from=build /app/dist ./dist     # only the built output, not the toolchain
EXPOSE 3000
USER node                        # drop root
HEALTHCHECK --interval=30s --timeout=3s \
  CMD node -e "require('http').get('http://localhost:3000/health',r=>process.exit(r.statusCode===200?0:1)).on('error',()=>process.exit(1))"
CMD ["node", "dist/server.js"]
```

This single file demonstrates: multi-stage build (small final image, no build tools shipped), cache-friendly layer ordering, non-root user, a healthcheck, exec-form CMD, and production env. That's essentially the template most real services start from.

---
**Previous:** [05 — Containers](05-containers.md) | **Next:** [07 — Building & Optimizing Images](07-building-and-optimizing-images.md)
