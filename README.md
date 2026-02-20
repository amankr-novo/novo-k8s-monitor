# novo-k8s

A Claude Code skill that lets you check pod health, view logs, inspect resource usage, and debug services running in Novo's prod and dev Kubernetes clusters — all from your editor.

## Installation

Add this to your Claude Code project settings (`.claude/settings.json`) or user settings:

```json
{
  "skills": [
    "github:amankr-novo/novo-k8s-monitor"
  ]
}
```

Or install from the CLI:

```bash
claude skill add github:amankr-novo/novo-k8s-monitor
```

## Prerequisites

- **kubectl** configured with contexts named `prod` and `dev` pointing to the Novo EKS clusters.
- **AWS SSO** session active. If you see auth errors, run:
  ```bash
  aws sso login --profile prod   # for prod
  aws sso login --profile dev    # for dev
  ```

## Usage

```
/novo-k8s [env] <service> [operation]
```

| Argument | Required | Default | Values |
|---|---|---|---|
| `env` | No | `prod` | `prod`, `dev` |
| `service` | Yes | — | Any service from the catalog below |
| `operation` | No | `health` | `health`, `logs`, `resources`, `events`, `deployment` |

### Examples

```
/novo-k8s prod credit-card          # health check on prod
/novo-k8s dev lending logs          # view logs on dev
/novo-k8s credit-card               # health check on prod (default)
/novo-k8s novo-api resources        # resource usage on prod
/novo-k8s dev ach events            # events/debugging on dev
/novo-k8s identity deployment       # deployment info on prod
```

You can also trigger it naturally — asking "check credit-card pods on prod" or "show me lending logs on dev" will invoke the skill automatically.

### Operations

| Operation | What it does |
|---|---|
| **health** (default) | Pod status, readiness, restart counts, CPU/memory usage. Flags issues like CrashLoopBackOff, pending pods, or high restarts. |
| **logs** | Last 100 lines from the app container (not istio-proxy). Searches for errors automatically. |
| **resources** | Current CPU/memory usage vs configured requests and limits. |
| **events** | Recent Kubernetes events and `describe pod` output for debugging. |
| **deployment** | Deployment status, rollout progress, and current container image. |

## Service Catalog

Use these short names as the `<service>` argument:

| Short Name | Matches pods |
|---|---|
| `credit-card` | credit-card-api, credit-card-workers, credit-card-cron, credit-card-transaction-processing-* |
| `onboarding-credit` | onboarding-credit-card-api, onboarding-credit-card-workers, onboarding-credit-cron-* |
| `debit-card` | debit-card-api, debit-card-cron-jobs, debit-card-workers |
| `card-management-service` | card-management-service-api, card-management-service-api-cronjobs, card-management-service-api-worker |
| `card-service` | card-service-node-cron, card-service-node-server, card-settlement-crons |
| `card-settlement-service` | card-settlement-worker |
| `novo-api` | novo-api, novo-mobile-api-cron, novo-mobile-api-db-migration, novo-mobile-api-worker |
| `lending` | lending-cron, lending-v2, lending-worker-v2 |
| `ach` | ach-service-api, ach-service-cron, ach-service-worker, ach-workers-service |
| `identity` | identity-api, identity-auth, identity-role-operations-consumer, identity-workers |
| `onboarding` | onboarding-api, onboarding-admin-api, onboarding-admin-cron-jobs, onboarding-admin-frontend-v2, onboarding-admin-worker, onboarding-frontend-v2 |
| `billing` | novo-billing-cron, novo-billing-service-api, novo-billing-worker |
| `invoicing` | novo-invoicing-service-api, novo-invoicing-service-worker, novo-invoicing-service-* crons |
| `chatbot` | chatbot-api, chatbot-cron-jobs-test-cron, chatbot-workers |
| `domestic-wire` | domestic-wire-api, domestic-wire-crons, domestic-wire-worker |
| `funding` | funding-api, funding-workers |
| `event-service` | event-service-api, event-service-workers |
| `check-service` | check-service-api, check-service-cron-jobs, check-service-workers |
| `admin` | admin-api, admin-center-api, admin-center-frontend, admin-frontend-v2 |
| `integration-tools` | integration-tools-cron, integration-tools-service-api, integration-tools-workers |
| `marketing` | marketing-service-api, marketing-service-worker |
| `recommendations` | recommendations-service, recommendations-service-cron, recommendations-worker |
| `ams` | ams-api, ams-cron, ams-workers |

## Environments

| | Prod | Dev |
|---|---|---|
| **kubectl context** | `prod` | `dev` |
| **Namespace** | `novo` | `novo` |
| **Region** | us-east-1 | us-east-1 |
| **SSO profile** | `prod` | `dev` |

All pods run an `istio-proxy` sidecar. The skill automatically targets the app container (not the sidecar) when fetching logs.

## Repo Structure

```
novo-k8s/
  SKILL.md              # Skill definition (operations, commands, output format)
  references/
    services.md         # Service catalog (short names -> pod prefixes)
```
