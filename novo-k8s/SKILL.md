---
name: novo-k8s
description: Check health, logs, and resource usage of services running in Novo Kubernetes clusters (prod and dev). Use when the user asks to check pods, logs, health, resource usage, restarts, events, or status of any service in novo k8s. Trigger phrases include "check pods", "check logs", "pod health", "k8s health", "novo prod", "novo dev", "check service", "pod status", "resource usage", "restart count". The user passes an environment and service name as arguments (e.g., "/novo-k8s prod credit-card" or "/novo-k8s dev lending"). Default environment is prod if not specified.
---

# Novo K8s Operations

Operate on Novo EKS clusters. The user provides an environment (`prod` or `dev`) and a service name.

## Environments

| | Prod | Dev |
|---|---|---|
| **kubectl context** | `prod` | `dev` |
| **Cluster** | `novo-prod` | `novo-dev` |
| **Namespace** | `novo` | `novo` |
| **Region** | us-east-1 | us-east-1 |
| **SSO login** | `aws sso login --profile prod` | `aws sso login --profile dev` |

All pods run an `istio-proxy` sidecar alongside the app container.

## Parsing User Input

The user provides: `/novo-k8s [env] <service-name> [operation]`

- **env**: `prod` or `dev`. Default to `prod` if not specified.
- **service-name**: Short name like `credit-card`, `lending`, `novo-api`.
- **operation**: Optional. One of: `health`, `logs`, `resources`, `events`, `deployment`. Default to `health`.

Examples:
- `/novo-k8s prod credit-card` → health check on prod
- `/novo-k8s dev lending logs` → logs on dev
- `/novo-k8s credit-card` → health check on prod (default env)

Set `CTX` to the kubectl context (`prod` or `dev`) and use `--context=$CTX` in all commands.

## Auth Check

Before any kubectl command, verify connectivity:

```bash
kubectl --context=$CTX get ns novo --no-headers 2>&1
```

If this fails with auth errors, instruct the user to run `aws sso login --profile $CTX` first.

## Resolving the Service Name

Match the user's short name to pods:

```bash
kubectl --context=$CTX get pods -n novo --no-headers | grep <service-name>
```

For the full service catalog, see [references/services.md](references/services.md).

## Operations

### 1. Health Check (default)

When the user asks to "check" a service without specifics, run the full health check:

```bash
# Pod status
kubectl --context=$CTX get pods -n novo -o wide | grep <service-name>

# Resource usage
kubectl --context=$CTX top pods -n novo | grep <service-name>
```

Present results as a markdown table with columns: Pod, Status, Ready, Restarts, Age, CPU, Memory.

Flag any issues:
- Pods not in `Running` state (except `Completed` jobs)
- Containers not fully ready (e.g., `1/2`)
- High restart counts (> 0)
- Pods stuck in `CrashLoopBackOff`, `Pending`, `ImagePullBackOff`

### 2. Logs

```bash
# Recent logs (last 100 lines) - use app container, not istio-proxy
kubectl --context=$CTX logs <pod-name> -n novo -c <container-name> --tail=100

# With timestamps
kubectl --context=$CTX logs <pod-name> -n novo -c <container-name> --tail=100 --timestamps

# From a time window
kubectl --context=$CTX logs <pod-name> -n novo -c <container-name> --since=1h

# Previous container logs (after crash/restart)
kubectl --context=$CTX logs <pod-name> -n novo -c <container-name> --previous
```

The app container name matches the deployment name. To confirm:

```bash
kubectl --context=$CTX get pod <pod-name> -n novo -o jsonpath='{.spec.containers[*].name}'
```

Search for errors:

```bash
kubectl --context=$CTX logs <pod-name> -n novo -c <container-name> --tail=500 | grep -i "<search-term>"
```

Multiple replicas:

```bash
kubectl --context=$CTX logs -l app=<label> -n novo -c <container-name> --tail=50
```

### 3. Resource Usage

```bash
kubectl --context=$CTX top pods -n novo | grep <service-name>

# Requests and limits
kubectl --context=$CTX get pods -n novo -o custom-columns='NAME:.metadata.name,CPU_REQ:.spec.containers[0].resources.requests.cpu,CPU_LIM:.spec.containers[0].resources.limits.cpu,MEM_REQ:.spec.containers[0].resources.requests.memory,MEM_LIM:.spec.containers[0].resources.limits.memory' | grep <service-name>
```

### 4. Events & Debugging

```bash
kubectl --context=$CTX describe pod <pod-name> -n novo | tail -30

kubectl --context=$CTX get events -n novo --sort-by='.lastTimestamp' | grep <service-name> | tail -20
```

### 5. Deployment Info

```bash
kubectl --context=$CTX get deployment -n novo | grep <service-name>

kubectl --context=$CTX rollout status deployment/<deployment-name> -n novo

kubectl --context=$CTX get deployment <deployment-name> -n novo -o jsonpath='{.spec.template.spec.containers[0].image}'
```

## Output Format

Always present results with:

1. **Environment badge** - Clearly state `[PROD]` or `[DEV]` at the top
2. **Status table** - Pod name, status, ready, restarts, age, CPU, memory
3. **Issues** - Flag anything abnormal
4. **Completed jobs** - List separately
5. **Assessment** - One-line overall health verdict
