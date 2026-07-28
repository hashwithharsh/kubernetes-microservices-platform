# Kubernetes Resource Explanations

This file is my reference for the Kubernetes resources used in this project. I wrote it while building the voting application so I can explain not only what each resource does, but also why I used it here.

The examples below are based on the manifests in this repository. They are intentionally kept at a practical junior DevOps level.

---

## 1. Namespace

**File:** `namespace/namespace.yaml`

A Namespace gives Kubernetes resources a separate logical area. I use the `voting` namespace for this project so that the application resources are easier to manage and inspect together.

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: voting
```

### Why I used it

- Keeps project resources separate from the default namespace.
- Makes commands easier to scope with `-n voting`.
- Gives me a place to apply namespace-level policies such as ResourceQuota and LimitRange.

### Useful commands

```bash
kubectl get namespaces
kubectl get all -n voting
kubectl describe namespace voting
```

---

## 2. ResourceQuota

**File:** `namespace/resource-quota.yaml`

ResourceQuota puts limits on the total amount of selected resources that can be consumed in a namespace.

This project uses:

- Maximum 20 Pods
- 2 CPU requested
- 4 GiB memory requested
- 4 CPU limit
- 8 GiB memory limit

### Why I chose these values

The voting application is small, so I did not need a large namespace budget. The quota is large enough for the normal application resources and a few test workloads, but small enough to demonstrate what happens when a namespace reaches its limits.

### Important point

ResourceQuota is a **namespace-wide** control. It is different from a container's individual resource request or limit.

```text
Container requests/limits
        ↓
individual workload

ResourceQuota
        ↓
whole namespace
```

### Useful commands

```bash
kubectl get resourcequota -n voting
kubectl describe resourcequota voting-quota -n voting
```

---

## 3. LimitRange

**File:** `namespace/limit-range.yaml`

LimitRange supplies default CPU and memory requests/limits for containers that do not specify them and can also enforce per-container constraints.

In this project the defaults are:

```text
Default request: 100m CPU / 128Mi memory
Default limit:   500m CPU / 512Mi memory
```

### Why I used it

It gives the namespace a safe default instead of allowing every test Pod to be created without resource settings.

### ResourceQuota vs LimitRange

| Resource | Main purpose |
|---|---|
| ResourceQuota | Controls total resource usage in a namespace |
| LimitRange | Controls/defaults resource settings for individual containers |

### Useful commands

```bash
kubectl get limitrange -n voting
kubectl describe limitrange voting-defaults -n voting
```

---

## 4. Deployment

**Files:** `deployments/vote.yaml`, `deployments/result.yaml`, `deployments/worker.yaml`, `deployments/redis.yaml`

A Deployment manages replicated, replaceable application Pods and provides declarative updates.

I use Deployments for the stateless parts of the application and Redis in this learning project.

### Vote and result

Both start with two replicas. This gives the front-end workloads some basic redundancy and also gives the HPA/rolling-update work something useful to operate on.

### Worker

The worker starts with one replica because it is a background processing component in this project.

### Redis

Redis is also one replica here. This is a learning setup, not a Redis HA design.

### Why Deployment instead of creating Pods directly

A Deployment gives me:

- Replica management
- Rolling updates
- Rollbacks
- Desired-state management
- Self-healing through ReplicaSets

### Useful commands

```bash
kubectl get deployments -n voting
kubectl describe deployment vote -n voting
kubectl rollout status deployment/vote -n voting
kubectl rollout history deployment/vote -n voting
kubectl rollout undo deployment/vote -n voting
```

---

## 5. RollingUpdate

The `vote` and `result` Deployments use:

```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 0
    maxSurge: 1
```

### What this means

- `maxUnavailable: 0` means Kubernetes should avoid intentionally reducing the available replica count during the update.
- `maxSurge: 1` allows one extra Pod while the new version is being brought up.

I chose this because these are user-facing services and I wanted to practice a controlled update rather than deleting all old Pods first.

### Commands

```bash
kubectl rollout status deployment/vote -n voting
kubectl rollout history deployment/vote -n voting
kubectl rollout undo deployment/vote -n voting
```

---

## 6. StatefulSet

**File:** `statefulsets/postgres.yaml`

PostgreSQL uses a single-replica StatefulSet.

A StatefulSet is useful for workloads where stable identity and persistent storage matter. In this project I use it to learn how a stateful workload behaves with a PersistentVolumeClaim.

### Why PostgreSQL uses StatefulSet here

The database has data that should survive Pod replacement. The StatefulSet also gives the database workload a stable identity pattern.

This is **not** a highly available PostgreSQL cluster. I intentionally keep it at one replica because the goal is learning Kubernetes storage and StatefulSet behavior.

### Important fields

```yaml
serviceName: db
replicas: 1
```

The `serviceName` matches the internal Service used by the application: `db`.

### Useful commands

```bash
kubectl get statefulsets -n voting
kubectl describe statefulset postgres -n voting
kubectl get pods -n voting -l app=postgres
```

---

## 7. PersistentVolumeClaim

The PostgreSQL StatefulSet uses a `volumeClaimTemplates` entry requesting **2Gi** of storage.

```yaml
volumeClaimTemplates:
  - metadata:
      name: postgres-data
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 2Gi
```

### Why 2Gi

The sample application has a small database workload, so 2Gi is more than enough for this learning project while keeping local-cluster resource usage reasonable.

### Storage flow

```text
StatefulSet
    ↓
PVC
    ↓
StorageClass / provisioner
    ↓
Persistent storage
    ↓
PostgreSQL
```

### Useful commands

```bash
kubectl get pvc -n voting
kubectl describe pvc postgres-data-postgres-0 -n voting
kubectl get pv
```

The exact PVC name can vary with the StatefulSet naming rules.

---

## 8. Service

**Files:** `services/*.yaml`

Services provide stable network endpoints for Pods.

This project uses `ClusterIP` Services because the application components communicate inside the cluster.

### Services in this project

```text
vote
result
redis
db
```

The database Service is deliberately called `db` because the application is configured to use `DB_HOST=db`.

### Why ClusterIP

Redis and PostgreSQL do not need direct external access. Vote and result are also kept internal and are exposed through Ingress.

### Useful commands

```bash
kubectl get svc -n voting
kubectl describe svc vote -n voting
kubectl get endpoints -n voting
kubectl get endpointslices -n voting
```

---

## 9. Service selectors and labels

Services find Pods through label selectors.

For example, the vote Service selects:

```yaml
selector:
  app: vote
```

and the vote Pods have:

```yaml
labels:
  app: vote
```

### Why this matters

A Service can exist and still have no usable backend if its selector does not match the Pod labels.

This is one of the first things I check when a Service is not reaching an application.

```bash
kubectl get pods --show-labels -n voting
kubectl get endpoints vote -n voting
```

---

## 10. Ingress

**File:** `ingress/voting-ingress.yaml`

Ingress defines HTTP routing rules from hostnames to Services.

This project uses:

```text
vote.local   → vote Service
result.local → result Service
```

### Why I used Ingress

It lets me practice host-based HTTP routing without exposing every application Service as a NodePort or LoadBalancer.

### Important dependency

An Ingress resource does not process traffic by itself. A compatible Ingress controller, such as nginx in the local-cluster setup, must be installed.

### Useful commands

```bash
kubectl get ingress -n voting
kubectl describe ingress voting-ingress -n voting
kubectl get ingressclass
```

---

## 11. ConfigMap

**File:** `configmaps/app-config.yaml`

The `voting-config` ConfigMap contains non-sensitive application configuration:

```text
REDIS_HOST=redis
DB_HOST=db
DB_NAME=postgres
DB_USER=postgres
APP_ENV=kubernetes
```

### Why I used ConfigMap

These values are configuration, not secrets. Keeping them outside the image makes the container image less tied to one environment.

### Useful commands

```bash
kubectl get configmap -n voting
kubectl describe configmap voting-config -n voting
kubectl get configmap voting-config -o yaml -n voting
```

---

## 12. Secret

**File:** `secrets/postgres-secret.yaml`

The PostgreSQL password is provided through a Kubernetes Secret.

The repository contains only a demo password. It is not a real credential.

### Why I used a Secret

Passwords and other sensitive values should not be stored as normal configuration in a ConfigMap.

The application Pods read the password using `secretKeyRef`.

```text
Secret
  ↓
Pod environment variable
  ↓
Application
```

### Important real-world note

A Kubernetes Secret manifest in Git is not automatically a secure secret-management solution. In a real environment I would use an appropriate secret-management workflow and avoid committing real credentials.

### Useful commands

```bash
kubectl get secrets -n voting
kubectl describe secret postgres-secret -n voting
```

I avoid printing secret values unnecessarily.

---

## 13. Resource requests

A request tells Kubernetes the amount of CPU or memory the container asks the scheduler to reserve for scheduling purposes.

Example from the vote Deployment:

```yaml
requests:
  cpu: 100m
  memory: 128Mi
```

### Why I used requests

The project uses requests so the scheduler has a baseline resource requirement and so CPU-based HPA utilization has a request value to work from.

I kept the requests modest because this application is small and is intended to run on a local learning cluster.

---

## 14. Resource limits

A limit sets a maximum amount of CPU or memory that a container can use under the configured cgroup limits.

Example:

```yaml
limits:
  cpu: 500m
  memory: 512Mi
```

### Why I used limits

I wanted to avoid an accidentally misbehaving sample container consuming unlimited local-cluster resources.

The limits are deliberately higher than the requests to leave room for normal application bursts.

### Important troubleshooting connection

If a container goes beyond its memory limit, it can be killed by the kernel and Kubernetes may report:

```text
OOMKilled
```

That is why resource configuration is also part of the troubleshooting section.

---

## 15. Readiness probe

A readiness probe answers:

> Can this Pod receive traffic right now?

The vote and result applications use HTTP readiness checks. PostgreSQL and Redis use command-based checks.

### Why readiness matters

A container can be running while its application is still starting or unavailable.

If readiness fails, Kubernetes can keep the Pod out of Service endpoints until it becomes ready.

### Useful checks

```bash
kubectl describe pod <pod> -n voting
kubectl get endpoints -n voting
```

---

## 16. Liveness probe

A liveness probe answers:

> Is this container still functioning well enough to keep running?

If a liveness probe repeatedly fails, Kubernetes can restart the container.

### Readiness vs liveness

```text
Readiness
→ should this Pod receive traffic?

Liveness
→ should this container keep running?
```

I keep these concepts separate because confusing them can cause unnecessary restarts.

---

## 17. HorizontalPodAutoscaler

**File:** `autoscaling/vote-hpa.yaml`

The HPA targets the `vote` Deployment:

```text
minimum replicas: 2
maximum replicas: 5
CPU target: 70%
```

### Why I used it

The vote frontend is stateless and is a reasonable workload for horizontal scaling.

### Resource decision

The vote Deployment has a CPU request of `100m`. CPU utilization based HPA needs a request value to calculate utilization correctly.

### Behavior settings

I added short stabilization windows so scale-up and scale-down decisions are not changed too aggressively.

### Important dependency

The cluster needs a working metrics provider, such as Metrics Server, for resource metrics used by HPA.

### Useful commands

```bash
kubectl get hpa -n voting
kubectl describe hpa vote -n voting
kubectl top pods -n voting
```

---

## 18. ServiceAccount

**File:** `rbac/serviceaccount.yaml`

The `voting-app` ServiceAccount gives Pods an identity inside Kubernetes.

### Why I used it

The project includes an RBAC example. Instead of using the default ServiceAccount, I created an application-specific identity.

### Useful command

```bash
kubectl get serviceaccounts -n voting
```

---

## 19. Role

**File:** `rbac/role.yaml`

The `voting-reader` Role grants limited permissions inside the `voting` namespace.

It allows:

```text
get/list pods
get/list configmaps
```

### Why I chose these permissions

The example demonstrates least privilege without giving the application administrator-level access.

A Role is namespaced, so this permission set applies within the `voting` namespace.

---

## 20. RoleBinding

**File:** `rbac/rolebinding.yaml`

The RoleBinding connects the `voting-reader` Role to the `voting-app` ServiceAccount.

```text
ServiceAccount
      ↓
RoleBinding
      ↓
Role
      ↓
get/list selected resources
```

### Useful commands

```bash
kubectl get role -n voting
kubectl get rolebinding -n voting
kubectl auth can-i get pods --as=system:serviceaccount:voting:voting-app -n voting
```

---

## 21. Node affinity

**File:** `scheduling/affinity.yaml`

The scheduling example uses `preferredDuringSchedulingIgnoredDuringExecution` with a preference for nodes labelled:

```text
workload=web
```

### Why preferred instead of required

I wanted to demonstrate a scheduling preference without making the workload impossible to schedule on a small local cluster that may not have the label.

With a preferred rule, Kubernetes tries to honor the preference but can still schedule the Pod elsewhere if necessary.

### Useful commands

```bash
kubectl get nodes --show-labels
kubectl describe node <node>
kubectl get pods -o wide -n voting
```

---

## 22. Taints and tolerations

**File:** `scheduling/taint-toleration.yaml`

A taint can stop normal Pods from being scheduled on a node. A toleration allows a matching Pod to tolerate that taint.

The example uses:

```text
key: workload
value: worker
effect: NoSchedule
```

### Scheduling flow

```text
Node has taint
      ↓
normal Pod rejected
      ↓
Pod has matching toleration
      ↓
Pod is allowed to schedule
```

A toleration does not force a Pod onto a node. It only removes that particular taint as a scheduling blocker.

### Useful commands

```bash
kubectl describe node <node>
kubectl get pods -o wide -n voting
kubectl describe pod <pod> -n voting
```

---

## 23. Labels

Labels are key/value metadata attached to Kubernetes objects and are heavily used for selection.

This project uses labels such as:

```text
app=vote
app=result
app=worker
app=redis
app=postgres
```

### Why they matter

Deployments use labels to identify their Pods, Services use selectors to find Pods, and commands can filter resources using labels.

```bash
kubectl get pods -l app=vote -n voting
kubectl get pods --show-labels -n voting
```

---

## 24. Kustomization

**File:** `k8s/kustomization.yaml`

Kustomize lets me apply the main Kubernetes resource set as one group without manually maintaining one large YAML file.

The main application resources are included in this Kustomization. The scheduling files are kept as separate learning examples because I don't want those demonstration Deployments to be created every time I deploy the normal voting application.

### Main deployment command

```bash
kubectl apply -k k8s/
```

### Scheduling examples

Apply them separately when practicing:

```bash
kubectl apply -f k8s/scheduling/affinity.yaml
kubectl apply -f k8s/scheduling/taint-toleration.yaml
```

---

# How the resources fit together

The main application flow is:

```text
Namespace: voting
        |
        +-- ResourceQuota
        +-- LimitRange
        |
        +-- ConfigMap -----------+
        |                         |
        +-- Secret --------------+----> application configuration
        |
        +-- ServiceAccount
        |       |
        |       +-- RoleBinding --> Role
        |
        +-- vote Deployment -----> vote Service -----> Ingress
        |
        +-- result Deployment ---> result Service --> Ingress
        |
        +-- worker Deployment
        |
        +-- redis Deployment ----> redis Service
        |
        +-- postgres StatefulSet -> db Service
                |
                +-- PVC
```

---

# Resource decisions at a glance

| Resource | Decision in this project | Reason |
|---|---|---|
| Namespace | `voting` | Keep project resources isolated |
| ResourceQuota | 20 Pods / 2 CPU requests / 4Gi memory requests | Small local-cluster budget and quota practice |
| LimitRange | 100m/128Mi defaults, 500m/512Mi limits | Safe defaults for containers without explicit values |
| Vote replicas | 2 | Basic availability and HPA starting point |
| Result replicas | 2 | Basic availability for user-facing result page |
| Worker replicas | 1 | Simple background worker for this project |
| Redis replicas | 1 | Simple cache/queue demonstration; not HA |
| PostgreSQL replicas | 1 | Stateful storage learning; not HA |
| PostgreSQL storage | 2Gi | More than enough for the sample workload and local testing |
| Service type | ClusterIP | Internal communication; Ingress handles HTTP entry |
| Ingress | `vote.local` / `result.local` | Practice host-based routing |
| CPU/memory requests | Explicit on main workloads | Scheduling and HPA resource awareness |
| CPU/memory limits | Explicit on main workloads | Prevent runaway local resource usage |
| Probes | Readiness + liveness where appropriate | Separate traffic readiness from container health |
| HPA | Vote, 2–5 replicas, 70% CPU | Practice horizontal scaling on stateless frontend |
| RBAC | Namespace Role with get/list | Demonstrate least privilege |
| Affinity | Preferred web-node preference | Demonstrate scheduling without making local setup too strict |
| Taint/toleration | Worker example | Demonstrate dedicated-node scheduling concept |

---

# Practical troubleshooting map

When a resource fails, I normally start with the object that is closest to the symptom.

```text
Pod not starting
  → kubectl get pods
  → kubectl describe pod
  → kubectl logs
  → kubectl get events

Service not reachable
  → kubectl get svc
  → kubectl describe svc
  → kubectl get endpoints
  → check Pod labels/selectors

Database not starting
  → kubectl get statefulset
  → kubectl get pods
  → kubectl get pvc
  → kubectl describe pvc
  → kubectl logs

HPA not scaling
  → kubectl get hpa
  → kubectl describe hpa
  → kubectl top pods
  → check CPU requests

Pod Pending
  → kubectl describe pod
  → check resources
  → check PVC
  → check affinity
  → check taints/tolerations

Forbidden error
  → kubectl auth can-i
  → inspect Role
  → inspect RoleBinding
  → inspect ServiceAccount
```

---

# What I would change in a real production environment

This project is intentionally a learning/reference project. In a real environment I would review several things further, for example:

- Use a proper external secret-management process instead of a demo Secret value in Git.
- Use a production storage class and backup/recovery plan for PostgreSQL.
- Consider a managed or highly available database instead of a single Pod database.
- Use real image registry names and immutable versioning appropriate for the environment.
- Add stronger network and security controls based on the actual cluster requirements.
- Monitor workloads and cluster health with the organization's monitoring stack.

Those are outside this project's intended junior-level scope, so I have not added them just to make the repository look larger.
