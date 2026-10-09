---
name: fuse-storefront-integration
description: Wire a custom storefront or website (Next.js, React, any server) to FUSE checkout links or embedded checkout, using the public FUSE docs connector as the source of truth for endpoints, fields and guides. Use when a developer asks how to sell FUSE programs from their own site, create FUSE checkout sessions or links, embed FUSE checkout, list a brand's programs, or handle FUSE webhooks.
---

# Connect a custom storefront to FUSE checkout

This skill uses the **FUSE docs connector**, the public, read-only MCP server at `https://docs.mcp.fusehealth.com/mcp` (server name `fuse-docs` in this plugin). It needs no sign-in and holds no account data.

Its tools:

| Tool | Use it for |
|---|---|
| `search_fuse_docs` | Find the right guide, operation or schema |
| `get_integration_guide` | Read a guide. Topics: `getting-started`, `sandbox-and-go-live`, `checkout`, `elements-embed`, `webhooks`, `ai-connector` |
| `list_api_operations` | List the documented API operations, by tag |
| `get_api_operation` | Get one operation: URL, auth, parameters, body, responses and example requests |
| `get_api_schema` | Get one named schema, such as `Program` or `CheckoutSessionCreate` |
| `get_connector_info` | Get connection details for the signed-in FUSE AI connector |

**Look it up before you write it.**
- Before writing code against an endpoint, call `get_api_operation` for it and use the paths, fields and error codes it returns.
- Before relying on a behaviour, read the guide that covers it.
- Don't invent endpoints, fields, headers or limits. If the docs don't cover something, say so.

## How FUSE checkout works

Confirm the details in the `checkout` guide.

- **FUSE hosts the questionnaire and payment.** A sale runs on a FUSE-hosted page, so the brand's code never handles questionnaire answers or card details. Don't build forms that collect either.
- **A sale starts with a checkout session.**
  - The brand's server creates one for a program with a secret API key.
  - The response includes an `embedUrl`. Send the shopper to it (a checkout link) or mount it on the page with FUSE Elements (the `elements-embed` guide).
- **Sessions are short-lived and single-use.** Create a new session for each purchase attempt. Never create links at build time or cache them.
- **The program must be live and ready.** The `checkout` guide lists the errors returned for a program that can't sell yet.

## Steps

### 1. Keys

Read the `getting-started` guide.

- **Secret key** (`fuse_live_…`, or `fuse_test_…` in a developer sandbox):
  - Keep it in a server-side environment variable only.
  - Never put it in browser code, a mobile app, a repository, or a variable with a client-exposed prefix such as `NEXT_PUBLIC_`, `VITE_` or `EXPO_PUBLIC_`.
- **Publishable key** (`fuse_pk_…`): it can only create checkout sessions, so it is the only key that may appear in page source.
- **Never print, log or commit a key.** Read keys from the environment and tell the developer which variable to set.

### 2. Sandbox first

1. Read the `sandbox-and-go-live` guide.
2. Develop against a free developer sandbox. Its checkout is simulated, so no card is charged.

### 3. List the programs to sell

1. Look up the programs list operation with `get_api_operation` (for example `GET /api/v1/programs`).
2. Call it from the server and render only what the storefront needs, such as names, descriptions and prices.

### 4. Create a checkout session per purchase attempt

1. Write a server route that creates the session. Look up `POST /checkout/sessions` and the `CheckoutSessionCreate` schema first.
2. Return only `embedUrl` (and `sessionId`, if the page needs it) to the browser.
3. Use only the optional `metadata` keys and `successUrl` rules documented in the guide.

### 5. Choose how the checkout opens

- **Checkout link:** redirect to `embedUrl`.
- **Embedded checkout:** follow the `elements-embed` guide, including allowlisting the site's origins and handling events. Treat browser events as a display signal only, not proof of purchase.

### 6. Confirm the outcome on the server

Use either source:

- the session status operation, or
- webhooks, read from the `webhooks` guide.

Verify each webhook signature exactly as the guide describes, and make the handler idempotent.

### 7. Handle errors

Map the documented error codes to clear messages. Examples are a program with no questionnaire, a program no longer taking enrollments, and rate limits on publishable-key session creation.

## Boundaries

- **This skill covers selling flows only.**
  - It covers listing programs, checkout sessions, embedded checkout and webhook confirmation.
  - It doesn't cover order or customer endpoints. Those return personal information and are outside the docs connector.
- **Don't store or log personal information** about the brand's customers in the storefront.
- **This skill doesn't change the brand's programs or prices.** If the user wants to create programs, set prices or put a program live by chatting, use the FUSE AI connector at `https://mcp.fusehealth.com/mcp`. It asks the user before public or store-changing actions, and those confirmations are always the user's to answer.

## Reference

- Docs connector page: https://www.fusehealth.com/developers/docs-connector
- API overview: https://www.fusehealth.com/developers/api
- Interactive API reference: https://api.fusehealth.com/api/v1/docs
