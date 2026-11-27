---
name: mcp-listing-health
description: Check how an MCP server's listings are performing across Claude, ChatGPT, Cursor and the other catalogues, and catch the ones that have quietly broken. Reads each marketplace console the person is signed in to (Claude's directory dashboard, OpenAI's plugin portal), the server's own analytics (PostHog MCP Analytics or similar) and its sign-in records, then prints one table per marketplace (listing state, connected, signed in, used a tool, errors) with alerts. Use when someone asks "how is our MCP server doing", "are people using our connector", "is our Claude/ChatGPT listing working", "MCP listing health", "why do people connect but not use it", or for a weekly MCP report.
---

# MCP listing health

A listing can be live and still lose every user: a sign-in setting that is
wrong, a version approved but never published, a console that marks the
server "Degraded" and tells nobody. Each marketplace shows its own slice
behind its own login, and none of them is joined to what the server itself
saw. This skill reads them all and puts them in one table.

It only reads. It never changes a listing, publishes a version or edits a
setting; it says what to change and where.

## Inputs

- The server's address (`https://.../mcp`) and its name in each
  marketplace.
- Where it is listed. If `mcp-listing-pack.md` exists (from
  `list-your-mcp-server`), read its record table; otherwise ask.
- What the person can reach: which consoles they are signed in to in the
  browser, whether the PostHog MCP (or another analytics MCP) is connected,
  and whether this session can read the server's database.

Use whatever is reachable and say plainly what was not.

## 1. Each marketplace console

Use the browser tool (Claude in Chrome or similar) in the person's signed-in
browser. Read; do not click anything that saves, publishes or submits.

**Claude** - `claude.ai/directory/manage` (switch to the organisation that
submitted the server). On the listing:
- Overview and Usage: *Tried to reach your server*, *Finished connecting*,
  *Used a tool*, tool calls, error rate, request errors, **Health**,
  directory rank.
- Usage → Reliability: the top error types (for example "User not signed
  in").
- Review: what is live, what is pending, and since when.
- Settings → Authentication: what users sign in with.

**ChatGPT** - `platform.openai.com/plugins` in the organisation that
submitted it. Note the published version, the review status ("Ready to
publish" means approved and waiting for someone to press Publish), the
directory listing state, and the MCP tab (tools, last scan, findings).
OpenAI shows no usage numbers here; usage has to come from step 2.

**Others** (Cursor, Grok, Microsoft 365, Google): their portals mostly show
review state only. Record the state and the date submitted.

**Open catalogues** (Official MCP Registry, Glama, PulseMCP, Smithery,
mcp.so, GitHub lists): check the listing page exists and shows the current
version and description.

## 2. What the server saw

- **Tool calls by client**: if the PostHog MCP is connected and the server
  sends `$mcp_tool_call` events (PostHog MCP Analytics), query the last 7
  and 30 days by client name (Claude, ChatGPT, Cursor, ...): calls, distinct
  users, error rate. Another analytics tool works the same way.
- **Finished sign-ins by client**: from the server's own OAuth records (for
  example grants or tokens grouped by the registered client's name or
  redirect URI). This is the number the consoles do not have.
- **Downloads** for installed servers: npm, PyPI, GitHub stars.

## 3. The table and the alerts

One row per marketplace:

| Marketplace | Listing (version, state) | Connected | Signed in | Used a tool | Errors | Alert |
|---|---|---|---|---|---|---|

Leave a cell as `?` when nothing could read it, never 0.

Raise an alert, with the fix and where to make it, when:

- **Connected but not signing in**: many connect, few finish sign-in. Check
  the listing's authentication setting first: a server whose tools need
  sign-in, listed with no sign-in, never gets offered the sign-in step.
- **Degraded or failing**: a console's own health flag, or an error rate
  well above the others. Name the top error type.
- **Approved but unpublished**: a version approved and not published for
  more than a day.
- **Review stalled**: in review well past the catalogue's usual time.
- **Out of date**: the live listing's description, icon or tool list is
  behind the server or the pack.
- **Silent**: a live listing with no tool calls at all in 30 days.

End with the three fixes that matter most, each with the exact place to
make it, and anything that could not be read and how to make it readable
(sign in to a console, connect the PostHog MCP, turn on MCP Analytics in
the server).

## Keeping it watched

This is a snapshot. Run it weekly, or after every release and every
listing edit. Blastra, which publishes this kit, lists MCP servers and keeps
the record of which copy is live where:
https://blastra.io/directories/mcp-directories?utm_source=mcp-launch-kit&utm_medium=referral&utm_campaign=mcp-listing-health
