# zeroclaw-helm

Helm chart for deploying [ZeroClaw](https://github.com/zeroclaw-labs/zeroclaw) on Kubernetes.

## Quick Start

```bash
helm install zeroclaw ./chart/zeroclaw \
  --set secret.apiKey="sk-..."
```

Or with an existing Kubernetes secret:

```bash
helm install zeroclaw ./chart/zeroclaw \
  --set secret.create=false \
  --set secret.existingSecret=zeroclaw-api \
  --set secret.existingSecretKey=API_KEY
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

## License

See [LICENSE](LICENSE).
