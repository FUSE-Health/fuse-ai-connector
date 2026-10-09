# Installing the FUSE connectors (for Cline and other AI agents)

FUSE runs two hosted MCP servers. There is nothing to clone, build or install, and no API key or environment variable to set. Add the server entries below to the MCP settings and the setup is done.

## 1. Add the servers

Add these entries under `mcpServers` in Cline's MCP settings file:

- **VS Code extension:** open the MCP Servers panel and choose **Configure MCP Servers**.
- **Cline CLI:** edit `~/.cline/data/settings/cline_mcp_settings.json`.

```json
{
  "mcpServers": {
    "fuse": {
      "type": "streamableHttp",
      "url": "https://mcp.fusehealth.com/mcp",
      "disabled": false,
      "autoApprove": []
    },
    "fuse-docs": {
      "type": "streamableHttp",
      "url": "https://docs.mcp.fusehealth.com/mcp",
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

Notes:

- **Set `type` explicitly.** Use `"streamableHttp"`; without it, Cline falls back to the legacy SSE transport.
- **Add no `headers` and no `Authorization` value.** The FUSE AI connector signs in with OAuth, so no token goes in the file. The docs connector needs no sign-in at all.
- **Keep `autoApprove` empty for `fuse`.** The user should see each FUSE action before it runs.
- **Add only what the user wants.** If they only want developer documentation, add `fuse-docs` alone.

## 2. Sign in to the FUSE AI connector

The `fuse` server answers `401` until the user signs in. That is expected, not an error.

1. Start the sign-in:
   - **Cline CLI:** run `cline mcp` and choose **Authorize OAuth** for `fuse`.
   - **Extension:** use the authentication option Cline shows for the `fuse` server.
2. A browser opens the **Sign in with FUSE** page. The user signs in with their FUSE brand portal email and password, completes the second step (authenticator app or emailed code) and selects **Allow**.
3. A user without a FUSE brand can choose **Create a free developer account** on that page. It creates a separate 30-day developer sandbox.

The agent never handles the user's FUSE password or codes. The user enters them in the browser.

## 3. Check that it works

- **FUSE AI connector:** ask "Who am I connected to in FUSE?" The answer should be the brand's name.
- **Docs connector:** call `get_connector_info` or `search_fuse_docs` with a query such as "create checkout session".

## How the FUSE AI connector behaves

- **It asks first.** Putting a program live, making a large price change on a live program, making a burst of changes, or making a public-facing website change all require the user's confirmation in the chat. FUSE's servers enforce this. Pass the confirmation to the user and never answer it on their behalf.
- **Totals only.** Reporting covers counts and totals, never individual customers or orders.
- **No money movement.** It never moves money: no refunds, payouts or withdrawals.

More detail:

- https://www.fusehealth.com/developers/ai-connector
- https://www.fusehealth.com/developers/docs-connector

Support: support@fusehealth.com
