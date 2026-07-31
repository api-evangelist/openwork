---
name: openwork-connect-external-mcp
description: Register and connect an external MCP server to an OpenWork Den organization and run its tools.
api: openwork:den-api
base_url: https://api.openworklabs.com
auth: "Authorization: Bearer <session-token>  (or x-api-key: <den-api-key> for org-key routes)"
operations:
  - postV1McpConnectionsDiscover
  - postV1McpConnectionsResolve
  - getV1McpConnectionsPresets
  - postV1McpConnections
  - getV1McpConnectionsByConnectionIdConnectStart
  - getV1McpConnectionsByConnectionIdTools
  - postV1McpConnectionsByConnectionIdToolsCall
  - putV1McpConnectionsByConnectionIdAccess
  - deleteV1McpConnectionsByConnectionId
---

# Connect an external MCP server in OpenWork Den

Wire an external MCP server into an OpenWork organization so members' agents can use its tools.

## Steps

1. **Discover requirements** — `POST /v1/mcp-connections/discover`
   (`postV1McpConnectionsDiscover`), or resolve a free-form query with
   `POST /v1/mcp-connections/resolve` (`postV1McpConnectionsResolve`), or pick a
   preset via `GET /v1/mcp-connections/presets` (`getV1McpConnectionsPresets`).
2. **Register the connection** — `POST /v1/mcp-connections` (`postV1McpConnections`).
   A duplicate returns `409 connection_conflict`.
3. **Complete OAuth (if required)** — `GET /v1/mcp-connections/{connectionId}/connect/start`
   (`getV1McpConnectionsByConnectionIdConnectStart`); the shared callback
   finalizes the handshake. Missing OAuth config returns
   `mcp_oauth_configuration_required` with the callback and client-metadata URLs.
4. **List and call tools** — `GET /v1/mcp-connections/{connectionId}/tools`
   (`getV1McpConnectionsByConnectionIdTools`) then
   `POST /v1/mcp-connections/{connectionId}/tools/call`
   (`postV1McpConnectionsByConnectionIdToolsCall`). A not-ready connection
   returns `409 connection_not_ready`; a failed run returns
   `502 tool_execution_failed` with a `diagnostic`.
5. **Scope access** — `PUT /v1/mcp-connections/{connectionId}/access`
   (`putV1McpConnectionsByConnectionIdAccess`) to set who in the org may use it.
6. **Remove** — `DELETE /v1/mcp-connections/{connectionId}` (`deleteV1McpConnectionsByConnectionId`).

## Rules

- Errors use the `{ "error": "<code>", "message": "...", "diagnostic": ... }`
  envelope; inspect `diagnostic` on `502` tool/handshake failures.
- No idempotency key; check `GET /v1/mcp-connections` before re-registering.
- Marketplace-managed connections reject edits with `marketplace_managed`.
- See `../conventions/openwork-conventions.yml` and `../errors/openwork-problem-types.yml`.
