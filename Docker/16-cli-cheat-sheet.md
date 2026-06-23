# 16 — Docker CLI Cheat Sheet

A task-oriented reference. Modern Docker also offers namespaced forms (`docker container ...`, `docker image ...`); the short forms below still work and are what most people type.

## Setup & info
```bash
docker --version                 # client version
docker version                   # client + daemon versions
docker info                      # daemon config: storage driver, cgroup, image store
docker login [registry]          # authenticate to a registry
docker logout [registry]
docker context ls                # list daemon targets
docker context use <name>        # switch target daemon
```

## Images
```bash
docker pull <image>[:tag]        # download an image
docker images                    # list local images   (alias: docker image ls)
docker history <image>           # layers + sizes
docker inspect <image>           # full metadata (JSON)
docker tag <src> <new>           # add a name pointing to the same image
docker rmi <image>               # remove an image
docker image prune [-a]          # remove dangling [or all unused] images
docker save -o out.tar <image>   # export image to tar
docker load -i out.tar           # import image from tar
docker build -t name:tag .       # build from ./Dockerfile
docker build -f File -t n:t .    # custom Dockerfile name
docker build --no-cache .        # ignore cache
docker build --target <stage> .  # build up to a stage
docker buildx build --platform linux/amd64,linux/arm64 -t n:t --push .   # multi-arch
docker push <image>              # upload to a registry
docker scout cves <image>        # vulnerability scan
docker scout quickview <image>   # summary + base-image advice
```

## Containers — run & lifecycle
```bash
docker run <image>               # create + start (foreground)
docker run -d <image>            # detached (background)
docker run -it <image> sh        # interactive shell
docker run --rm <image>          # auto-remove on exit
docker run -d --name web -p 8080:80 nginx
docker create <image>            # create without starting
docker start <c>                 # start a created/stopped container
docker stop <c>                  # graceful stop (SIGTERM → SIGKILL)
docker restart <c>
docker pause <c> / docker unpause <c>
docker kill <c>                  # immediate SIGKILL
docker rm <c>                    # remove (stopped); -f to force-remove running
docker rename <old> <new>
docker update --memory 1g --cpus 2 <c>   # change limits live
```

## Containers — inspect & interact
```bash
docker ps                        # running containers
docker ps -a                     # all containers
docker ps -q                     # IDs only
docker ps --format '{{.Names}}\t{{.Status}}'
docker logs -f --tail 100 <c>    # follow last 100 log lines
docker exec -it <c> bash         # shell into a running container (or: sh)
docker exec <c> <cmd>            # one-off command inside
docker attach <c>                # attach to PID 1 stdio (careful)
docker inspect <c>               # full JSON state/config
docker inspect -f '{{.State.Status}}' <c>
docker top <c>                   # processes inside
docker stats [<c>...]            # live resource usage
docker port <c>                  # port mappings
docker diff <c>                  # filesystem changes vs image
docker cp <c>:/path ./local      # copy out;  ./local <c>:/path to copy in
docker commit <c> img:tag        # snapshot to image (debug only)
```

## Run flags worth memorizing
```bash
-d                 # detached
-it                # interactive + TTY
--rm               # remove on exit
--name NAME        # name it
-p H:C             # publish port host:container
-P                 # publish all EXPOSEd ports to random host ports
-e KEY=VAL         # env var       (--env-file FILE for many)
-v NAME:/path      # named volume   (./host:/path for bind mount)
--tmpfs /path      # in-memory mount
-w /path           # working dir
-u UID[:GID]       # run as user
--network NET      # attach to a network
--restart unless-stopped
--memory 512m --cpus 1.5 --pids-limit 200
--cap-drop ALL --cap-add NET_BIND_SERVICE
--read-only --security-opt no-new-privileges
--entrypoint CMD   # override entrypoint
--health-cmd '...' # ad-hoc healthcheck
```

## Volumes
```bash
docker volume create <name>
docker volume ls                 # list (-f dangling=true for unused)
docker volume inspect <name>
docker volume rm <name>
docker volume prune              # remove unused volumes (⚠ data loss)
docker run -v <name>:/path <image>
docker run -v "$(pwd)":/app <image>          # bind mount
docker run --mount type=volume,source=<name>,target=/path <image>
```

## Networks
```bash
docker network ls
docker network create <name>                 # user-defined bridge (DNS by name!)
docker network create --driver overlay <name>
docker network inspect <name>
docker network connect <net> <c>
docker network disconnect <net> <c>
docker network rm <name>
docker network prune
docker run --network <net> --name api <image>   # reachable as "api" on that net
```

## Docker Compose (v2: `docker compose`)
```bash
docker compose up -d             # start project (detached)
docker compose up --build        # rebuild then start
docker compose down              # stop + remove (containers, networks)
docker compose down -v           # ...also remove named volumes (⚠ data loss)
docker compose ps                # project status
docker compose logs -f [svc]     # follow logs
docker compose exec <svc> sh     # shell into a running service
docker compose run --rm <svc> <cmd>   # one-off container (tests/migrations)
docker compose build [svc]
docker compose pull
docker compose up -d --scale worker=4
docker compose config            # render & validate merged config
docker compose -f a.yaml -f b.yaml up -d      # layered/override files
docker compose --profile debug up             # include profiled services
```

## Swarm & stacks
```bash
docker swarm init --advertise-addr <IP>
docker swarm join --token <tok> <MANAGER>:2377
docker swarm join-token worker|manager
docker node ls
docker node update --availability drain <node>
docker service create --name web --replicas 3 -p 8080:80 nginx
docker service ls
docker service ps <svc>
docker service scale <svc>=5
docker service update --image img:2.0 <svc>   # rolling update
docker service rollback <svc>
docker service logs <svc>
docker service rm <svc>
docker stack deploy -c stack.yaml <name>      # deploy Compose file to swarm
docker stack services <name>
docker stack ps <name>
docker stack rm <name>
docker secret create <name> <file>
docker config create <name> <file>
```

## System & cleanup
```bash
docker system df [-v]            # disk usage (detailed)
docker system info               # = docker info
docker system events             # live daemon event stream
docker container prune           # remove stopped containers
docker image prune [-a]          # dangling [or all unused] images
docker volume prune              # unused volumes (⚠)
docker network prune
docker builder prune             # build cache
docker system prune              # containers + networks + dangling images + cache
docker system prune -a --volumes # aggressive (⚠⚠ removes unused images + volumes)
```

## Handy `inspect`/format snippets
```bash
docker inspect -f '{{.State.Status}}' <c>
docker inspect -f '{{.State.OOMKilled}}' <c>            # was it OOM-killed?
docker inspect -f '{{.NetworkSettings.IPAddress}}' <c>
docker inspect -f '{{json .Mounts}}' <c>
docker inspect -f '{{range .Config.Env}}{{println .}}{{end}}' <c>
docker image inspect -f '{{.Config.Entrypoint}}' <image>
docker ps --filter "status=exited" --filter "name=web" -q
```

## Exit codes quick reference
```
0    success
1    general application error
125  docker run itself failed (e.g. bad flag)
126  command found but not executable
127  command not found
137  SIGKILL (often Out-Of-Memory kill)
143  SIGTERM (graceful stop)
```

## Environment variables the CLI respects
```bash
DOCKER_HOST            # which daemon to talk to (e.g. ssh://user@host)
DOCKER_CONTEXT         # active context
DOCKER_BUILDKIT=1      # force BuildKit (default on modern Docker)
DOCKER_CONTENT_TRUST=1 # require signed images
DOCKER_DEFAULT_PLATFORM=linux/amd64   # default build/run platform
```

---
**Previous:** [15 — Best Practices & Production](15-best-practices-and-production.md) | **Back to:** [Index](00-README.md)
