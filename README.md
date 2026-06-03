# logcli-bridge

> ⚠️ **Prototype** Rough edges, no tests, arguments and functionalitymay change. Not production-ready.

A hacked Bash/Python wrapper that lets [`logcli`](https://grafana.com/docs/loki/latest/query/logcli/) talk to Loki when you can't reach Loki's API directly. It runs a small local HTTP shim that forwards `logcli` requests through either:

- **Grafana's authenticated datasource proxy** (default) — handles auth via OAuth or browser cookies, so you query Loki the same way Grafana does.
- **`kubectl port-forward`** (`--kube`) — forwards straight to an in-cluster Loki, bypassing Grafana.

## Requirements

- `logcli`
- `python3` (stdlib only — runs the local shim and reads Chrome cookies)
- `kubectl` (only for `--kube` mode)

## Usage

```bash
# Through Grafana (OAuth login by default)
logcli-bridge --host grafana.example.com labels
logcli-bridge -H grafana.example.com query --since=1h --limit=100 '{namespace="monitoring"}'

# Direct to in-cluster Loki via kubectl port-forward
logcli-bridge --kube query --since=1h --limit=100 '{namespace="monitoring"}'
```

Everything after the wrapper options (or after `--`) is passed verbatim to `logcli`.

Run `logcli-bridge --help` for the full list of options and environment variables.

## Authentication (Grafana mode)

- `--oauth` (default): browser-based OAuth login, cached at `~/.cache/logcli-bridge/session.json`.
- `--no-oauth`: reuse Chrome's Grafana session cookies.
- `--cookie`: supply a `Cookie` header value directly.
