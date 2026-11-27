---
name: mcp-readiness
description: Check whether an MCP server is ready to submit to the MCP catalogues and the AI assistant marketplaces (the Official MCP Registry, Smithery, Glama, mcp.so, the Claude Connectors Directory, ChatGPT apps, Cursor, Grok Bot, Microsoft 365), and list what a reviewer would reject before anyone submits. Reads the server's code when it is in the repo, or probes a live server URL with the two requests every MCP client sends first (initialize and tools/list), never calling a tool. Use when someone asks "is my MCP server ready", "MCP review checklist", "prepare my connector for the Claude directory", "ChatGPT app review", "why was my MCP server rejected", "before we submit our MCP server", or "check our MCP server".
---

# MCP readiness

Most MCP review rejections are found by a reviewer weeks after the
submission, and each fix restarts the queue. This skill finds them first.
It reports what passes, what fails and what could not be checked, stage by
stage, so the person knows which catalogues the server can go to today.

It never calls a tool on the server. It reads code, and it sends only
`initialize` and `tools/list`, which is what any client sends when it
connects.

## Inputs

Ask only for what is missing:

- **The server:** the repository (if this session is in it), the remote
  URL (`https://.../mcp`), or the npm, PyPI or Docker package.
- **Who it is for:** the person's customers sign in to it (OAuth), or it
  works without an account, or both.
- **Where they want to be:** the open catalogues only, or also the reviewed
  marketplaces (Claude, ChatGPT, Grok Bot, Cursor, Microsoft 365). The
  reviewed ones raise the bar (Stage 2 below).

## 1. Read the server

**In the repository:** find where tools are registered (search for
`tools/list`, `registerTool`, `server.tool(`, `@mcp.tool`, `ListToolsRequest`)
and read every tool: name, title, description, input schema, annotations
and what the handler actually does. Find the HTTP route that serves the
endpoint and the OAuth routes, if any.

**From a live URL only:** probe it.

```bash
URL=https://example.com/mcp
curl -si "$URL" -X POST \
  -H 'content-type: application/json' \
  -H 'accept: application/json, text/event-stream' \
  -d '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2025-06-18","capabilities":{},"clientInfo":{"name":"readiness-check","version":"1.0"}}}'
```

Keep the `mcp-session-id` response header if one comes back and send it on
the next request, then:

```bash
curl -s "$URL" -X POST \
  -H 'content-type: application/json' \
  -H 'accept: application/json, text/event-stream' \
  -H "mcp-session-id: $SESSION" \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/list"}'
```

A `401` is normal for a server that needs sign-in. Read its
`WWW-Authenticate` header and follow it (step 2.2) instead of reporting the
server as down. A server that answers `tools/list` without a token and
returns only some tools is using lazy sign-in, which is fine.

Also try `initialize` with the newest protocol version the person's
clients use. A server that answers the old version and rejects the new one
with a 400 has failed review before.

## 2. The checks

Mark each one **pass**, **fail** (with the fix) or **unknown** (with what
would settle it). Unknown is not pass.

### 2.1 Transport and address

- Hosted servers: a public `https://` address using Streamable HTTP. The
  reviewed marketplaces take nothing else. Installed (stdio) servers still
  fit the registry, community directories and GitHub lists.
- The same address for every user, and one that will not change. Every
  listing points at it.
- `GET`, `HEAD` and an unknown JSON-RPC method on the endpoint answer with
  an error, not a 500. A 500 on the endpoint itself has been a review
  finding.

### 2.2 Sign-in

Only if any tool reads a user's own data.

- OAuth 2.x. Pasting an API key is not accepted by the reviewed marketplaces
  for user data.
- A `401` from the endpoint carries `WWW-Authenticate` pointing to
  protected-resource metadata (`/.well-known/oauth-protected-resource...`,
  RFC 9728), which names the authorization server, whose own metadata
  (`/.well-known/oauth-authorization-server`, RFC 8414) lists the
  endpoints.
- Dynamic client registration (RFC 7591), or Client ID Metadata Documents,
  so Claude and ChatGPT can register themselves. PKCE with S256.
- The redirect URIs the clients use are accepted, for example
  `https://claude.ai/api/mcp/auth_callback`,
  `https://chatgpt.com/connector_platform_oauth_redirect` and
  `http://localhost:<any port>/callback` for local clients.
- **Each customer sees only their own data.** Early servers are often built
  on a staff login that can search every customer. Reviewers sign in as a
  customer and ask for another one by name, so check the code path, not
  only the happy case.

### 2.3 Tools

For every tool:

- A `title`, and the annotations `readOnlyHint` and `destructiveHint` (and
  `openWorldHint` where it applies). Claude and Microsoft 365 require them.
  Check them against what the handler does: a tool that only reads must say
  `readOnlyHint: true`, or careful clients and the tool-risk maps treat it as
  a write.
- A name and title a customer would understand ("Find open invoices", not
  "api_read").
- A description of **what the tool does and returns**, under 1,024
  characters. A longer one has failed review.
- **No instructions about other servers.** A description or server
  instruction telling the assistant to call this tool before any other
  server's tools, or never to use another tool, is rejected by Claude's
  review. Describe this tool; let the assistant decide.
- An input schema that validates: no empty string in an `enum`, required
  fields listed, every property described.
- The whole tool list is light enough to load. Count the characters of the
  `tools/list` response and divide by four for a rough token count; several
  thousand tokens is common, much more than that costs every assistant that
  connects.

### 2.4 What the catalogues read about the server

- A `server.json` for the Official MCP Registry, with a namespace the
  person can prove (`io.github.<user>/<name>` from GitHub, or reverse DNS
  such as `com.example/<name>` from a DNS TXT record or a file on the
  domain).
- A server card under `/.well-known/mcp/` if the server is hosted (several
  catalogues and crawlers look there).
- The same short description everywhere: one line of 100 characters or
  fewer that names only what the tools do.

### 2.5 What the reviewed marketplaces also ask for

- Privacy policy and terms pages, live. A support email someone reads. A
  documentation page that says what each tool does and how to connect.
- A **reviewer account**: real-looking data, no two-factor, and a password
  that still works in two months. Reviews can start weeks after submitting.
- Test prompts and their expected answers. ChatGPT asks for five that
  should work and three that should not.
- A short demo video (ChatGPT).
- Cursor and Grok Bot: a permissive licence such as MIT on any open-source
  part, no charge for the plugin itself, and the plugin repository in the
  brand's own GitHub organisation.
- Company checks that take days and need the person, not the server:
  OpenAI organisation verification, a paid Claude organisation to submit
  from, Microsoft Partner Center, AWS seller registration, Meta business
  verification. Start them now if those marketplaces matter.

### 2.6 Being used, not only listed

Once an assistant has the server, it still has to think of using it. A
short **companion skill** that tells the assistant when the server is the
right tool (for example "when the person writes an email, fetch their saved
signature") makes the difference: in one test, an assistant with the skill
called the server every time, and without it wrote "[Your name]" instead.
Note whether one exists; `list-your-mcp-server` covers shipping it.

## 3. The report

One table per stage, then the next three fixes.

| Stage | Unlocks | Ready? |
|---|---|---|
| 0. Start now | Nothing yet; company checks are often the slowest part | items 2.5 company checks, policies, support |
| 1. Open catalogues | Official MCP Registry, community directories, GitHub lists, developer-tool catalogues | 2.1, 2.3, 2.4 |
| 2. Reviewed marketplaces | Claude, ChatGPT, Grok Bot, Cursor, Microsoft 365, AWS, Meta | everything, including 2.2 and 2.5 |

For each failing check give the file and line (when the code is here) and
the exact change. Offer to make the code changes; do not make them without
asking.

Say plainly what was not checked, for example "tools behind sign-in were
not listed because there is no reviewer account".

## 4. Keeping it ready

Readiness is a snapshot of one version. A new tool, a renamed tool or a
changed description after approval makes every listing describe the old
server, and on the reviewed marketplaces each edit is a new review. Re-run
this skill before every release that changes the tool list.

When the server is ready, `list-your-mcp-server` (in this kit) takes it
through the catalogues in the right order. Blastra, which publishes this
kit, does that work as a service and keeps the record of which copy is live
where: https://blastra.io/directories/mcp-directories
