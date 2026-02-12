# Changelog

This file documents all notable changes to the [n8n-helm-chart](https://github.com/8gears/n8n-helm-chart/). The release numbering uses [semantic versioning](http://semver.org).

## 2.0.4 (jjnyn release)

- **Task runners (official sidecar model):** Runners now run as a sidecar on the main pod and on each worker pod (per [n8n docs](https://docs.n8n.io/hosting/configuration/task-runners/)). Standalone runners deployment removed; broker port 5679 is no longer exposed on the Service (sidecar uses localhost:5679).
- **Defaults with extraEnv override:** `N8N_RUNNERS_BROKER_LISTEN_ADDRESS` defaults to `0.0.0.0` on main/worker (override via `main.extraEnv` / `worker.extraEnv`). Runners sidecar defaults: `N8N_RUNNERS_TASK_BROKER_URI=http://localhost:5679`, `N8N_RUNNERS_AUTO_SHUTDOWN_TIMEOUT=15` (override via `runners.extraEnv`).
- **Custom launcher config:** Optional `runners.configMapName` and `runners.configMapKey` to mount a custom `n8n-task-runners.json` (e.g. for allowlisting JS/Python packages). When no ConfigMap is set, pass allowlist env vars via `runners.extraEnv` (`NODE_FUNCTION_ALLOW_BUILTIN`, `NODE_FUNCTION_ALLOW_EXTERNAL`, `N8N_RUNNERS_STDLIB_ALLOW`, `N8N_RUNNERS_EXTERNAL_ALLOW`).
- **N8N_RUNNERS_AUTH_TOKEN:** Documented setting via `main.secret.n8n.runners.auth_token` (recommended, so n8n and sidecar share the same token via envFrom) or via `main.extraEnv` / `runners.extraEnv` (e.g. `valueFrom.secretKeyRef`).

## 2.0.3 (jjnyn release)

- **Task runners:** When `runners.enabled` is true, the chart now sets on main and worker pods: `N8N_RUNNERS_MODE=external` and `N8N_RUNNERS_BROKER_LISTEN_ADDRESS=0.0.0.0` so the broker listens on all interfaces and runner pods can connect (fixes "Waiting for task broker to be ready..." when broker was bound to 127.0.0.1).

## 2.0.2 (jjnyn release)

- **Task runners (external mode):** Added optional `runners` section so you can deploy the `n8nio/runners` image for n8n v2+ Code node execution. You can set `runners.image.repository` and `runners.image.tag` (defaults to chart appVersion). When `runners.enabled` is true, the main service exposes port 5679 (task broker) and a separate `runners` deployment is created. See [n8n task runners docs](https://docs.n8n.io/hosting/configuration/task-runners/).
- Chart maintainer: **jjnyn** added as maintainer for this release.

The full changelog for the upstream chart is at https://github.com/8gears/n8n-helm-chart/releases/ and https://artifacthub.io/packages/helm/open-8gears/n8n?modal=changelog
