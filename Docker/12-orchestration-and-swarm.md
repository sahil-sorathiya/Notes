# 12 — Orchestration & Swarm

## 12.1 Why orchestration exists

Compose runs many containers on **one** host. Production usually needs **many hosts** and capabilities Compose doesn't provide:

- **Scheduling** — deciding which node runs which container, based on resources.
- **High availability** — if a container or a whole node dies, restart/reschedule it elsewhere automatically.
- **Scaling** — run N replicas of a service and balance traffic across them.
- **Rolling updates & rollbacks** — deploy new versions gradually with zero downtime, and revert safely.
- **Service discovery & load balancing** — find healthy replicas and distribute requests.
- **Secret/config management** at cluster scale.

An **orchestrator** automates all of this across a cluster of machines. The two you'll hear about are **Docker Swarm** and **Kubernetes**.

## 12.2 Swarm vs Kubernetes (the honest summary)

| | **Docker Swarm** | **Kubernetes (K8s)** |
|---|---|---|
| Setup complexity | Very low — built into Docker | High — many components, steeper learning curve |
| Concepts | Few (service, task, stack) | Many (pod, deployment, service, ingress, configmap…) |
| Config | Reuses your **Compose file** | YAML manifests / Helm charts |
| Ecosystem & features | Modest | Vast — the industry standard, huge ecosystem |
| Autoscaling, advanced networking | Limited | Extensive |
| Where it's going | Maintained but de-emphasized | Dominant; what most orgs run in production |

**Practical reality:** Kubernetes won the orchestration war and is what most companies use at scale. **Swarm** remains useful because it's dramatically simpler and reuses everything you already know (Compose files, Docker CLI), making it a great way to *learn orchestration concepts* and to run small clusters without K8s overhead. The concepts you learn in Swarm (services, replicas, rolling updates, overlay networks) transfer conceptually to Kubernetes.

These notes focus on Swarm (since it's part of Docker) and then point you toward Kubernetes.

## 12.3 Swarm architecture

A **swarm** is a cluster of Docker hosts (nodes) running in swarm mode.

- **Manager nodes** — maintain cluster state, schedule tasks, and expose the API. They use the **Raft** consensus algorithm to stay consistent, so you run an **odd number** (3 or 5) for fault tolerance. A cluster tolerates the loss of `(N-1)/2` managers.
- **Worker nodes** — run the actual workload containers (tasks). Managers are workers too by default.

Core objects:
- **Service** — the declarative definition of *what* to run (image, replicas, ports, networks). The desired state.
- **Task** — a single container instance of a service, scheduled onto a node. Swarm continuously **reconciles** reality toward the declared desired state (if a task dies, it launches a replacement).
- **Stack** — a group of services deployed together from a Compose file.

```
        ┌──────────── Swarm cluster ────────────┐
        │  Manager (leader)  Manager   Manager   │   ← Raft consensus, scheduling
        │       │                                │
        │   schedules tasks                      │
        │       ▼                                │
        │  Worker   Worker   Worker   Worker     │   ← run service tasks (containers)
        └────────────────────────────────────────┘
```

## 12.4 Setting up a swarm

```bash
# On the first manager
docker swarm init --advertise-addr <MANAGER-IP>
# → prints a `docker swarm join --token ...` command

# On each worker, run the printed join command
docker swarm join --token <WORKER-TOKEN> <MANAGER-IP>:2377

# Get join tokens later
docker swarm join-token worker
docker swarm join-token manager

# Inspect the cluster
docker node ls                          # list nodes (managers/workers, status)
docker node inspect <node>
docker node update --availability drain <node>   # cordon a node for maintenance
docker swarm leave                      # a node leaves the swarm
```

## 12.5 Services: deploying and scaling

```bash
# Create a replicated service
docker service create \
  --name web \
  --replicas 3 \
  --publish 8080:80 \
  nginx:alpine

docker service ls                       # all services + replica counts
docker service ps web                   # tasks of a service and which nodes they're on
docker service logs web                 # aggregated logs
docker service inspect web

# Scale up/down
docker service scale web=5

# Update (rolling) — e.g. new image version
docker service update --image nginx:1.27-alpine web

# Roll back to the previous spec
docker service rollback web

docker service rm web
```

**Service modes:**
- **Replicated** (default) — run a specified number of identical tasks (`--replicas N`).
- **Global** — run exactly one task on **every** node (`--mode global`), e.g. for log shippers/monitoring agents.

## 12.6 Rolling updates and rollbacks

Swarm updates a service's tasks gradually so the service stays available:

```bash
docker service update \
  --image myapp:2.0 \
  --update-parallelism 1 \      # update 1 task at a time
  --update-delay 10s \          # wait 10s between batches
  --update-failure-action rollback \   # auto-rollback if updates fail
  myapp
```

Combined with **healthchecks**, Swarm only proceeds to the next batch once new tasks are healthy, and can automatically roll back on failure. This is zero-downtime deployment in a nutshell — and the same idea Kubernetes implements with Deployments.

## 12.7 Swarm networking and load balancing

- **Overlay networks** (section 09.7) span all nodes so service tasks on different machines communicate on one virtual network.
- **Routing mesh:** when you publish a service port, *every* node accepts traffic on that port and routes it to a healthy task anywhere in the cluster — so an external load balancer can target any node.
- **Internal load balancing:** each service gets a stable **virtual IP (VIP)**; requests to the service name are balanced across its tasks automatically via DNS + the VIP.

```bash
docker network create --driver overlay --attachable backend
docker service create --name api --network backend --replicas 3 myapi
# other services reach it simply as "api"
```

## 12.8 Stacks (Compose at cluster scale)

A **stack** deploys a multi-service app from a Compose file across the swarm. This is where the `deploy:` key (section 10.12) finally matters.

```yaml
# stack.yaml (a Compose file with deploy settings)
services:
  web:
    image: myorg/web:1.0
    ports: ["8080:80"]
    deploy:
      replicas: 3
      update_config:
        parallelism: 1
        delay: 10s
      restart_policy:
        condition: on-failure
      resources:
        limits: { cpus: "0.5", memory: 256M }
  db:
    image: postgres:16
    volumes: [db-data:/var/lib/postgresql/data]
    deploy:
      placement:
        constraints: ["node.role == worker"]
volumes:
  db-data:
```

```bash
docker stack deploy -c stack.yaml myapp     # deploy/update the stack
docker stack services myapp                 # services in the stack
docker stack ps myapp                       # tasks across nodes
docker stack rm myapp                        # tear down
```

So the same Compose file you use for local dev (`docker compose up`) can deploy to a cluster (`docker stack deploy`) — the `deploy:` block is honored only in the latter.

## 12.9 Secrets and configs in Swarm

Swarm has first-class, encrypted **secrets** and **configs** (unlike plain Compose on a single host):

```bash
echo "s3cr3t" | docker secret create db_password -
docker config create nginx_conf ./nginx.conf
```
```yaml
services:
  db:
    image: postgres:16
    secrets: [db_password]
    environment:
      POSTGRES_PASSWORD_FILE: /run/secrets/db_password
secrets:
  db_password:
    external: true
```

Secrets are stored encrypted in the Raft log and mounted into tasks as in-memory files at `/run/secrets/<name>` — never written to the image or to disk in the container. This is the right way to handle credentials in a swarm (section 13).

## 12.10 Stateful workloads and storage in a cluster

Stateless services scale trivially. **Stateful** ones (databases) are harder: a task rescheduled to another node needs its data to follow it. Options:

- **Pin** the task to a node (placement constraints) so its local volume is always there — simple but fragile.
- Use a **networked/cluster-aware volume driver** (NFS, cloud block storage plugins) so data is reachable from any node.
- Often the pragmatic answer: run databases as **managed cloud services** rather than inside the cluster.

This is one of the hardest parts of orchestration and a major reason Kubernetes has elaborate storage abstractions (PersistentVolumes/StatefulSets).

## 12.11 Where to go next: Kubernetes

If you outgrow Swarm, Kubernetes is the destination. A rough concept map from what you've learned:

| Swarm / Docker concept | Kubernetes equivalent (roughly) |
|------------------------|----------------------------------|
| Container | Container inside a **Pod** |
| Service (replicated) | **Deployment** + **ReplicaSet** |
| Service VIP / routing mesh | **Service** (+ **Ingress** for HTTP) |
| Stack / Compose file | A set of **manifests** (or a **Helm** chart) |
| Secret / config | **Secret** / **ConfigMap** |
| Overlay network | **CNI** networking |
| Volume driver | **PersistentVolume / PersistentVolumeClaim / StorageClass** |
| Global service | **DaemonSet** |

Docker Desktop ships a one-click single-node Kubernetes, and tools like **kind**, **minikube**, and **k3s** let you run K8s locally to learn. Crucially, the **images** you build with Docker run unchanged on Kubernetes — orchestration changes *how* containers are run and managed, not the containers themselves.

---
**Previous:** [11 — Registries & Distribution](11-registries-and-distribution.md) | **Next:** [13 — Security](13-security.md)
