# Changelog

This file documents all notable changes to the [n8n-helm-chart](https://github.com/8gears/n8n-helm-chart/). The release numbering uses [semantic versioning](http://semver.org).

## 2.0.2 (jjnyn release)

- **Task runners (external mode):** Added optional `runners` section so you can deploy the `n8nio/runners` image for n8n v2+ Code node execution. You can set `runners.image.repository` and `runners.image.tag` (defaults to chart appVersion). When `runners.enabled` is true, the main service exposes port 5679 (task broker) and a separate `runners` deployment is created. See [n8n task runners docs](https://docs.n8n.io/hosting/configuration/task-runners/).
- Chart maintainer: **jjnyn** added as maintainer for this release.

The full changelog for the upstream chart is at https://github.com/8gears/n8n-helm-chart/releases/ and https://artifacthub.io/packages/helm/open-8gears/n8n?modal=changelog
