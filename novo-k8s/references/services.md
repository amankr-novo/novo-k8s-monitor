# Novo Service Catalog

Common service name shortcuts and their matching pod prefixes.

| Short Name | Pod Prefixes |
|---|---|
| credit-card | `credit-card-api`, `credit-card-workers`, `credit-card-cron`, `credit-card-transaction-processing-api`, `credit-card-transaction-processing-cron-jobs`, `credit-card-transaction-processing-workers`, `credit-card-api-db-migration` |
| onboarding-credit | `onboarding-credit-card-api`, `onboarding-credit-card-workers`, `onboarding-credit-cron-expire-cc-invitations` |
| debit-card | `debit-card-api`, `debit-card-cron-jobs`, `debit-card-workers` |
| card-management-service | `card-management-service-api`, `card-management-service-api-cronjobs`, `card-management-service-api-worker` |
| card-service | `card-service-node-cron`, `card-service-node-server`, `card-settlement-crons` |
| card-settlement-service | `card-settlement-worker` |
| novo-api | `novo-api`, `novo-mobile-api-cron`, `novo-mobile-api-db-migration`, `novo-mobile-api-worker` |
| lending | `lending-cron`, `lending-v2`, `lending-worker-v2` |
| ach | `ach-service-api`, `ach-service-cron`, `ach-service-worker`, `ach-workers-service` |
| identity | `identity-api`, `identity-auth`, `identity-role-operations-consumer`, `identity-workers` |
| onboarding | `onboarding-api`, `onboarding-admin-api`, `onboarding-admin-cron-jobs`, `onboarding-admin-frontend-v2`, `onboarding-admin-worker`, `onboarding-frontend-v2` |
| billing | `novo-billing-cron`, `novo-billing-service-api`, `novo-billing-worker` |
| invoicing | `novo-invoicing-service-api`, `novo-invoicing-service-worker`, and multiple cron pods prefixed `novo-invoicing-service-` |
| chatbot | `chatbot-api`, `chatbot-cron-jobs-test-cron`, `chatbot-workers` |
| domestic-wire | `domestic-wire-api`, `domestic-wire-crons`, `domestic-wire-worker` |
| funding | `funding-api`, `funding-workers` |
| event-service | `event-service-api`, `event-service-workers` |
| check-service | `check-service-api`, `check-service-cron-jobs`, `check-service-workers` |
| admin | `admin-api`, `admin-center-api`, `admin-center-frontend`, `admin-frontend-v2` |
| integration-tools | `integration-tools-cron`, `integration-tools-service-api`, `integration-tools-workers` |
| marketing | `marketing-service-api`, `marketing-service-worker` |
| recommendations | `recommendations-service`, `recommendations-service-cron`, `recommendations-worker` |
| ams | `ams-api`, `ams-cron`, `ams-workers` |
