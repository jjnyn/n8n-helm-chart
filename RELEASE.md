# Releasing the Helm chart

Use these steps to release the chart so your repo (or others) can install it via Helm.

## Using this chart from eks-cluster-manifests

If you deploy via **eks-cluster-manifests** (or similar) with a Git repo + version, point the manifest at your fork and the release tag:

```yaml
app_name: n8n
chart_name: n8n
chart_url: "https://github.com/YOUR_ORG/n8n-helm-chart.git"   # your fork (e.g. jjnyn/n8n-helm-chart)
chart_version: "2.0.2"   # must match a Git tag you push (e.g. v2.0.2 or 2.0.2)
namespace: n8n
project: default
release_name: n8n
enabled: "true"
```

Then create and push a Git tag so the tool can check out that version:

```bash
git tag v2.0.2   # or 2.0.2 if your tool expects no "v" prefix
git push origin v2.0.2
```

Use the same tag format your tool expects (e.g. `2.0.2` vs `v2.0.2`). The chart’s version in `Chart.yaml` is already `2.0.2`.

## 1. Resolve dependencies (required once)

From the **repo root**:

```bash
cd charts/n8n && helm dependency build && cd ../..
```

This fetches the Valkey subchart. If you don't need Valkey, you can skip and use `--set valkey.enabled=false` when installing.

## 2. Package the chart

From the **repo root**:

```bash
make package
# or:
helm package charts/n8n
```

This creates `n8n-2.0.2.tgz` (version from `charts/n8n/Chart.yaml`).

---

## Option A: Publish to an OCI registry (e.g. GitHub Container Registry)

Best if you use GHCR, GCR, or another OCI registry.

1. **Log in to the registry** (example: GitHub Container Registry):

   ```bash
   helm registry login ghcr.io -u YOUR_GITHUB_USERNAME -p YOUR_GITHUB_PAT
   ```

2. **Push the packaged chart** (tag must match chart version):

   ```bash
   helm push n8n-2.0.2.tgz oci://ghcr.io/YOUR_ORG_OR_USERNAME
   ```

   Example: `helm push n8n-2.0.2.tgz oci://ghcr.io/jjnyn`  
   Chart will be available as `ghcr.io/jjnyn/n8n:2.0.2`.

3. **Install from your repo:**

   ```bash
   helm install my-n8n oci://ghcr.io/YOUR_ORG_OR_USERNAME/n8n --version 2.0.2
   ```

---

## Option B: Publish as a Helm HTTP repo (e.g. GitHub Pages)

Best if you want a classic `helm repo add` + `helm install` flow.

1. **Package the chart** (see step 2 above).

2. **Create a `charts` directory** and copy the tgz:

   ```bash
   mkdir -p release-charts
   cp n8n-2.0.2.tgz release-charts/
   ```

3. **Generate the Helm repo index:**

   ```bash
   helm repo index release-charts --url https://YOUR_GITHUB_USERNAME.github.io/n8n-helm-chart
   ```

   This creates/updates `release-charts/index.yaml`.

4. **Publish `release-charts/`** to GitHub Pages (or any static host):
   - Either push `release-charts/` to a branch like `gh-pages` and enable Pages for that branch.
   - Or copy `release-charts/*` into the root of a repo used for Pages (e.g. `YOUR_USERNAME.github.io/n8n-helm-chart/`).

5. **Add the repo and install:**

   ```bash
   helm repo add jjnyn https://YOUR_GITHUB_USERNAME.github.io/n8n-helm-chart
   helm repo update
   helm install my-n8n jjnyn/n8n --version 2.0.2
   ```

---

## Optional: Create a GitHub Release

To attach the chart to a GitHub release (e.g. `v2.0.2`):

1. Create a new release in the GitHub UI (or with `gh release create v2.0.2 n8n-2.0.2.tgz`).
2. Upload `n8n-2.0.2.tgz` as an asset.

Users can download the tgz and install with:

```bash
helm install my-n8n ./n8n-2.0.2.tgz
```

---

## Summary (quick copy-paste)

**Package only:**

```bash
cd /path/to/n8n-helm-chart
helm dependency build charts/n8n
helm package charts/n8n
```

**OCI (e.g. GHCR):**

```bash
helm registry login ghcr.io -u YOUR_USERNAME -p YOUR_PAT
helm push n8n-2.0.2.tgz oci://ghcr.io/YOUR_ORG/n8n
# Install: helm install my-n8n oci://ghcr.io/YOUR_ORG/n8n --version 2.0.2
```

**HTTP repo (index for GitHub Pages):**

```bash
mkdir -p release-charts && cp n8n-2.0.2.tgz release-charts/
helm repo index release-charts --url https://YOUR_USERNAME.github.io/n8n-helm-chart
# Then push release-charts/ to GitHub Pages; add repo and install as above.
```
