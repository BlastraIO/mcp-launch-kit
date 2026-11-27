# MCP Launch Kit

Free Claude Code skills for getting an MCP server through review and into
every catalogue it belongs in.

There are about fifty places to list an MCP server. A few feed the rest,
a few need your company's own identity, and the reviewed ones take weeks and
review every later edit again. These skills do the checking and the
submitting in the order that avoids the usual mistakes: a reviewer finding a
bug you could have caught, a channel nobody tracked, and copy that went
stale on half the listings.

| Skill | What it does |
|---|---|
| `mcp-readiness` | Checks the server before anyone submits it: transport, sign-in, tool titles and read-only labels, descriptions, schemas, the registry file, and what the reviewed marketplaces ask for. Reads the code, or probes a live URL without calling a tool. Reports what passes at each stage. |
| `list-your-mcp-server` | Builds the submission pack once, publishes to the Official MCP Registry first, plans the other catalogues by who may submit, submits the open ones, prepares paste-ready answers for Claude, ChatGPT, Cursor, Grok and the rest, and keeps a record of which copy went where. |
| `mcp-listing-health` | Reads each marketplace console you are signed in to (Claude's directory dashboard, OpenAI's plugin portal), your server's analytics and its sign-in records, and prints one table per marketplace: listing state, connected, signed in, used a tool, errors. Flags what quietly broke: connections that never sign in, a "Degraded" server, a version approved but never published, a stalled review. |

The catalogue list the skills read, with each one's requirements, fee and
approval time, is public and free:
https://blastra.io/directories/mcp-directories.json

## Install

In Claude Code:

```
/plugin marketplace add BlastraIO/mcp-launch-kit
/plugin install mcp-launch-kit@mcp-launch-kit
```

Or copy any folder from `skills/` into `~/.claude/skills/` (for you) or
`.claude/skills/` (for one project).

Then ask your agent: "Is our MCP server ready to submit?", "Submit our MCP
server to the catalogues." or "How are our MCP listings doing?"

## Who made this

[Blastra](https://blastra.io/directories/mcp-directories?utm_source=mcp-launch-kit&utm_medium=referral&utm_campaign=readme) lists software in the directories, catalogues
and marketplaces where buyers and AI assistants find it, and MCP servers in
the MCP catalogues. These skills are the playbook we use, from listing our
own servers in about 25 catalogues. You do not need a Blastra account to use
them.

## Licence

MIT
