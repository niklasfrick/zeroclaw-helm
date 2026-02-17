# zeroclaw-helm

[![Lint and Test Charts](https://github.com/niklasfrick/zeroclaw-helm/actions/workflows/ci.yaml/badge.svg)](https://github.com/niklasfrick/zeroclaw-helm/actions/workflows/ci.yaml)
[![Release Charts](https://github.com/niklasfrick/zeroclaw-helm/actions/workflows/release.yaml/badge.svg)](https://github.com/niklasfrick/zeroclaw-helm/actions/workflows/release.yaml)

Helm chart for deploying [ZeroClaw](https://github.com/zeroclaw-labs/zeroclaw) on Kubernetes.

## Installation

```bash
helm repo add zeroclaw https://niklasfrick.github.io/zeroclaw-helm
helm repo update
helm install zeroclaw zeroclaw/zeroclaw --set secret.apiKey="sk-..."
```

Or with an existing Kubernetes secret:

```bash
helm install zeroclaw zeroclaw/zeroclaw \
  --set secret.create=false \
  --set secret.existingSecret=zeroclaw-api \
  --set secret.existingSecretKey=API_KEY
```

### From source (development)

```bash
helm install zeroclaw ./chart/zeroclaw --set secret.apiKey="sk-..."
```

## Features

- **Gateway & Daemon modes** — run as a webhook-only server or a full autonomous runtime with channels (Telegram, Discord, etc.)
- **Config-driven** — provider, model, pairing, and bind settings via `values.yaml`
- **Persistent storage** — data volume at `/zeroclaw-data` backed by a PVC
- **Ingress & Gateway API** — first-class support for both `Ingress` and `HTTPRoute`
- **Security defaults** — runs as non-root with dropped capabilities

## Configuration

All configuration is done through Helm values. Key settings:

| Value               | Default      | Description                              |
| ------------------- | ------------ | ---------------------------------------- |
| `config.mode`       | `gateway`    | `gateway` (webhook only) or `daemon`     |
| `config.provider`   | `openrouter` | LLM provider                             |
| `config.model`      | `""`         | Model override (default: `claude-sonnet-4-5-20250929`) |
| `secret.apiKey`     | `""`         | API key (creates a Kubernetes Secret)    |
| `persistence.size`  | `10Gi`       | PVC size for `/zeroclaw-data`            |

See the full values reference and examples in [`chart/zeroclaw/README.md`](chart/zeroclaw/README.md).

## Development

```bash
# Lint
helm lint ./chart/zeroclaw

# Dry-run render
helm template zeroclaw ./chart/zeroclaw

# Install (local cluster)
helm install zeroclaw ./chart/zeroclaw --set secret.apiKey="sk-..."

# Upgrade
helm upgrade zeroclaw ./chart/zeroclaw -f my-values.yaml

# Uninstall
helm uninstall zeroclaw
```

## CI/CD

This repository uses GitHub Actions for continuous integration and release automation:

- **Pull Requests** — every PR to `main` runs chart linting ([chart-testing](https://github.com/helm/chart-testing)), template rendering with multiple value combinations, and schema validation with [kubeconform](https://github.com/yannh/kubeconform). A [kind](https://kind.sigs.k8s.io/) cluster is spun up for install testing.
- **Merge to main** — automatically determines the next [SemVer](https://semver.org/) version from [Conventional Commits](https://www.conventionalcommits.org/), updates `Chart.yaml`, packages the chart, creates a GitHub Release, and publishes to the Helm repository hosted on GitHub Pages.

### Versioning

Commit messages on `main` drive automatic version bumps:

| Commit prefix | Bump  | Example                          |
| ------------- | ----- | -------------------------------- |
| `fix:`        | Patch | `fix: correct probe path`        |
| `feat:`       | Minor | `feat: add ServiceMonitor`       |
| `feat!:` / `BREAKING CHANGE` | Major | `feat!: rename values root key` |

## Repository Setup

To enable the Helm repository on GitHub Pages:

1. Go to **Settings > Pages** in this repository.
2. Set **Source** to **Deploy from a branch**.
3. Set **Branch** to `gh-pages` and path to `/ (root)`.
4. Save.

The release workflow will create the `gh-pages` branch automatically on the first release.

## License

See [LICENSE](LICENSE).
