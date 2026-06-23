# 09 — Docker Networking

Networking is where many learners get stuck, because "localhost" means something different inside a container. This section builds the model from the ground up.

## 9.1 The mental model

Each container (by default) gets its own **network namespace**: its own virtual network interface, IP address, routing table, and port space. Docker wires these namespaces together using virtual interfaces and a software bridge, and runs an embedded **DNS server** so containers can find each other by name.

The single most important consequence: **inside a container, `localhost` (127.0.0.1) refers to that container itself, not the host and not other containers.** To reach another container you use *its* address — and the clean way to do that is by container name over a user-defined network.

## 9.2 Network drivers (the built-in types)

| Driver | Scope | What it does | Typical use |
|--------|-------|--------------|-------------|
| **bridge** | Single host | Default. A private internal network behind a virtual bridge; containers get internal IPs and reach the outside via NAT | Most single-host apps |
| **host** | Single host | Container shares the host's network stack directly — no isolation, no NAT, no port mapping needed | Max network performance; ports bind directly to host |
| **none** | Single host | No networking at all (only loopback) | Fully isolated / security-sensitive batch jobs |
| **overlay** | Multi-host | A virtual network spanning multiple Docker hosts (used by Swarm) so containers on different machines communicate | Clustered services (section 12) |
| **macvlan** | Single host | Gives a container its own MAC and an IP on the physical LAN, appearing as a real device on the network | Legacy apps needing to look like physical hosts |
| **ipvlan** | Single host | Similar to macvlan but shares the host's MAC; L2/L3 modes | Advanced LAN integration |

```bash
docker network ls                       # list networks
docker network inspect bridge           # details: subnet, gateway, connected containers
docker network create mynet             # create a user-defined bridge network
docker network create --driver overlay myoverlay
docker network rm mynet
docker network prune
docker network connect mynet web        # attach a running container
docker network disconnect mynet web
```

## 9.3 The default bridge vs user-defined bridges (important!)

There are two kinds of bridge networks, and the difference matters a lot:

- **Default `bridge`** (the one containers join if you specify nothing): containers get IPs but there is **no automatic DNS resolution by name**. Containers can only reach each other by IP (which is fragile) or via the deprecated `--link` flag.
- **User-defined bridge** (one you create with `docker network create`): provides **automatic DNS** — containers can reach each other **by container name** (or network alias). It also offers better isolation between unrelated apps.

**Best practice: always create a user-defined bridge network for your app's containers.** Then `db` can reach `api` simply as the hostname `api`. (Docker Compose does this for you automatically — every Compose project gets a user-defined network and services resolve each other by service name.)

```bash
docker network create appnet
docker run -d --name db  --network appnet postgres:16
docker run -d --name api --network appnet myapi   # inside api: connect to host "db:5432"
```

## 9.4 Publishing ports (container → host → world)

By default a container's ports are reachable only from within its Docker network, not from your host or the internet. To expose a container port to the host, **publish** it:

```bash
docker run -d -p 8080:80 nginx        # host:8080  →  container:80
docker run -d -p 127.0.0.1:8080:80 nginx   # bind only to host loopback (not public)
docker run -d -p 80:80/udp myapp      # UDP
docker run -d -P myapp                # publish all EXPOSEd ports to random host ports
docker port <container>               # show the actual mappings
```

Syntax: `-p [HOST_IP:]HOST_PORT:CONTAINER_PORT[/protocol]`.

Key distinctions:
- **`EXPOSE`** (Dockerfile) is documentation only — it does *not* publish anything (section 06.2).
- **`-p`** actually creates the host↔container mapping (via NAT/iptables or nftables rules the daemon manages).
- Binding to `0.0.0.0` (the default for `-p 8080:80`) exposes the port on **all host interfaces**, including public ones. To keep a service private, bind to `127.0.0.1` explicitly.

> **Security gotcha:** Docker's published-port rules can bypass some host firewalls (UFW in particular), because Docker inserts its own iptables/nftables rules. If you `-p 5432:5432` a database on a public server, it may be exposed to the internet even with UFW "blocking" it. Bind sensitive ports to `127.0.0.1`, or don't publish them at all (let other containers reach them over the internal network).

## 9.5 How containers reach the host and each other

- **Container → other container (same user-defined network):** use the **container/service name** as hostname. This is the clean, recommended path.
- **Container → the host machine:** use the special DNS name **`host.docker.internal`** (available on Docker Desktop, and on Linux when you add `--add-host=host.docker.internal:host-gateway`). Useful for reaching a service running on the host (e.g. a DB on your laptop).
- **Host → container:** reach a *published* port via `localhost:HOST_PORT`.
- **Container → internet:** works by default (outbound via NAT) on bridge networks.

## 9.6 Host and none networking

```bash
docker run -d --network host nginx     # binds to host ports directly; no -p needed
docker run -d --network none myjob     # no network; only loopback inside
```

- **host:** removes the network namespace isolation. The container's ports are the host's ports (no mapping, no NAT). Lowest overhead and latency, but no isolation and potential port conflicts. Note: on macOS/Windows, host networking behaves differently because of the Linux VM in between.
- **none:** total network isolation. Good for untrusted compute that must not talk to anything.

## 9.7 Overlay networks (multi-host)

When containers live on **different physical hosts** (a Swarm cluster), an **overlay** network creates a single virtual L2 network across them, encapsulating traffic so containers communicate as if on one LAN. Swarm uses overlay networks plus an internal load-balancing VIP and DNS-based service discovery. This is the foundation of multi-host orchestration (section 12). Kubernetes solves the same problem with its own CNI plugins instead.

## 9.8 DNS and service discovery

- On a user-defined network, Docker runs an embedded DNS resolver at `127.0.0.11` inside each container. Names resolve to current container IPs, so containers can be restarted/rescheduled and still be found by name.
- **Network aliases** let a container be reachable under additional names: `docker run --network appnet --network-alias db postgres`.
- You can override DNS with `--dns`, `--dns-search`, and add static entries with `--add-host name:ip`.

## 9.9 Inspecting and debugging networking

```bash
docker network inspect appnet                 # which containers, what subnet/IPs
docker inspect -f '{{json .NetworkSettings.Networks}}' web   # a container's networks
docker exec -it web sh                          # then, inside:
#   getent hosts db        → does the name resolve?
#   nc -zv db 5432         → can I reach the port?
#   ip addr / cat /etc/resolv.conf
```

A typical "they can't talk to each other" checklist:
1. Are both containers on the **same user-defined** network? (`docker network inspect`)
2. Are you using the **container/service name** as the hostname (not `localhost`)?
3. Is the target app **listening on `0.0.0.0`** inside its container, not just `127.0.0.1`? (A server bound to loopback inside the container is unreachable from other containers — a very common bug.)
4. Right **port**? Remember you connect to the *container* port on the internal network, not the published host port.
5. For host-published access, is the port actually **published** with `-p`?

## 9.10 Custom subnets and address pools

You can control IP ranges to avoid clashes with corporate networks:

```bash
docker network create --subnet 10.20.0.0/24 --gateway 10.20.0.1 appnet
docker run --network appnet --ip 10.20.0.10 myapp     # static IP (user-defined nets)
```

Daemon-wide defaults for auto-created networks are set via `default-address-pools` in `/etc/docker/daemon.json` (section 02.8) — useful when Docker's default `172.x` ranges collide with your VPN/LAN.

## 9.11 Firewall backends (recent change)

Historically Docker programmed the host firewall using **iptables**. Modern Linux distros are moving to **nftables**, and Docker Engine has begun adding direct nftables support (experimental in recent versions, opt-in via `--firewall-backend=nftables`). For most users this is transparent, but it explains why Docker's networking interacts with the host firewall the way it does, and why the UFW-bypass gotcha (9.4) exists.

---
**Previous:** [08 — Storage & Volumes](08-storage-and-volumes.md) | **Next:** [10 — Docker Compose](10-docker-compose.md)
