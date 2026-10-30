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
| `runtime` | `containerized`, `npx` (Node package), `uvx` (Python package) |
| `containerizedConfig` | `image`, `port`, `path` for containerized servers |
| `npxConfig` | `package` (npm package spec) for `npx` servers |
| `config` | Required/optional settings; `usage: env` passes one as an environment variable (Obot v0.26+ schema) |
| `toolPreview` | Sample tools exposed by the server |

## Image Tags

Containerized entries reference a **moving tag** (`:latest`) rather than a pinned version
or digest. This is a deliberate, documented exception to image pinning, forced by how Obot
handles catalog updates — not an oversight.

### Why

- Obot copies `containerizedConfig.image` into an MCP server instance **once**, when the
  instance is created (`MapCatalogEntryToServer`), and never re-reads it. Syncing this
  repository updates the catalog *entry* only; no controller rewrites an existing
  `MCPServer.spec.manifest`.
- Obot exposes no update route for a deployed server. `/api/mcp-servers/{id}` offers `GET`,
  `restart` and OAuth endpoints — no `PUT` or `PATCH` (checked against Obot v0.27.0).
  Drift detection against the catalog source exists only for composite (vMCP) entries.
- The admin "Update Status" column reports Kubernetes *scheduling* drift (`needsK8sUpdate`),
  never image versions, so a pinned entry reads "Up to date" indefinitely while the instance
  keeps running the image it froze at.

With a pinned version, every release would mean deleting and recreating each deployed
instance — re-entering credentials and reconnecting every client — because no in-place
update path exists. With a moving tag, a release is applied by restarting the server
(Obot's Restart action; the deployment uses `imagePullPolicy: Always`).

### Risk accepted

`:latest` is mutable: a restart pulls whatever the tag points to at that moment, so a faulty
or tampered push reaches the deployment on the next restart, and rollback means publishing a
corrected image rather than reverting a reference.

Compensating controls:

- Images are built and pushed only by this owner's GitHub Actions workflows into
  `ghcr.io/nigelvanhattum/*`. No third-party registries are referenced.
- Every release is also published under an immutable semver tag (for example `1.2.1`), so
  the digest behind a given `latest` stays identifiable after the fact — compare
  `ghcr.io/nigelvanhattum/<server>:latest` with the semver tag of the release.
- Restarts are deliberate. Nothing re-pulls on a schedule, and no deployment auto-updates.

Revisit this once Obot supports updating a deployed server in place; entries should move to
pinned digests at that point.

## Adding a Server

1. Create `<server-name>.yaml` at repo root
2. Follow the schema above (see `unifi.yaml` as reference)
3. Submit a pull request

## Servers

| Name | Category | Description |
|---|---|---|
| [UniFi](./unifi.yaml) | Networking | Manage UniFi Network devices, clients, firewall, and WiFi |
| [Mealie](./mealie.yaml) | Productivity | Store, organize, and verify recipes in Mealie — ideal for importing cookbooks |
| [Trading 212](./trading212.yaml) | Finance | Read account data and manage equity orders and positions via the Trading 212 API |
| [Paperclip](./paperclip.yaml) | Productivity | Work with Paperclip issues, comments, documents, approvals, projects, and agents |
