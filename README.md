# FUSE AI connector

![FUSE](logo.png)

[FUSE](https://www.fusehealth.com) is the commerce platform for health and wellness brands. With the FUSE AI connector, you run your FUSE brand by chatting with an AI assistant:

- build and price programs (the offerings you sell)
- check that a program is ready to sell
- create coupons and checkout links
- update your storefront
- see how the business is doing

This repository holds the connection details, plugin manifests and skills for FUSE's two hosted MCP servers. It contains no server code: both servers are run by FUSE Health, Inc., and there is nothing to install or build locally.

## The two connectors

| | FUSE AI connector | FUSE docs connector |
|---|---|---|
| Address | `https://mcp.fusehealth.com/mcp` | `https://docs.mcp.fusehealth.com/mcp` |
| What it's for | Running your FUSE brand | Building software that works with FUSE |
| Sign-in | OAuth 2.1 "Sign in with FUSE" (dynamic client registration, PKCE). No API key to paste. | None |
| Access | Reads and changes your brand, within your account's permissions | Read-only, public documentation |
| Transport | Streamable HTTP | Streamable HTTP |
| Guide | [AI connector](https://www.fusehealth.com/developers/ai-connector) | [Docs connector](https://www.fusehealth.com/developers/docs-connector) |

For the FUSE AI connector, sign in with your FUSE brand portal login. To try it without a brand, choose **Create a free developer account** on the Sign in with FUSE page. That gives you a separate 30-day developer sandbox, not your real brand.

## Set up

### Claude Code (plugin)

This repository is a Claude Code plugin marketplace. Inside Claude Code, run:

```
/plugin marketplace add FUSE-Health/fuse-ai-connector
/plugin install fuse-health@fuse-health
```

From your shell, the same two steps are:

```bash
claude plugin marketplace add FUSE-Health/fuse-ai-connector
claude plugin install fuse-health@fuse-health
```

The plugin adds both connectors and two skills. To finish:

1. Run `/mcp`.
2. Select `plugin:fuse-health:fuse` and choose **Authenticate**.
3. Sign in with FUSE.

The docs connector needs no sign-in.

If you added FUSE earlier with `claude mcp add`, remove that entry so you don't see the same tools twice.

To add only the connectors, without the plugin:

```bash
claude mcp add --transport http --scope user fuse https://mcp.fusehealth.com/mcp
claude mcp add --transport http --scope user fuse-docs https://docs.mcp.fusehealth.com/mcp
```

### Claude (web, desktop and mobile)

1. Go to **Customize → Connectors** and choose **Add custom connector**.
2. Name it `FUSE` and paste `https://mcp.fusehealth.com/mcp`.
3. Leave the OAuth client ID and secret empty, select **Add**, then **Connect**, and sign in with FUSE.

On Team and Enterprise plans, an Owner adds it once in **Organization settings → Connectors**. See [Claude setup](https://www.fusehealth.com/developers/ai-connector/clients#claude).

### Cursor

This repository is also a Cursor plugin. Until it is listed in the Cursor Marketplace, add the servers to `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "fuse": { "url": "https://mcp.fusehealth.com/mcp" },
    "fuse-docs": { "url": "https://docs.mcp.fusehealth.com/mcp" }
  }
}
```

### Cline

Follow [llms-install.md](llms-install.md), or ask Cline to set it up from that file.

### ChatGPT, Codex, VS Code, Gemini CLI and others

Step-by-step guides for each app are on [Connect FUSE to your AI tools](https://www.fusehealth.com/developers/ai-connector/clients). Any app that can add a remote MCP server with OAuth sign-in works. Use the address above and choose Streamable HTTP and OAuth.

## What the FUSE AI connector can do

| Area | Examples |
|---|---|
| Programs | List, create and duplicate programs; add products; set prices; check readiness; put a program live |
| Catalog | Browse products and templates; find discontinued products and pharmacy price changes; check which states a product ships to |
| Coupons | List, create and change coupons |
| Checkout | Create checkout links for a program |
| Storefront | Colours, headline, menus and footer; create and edit pages other than your homepage (saved as drafts) |
| Account | Brand profile, onboarding checklist, team, payout and SMS setup, fees |
| Business totals | Orders and revenue over time, by program and by state; projected revenue |

Your AI app shows the full tool list when you connect. It changes as FUSE opens more of the brand portal to AI.

### It asks first

The connector stops and asks you in the chat before it:

- puts a program live (or back on sale)
- makes a large price change on a live program
- makes a burst of many changes
- changes anything public-facing on your website

The check runs on FUSE's servers, not in the AI. A change that needs confirming isn't applied until you approve that exact change.

### What it won't do

- **Move money.** No refunds, payouts or withdrawals.
- **Show individual customers or orders.** Reporting is totals and counts only, and small numbers are grouped so they can't be traced to a person.
- **Change API keys or invite people** to your team.
- **Upload images.**
- **Publish your storefront** or change its visibility or custom domain. Do those in the FUSE brand portal.

## What the docs connector can look up

The docs connector has six read-only tools:

| Tool | What it returns |
|---|---|
| `search_fuse_docs` | Search results across FUSE's developer guides and API reference |
| `list_api_operations` | The documented API operations |
| `get_api_operation` | One operation: URL, auth, parameters, body, responses and examples |
| `get_api_schema` | One named schema, such as `Program` |
| `get_integration_guide` | A guide: getting started, sandbox and go-live, checkout, embedded checkout, webhooks or AI connector setup |
| `get_connector_info` | How to connect the FUSE AI connector |

It covers FUSE's public developer documentation only. It does not document endpoints that return customer information.

## Skills in the plugin

| Skill | What it does |
|---|---|
| `fuse-launch-program` | Walks a brand through building a program from a template, adding products, setting prices, checking readiness and putting it live. Every confirmation goes to you. |
| `fuse-storefront-integration` | Uses the docs connector to wire a custom storefront to FUSE checkout links or embedded checkout. It keeps secret keys server-side and confirms each sale on the server. |

## What this plugin runs and sends

- **No local code.** The plugin runs no scripts, hooks, commands or local servers.
- **Two remote connections.** It declares two remote MCP servers, and your AI app connects to them over HTTPS:
  - `https://mcp.fusehealth.com/mcp`, after you sign in on FUSE's sign-in page
  - `https://docs.mcp.fusehealth.com/mcp`, with no sign-in
- **Nothing else.** The plugin sends nothing anywhere else and contains no keys or credentials.

Files in this repository:

| File | Purpose |
|---|---|
| `.mcp.json` | Both connectors, for Claude Code and other apps that read `.mcp.json` |
| `mcp.json` | Both connectors, for the Cursor plugin |
| `.claude-plugin/` | Claude Code plugin and marketplace manifests |
| `.cursor-plugin/` | Cursor plugin manifest |
| `skills/` | The two skills |
| `llms-install.md` | Setup steps for Cline and other agents |

## Privacy

- **The FUSE AI connector** never sends personal information about your customers to the AI. Ask about a specific customer or order, and it tells you that isn't available.
- **The docs connector** has no sign-in, reads no account data and keeps no record of your questions beyond standard hosting logs.
- **Policies.** Using either connector is covered by the [FUSE Privacy Policy](https://www.fusehealth.com/privacy-policy) and [Terms of Service](https://www.fusehealth.com/terms-of-service).

To disconnect, remove FUSE from your AI app's connector settings.

## Support

Email [support@fusehealth.com](mailto:support@fusehealth.com). If something looks wrong, include:

- what you asked, in your exact words
- what the AI did or said (a screenshot helps)
- what you expected
- which AI app you used, and roughly when

We especially want to hear about a change that happened without asking you, or a confirmation that didn't match what you asked for.

## License

The files in this repository are released under the [MIT License](LICENSE). That covers the manifests, configuration files, skills and documentation here, and nothing else.

The hosted FUSE services these files point to are proprietary to FUSE Health, Inc. and are not covered by the MIT License. That includes the FUSE AI connector, the docs connector and the FUSE platform. They are governed by the [FUSE Terms of Service](https://www.fusehealth.com/terms-of-service) and [Privacy Policy](https://www.fusehealth.com/privacy-policy). The MIT License grants no rights in the FUSE name or logo.
