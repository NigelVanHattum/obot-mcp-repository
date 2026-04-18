# MCP Catalog

An [Obot](https://obot.ai)-compatible catalog of MCP (Model Context Protocol) servers.

## Structure

Each MCP server is defined by a single YAML file at the root of the repository.

### YAML Fields

| Field | Description |
|---|---|
| `name` | Display name |
| `shortDescription` | One-line summary |
| `description` | Full markdown description shown in UI |
| `metadata.categories` | Category tag (e.g. Networking, Communication) |
| `metadata.allow-multiple` | Whether multiple instances are allowed |
| `icon` | URL to icon image |
| `repoURL` | Source GitHub repository |
| `runtime` | `containerized` or `node` or `python` |
| `containerizedConfig` | `image`, `port`, `path` for containerized servers |
| `env` | List of required/optional environment variables |
| `toolPreview` | Sample tools exposed by the server |

## Adding a Server

1. Create `<server-name>.yaml` at repo root
2. Follow the schema above (see `unifi.yaml` as reference)
3. Submit a pull request

## Servers

| Name | Category | Description |
|---|---|---|
| [UniFi](./unifi.yaml) | Networking | Manage UniFi Network devices, clients, firewall, and WiFi |
