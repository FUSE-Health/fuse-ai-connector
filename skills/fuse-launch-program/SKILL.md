---
name: fuse-launch-program
description: Walk a FUSE brand through building a program (an offering it sells), pricing it, checking it is ready to sell and putting it live, using the signed-in FUSE AI connector. Use when someone asks to create, set up, price, launch or "go live" with a program or offering on FUSE, or asks why a program isn't ready.
---

# Launch a program on FUSE

This skill uses the **FUSE AI connector**, the signed-in MCP server at `https://mcp.fusehealth.com/mcp` (server name `fuse` in this plugin). It acts on one FUSE brand account, with that account's permissions. A program is an offering the brand sells: a set of products with prices and a checkout.

Use the connector's own tool names and descriptions. Don't guess a tool name. If you're unsure which tool fits, the connector has a built-in help tool that explains what it can do and why a request was refused.

## Ground rules

These rules hold for every step:

- **Confirmations come from FUSE and belong to the user.**
  - FUSE's servers stop certain changes until the user approves them in the chat:
    - putting a program live or back on sale
    - a large price change on a live program
    - a burst of many changes
    - a public-facing website change
  - When a tool returns a confirmation request, show the user its exact sentence and wait for their reply. Never answer it yourself.
  - Never split a change into smaller pieces, or spread it out over time, to avoid a confirmation.
  - Only turn confirmations off for the day ("yes, and don't ask again today") if the user says so in their own words.
- **Act only on what the user asked.** Before you create, change or publish anything, say what you are about to do. Ask when the program, product or price is ambiguous.
- **No customer records.** The connector reports totals and counts, never individual customers or orders. Don't ask for, collect or repeat personal details about the brand's customers. If someone asks about a specific customer or order, tell them it isn't available through the connector.
- **No money movement.** The connector does not issue refunds, payouts or withdrawals, so don't offer to.
- **On a refusal, explain it and stop.** Use the help tool to find out why, then tell the user. Don't retry with a workaround.

## Steps

### 1. Check the connection

1. Ask the connector who you are connected to, and tell the user the brand name it returns.
2. If it is a free developer sandbox, tell the user that changes there don't affect a live brand.
3. If the `fuse` server needs sign-in:
   - **Claude Code:** run `/mcp`, select `fuse` and choose **Authenticate**.
   - **Claude or Cursor apps:** connect FUSE from the connectors settings.
   - The user signs in on the **Sign in with FUSE** page.

### 2. Start from a template

1. List the program templates and help the user pick one.
2. Prefer a template over a blank program. A template carries the questionnaire a program needs before it can sell.
3. Create the program as a **draft** with the name the user gives.
4. To copy an existing program, use duplicate rather than rebuilding it.

### 3. Add products

1. Show the user the products they can add.
2. Add only the products they choose.
3. If the catalog flags a discontinued product, a pharmacy price change or limited state availability, tell the user before adding it.

### 4. Set prices

1. Confirm each price with the user, including the amount and the billing period, such as "$199 a month".
2. Then set it.
3. On a program that is already live, a large change will ask for confirmation. Pass that confirmation to the user and wait for their reply.

### 5. Check readiness

1. Run the connector's readiness check.
2. Report what's missing in plain language, one line per item, with the next action for each.
3. Fix only the items the user asks you to fix.

### 6. Put it live, only when asked

1. If the user asks to put the program live, call the tool.
2. The connector will ask for confirmation. Show its sentence to the user unchanged and wait for their reply.
3. Never treat an earlier "yes" as approval of a later change.

### 7. Test a purchase

1. Offer to create a checkout link for the program so the user can test buying it.
2. In a developer sandbox, checkout is simulated and no card is charged.

### 8. Hand over what the connector can't do yet

Tell the user these steps happen in the FUSE brand portal:

- **Publish the storefront.** A FUSE-hosted storefront (at `yourbrand.fusehealth.com` or the brand's own domain) is published from the portal's **Portal** editor. This applies only if FUSE hosts the storefront. A brand that sells from its own website uses checkout links or the FUSE API instead.
- **Storefront visibility and custom domain.** Both are set in the portal.
- **Homepage design.** Design the homepage in the Portal editor, not through the AI. Use the connector for other pages, such as About or FAQ, which it saves as drafts.

## Finish

End with a short summary:

- the program name and status (draft or live)
- the products and prices set
- any readiness items still open
- the checkout link, if one was created

## Reference

- Connector guide: https://www.fusehealth.com/developers/ai-connector
- Confirmations: https://www.fusehealth.com/developers/ai-connector#confirmations
- Privacy and limits: https://www.fusehealth.com/developers/ai-connector#privacy
