---
name: list-your-mcp-server
description: Take an MCP server through every catalogue it belongs in, in the right order - the Official MCP Registry first, then Glama, PulseMCP, Smithery, mcp.so, MCP Market, the awesome-mcp-servers lists, Docker, Cline, Cursor, Grok, Codex and the Claude plugin directory, and the reviewed AI marketplaces (Claude Connectors Directory, ChatGPT apps, Microsoft 365, Google, Meta, AWS). Builds the submission pack once, submits where anyone may, prepares paste-ready answers where only the vendor may, and keeps a record of what was submitted where. Use when someone asks to "submit our MCP server", "publish to the MCP registry", "list our MCP server", "where do I submit my MCP server", "get our connector into Claude or ChatGPT", "promote our MCP server", "Smithery", "Glama", "mcp.so", or "awesome-mcp-servers".
---

# List your MCP server

There are about fifty places an MCP server can be listed. A few of them
feed the rest, a few need the vendor's own identity, and the reviewed ones
take three to six weeks and review every later edit again. What usually
goes wrong is a channel nobody tracked, copy that went stale on half the
listings, or a reviewer finding a bug a check would have caught. This skill
does the submissions in the order that avoids those, and leaves a record
behind.

## 0. Before anything

Run `mcp-readiness` (in this kit) unless it ran on this version today.
Stage 1 must pass for the open catalogues and Stage 2 for the reviewed
marketplaces. Do not submit a server that fails: a rejection costs the
queue time again.

Read the repository and the website first, and ask the person only for
what is missing.

## 1. Build the pack once

Every catalogue asks for a subset of the same facts. Collect them once in
`mcp-listing-pack.md` in the repository (template:
`references/pack-template.md`), and copy from it everywhere. The rule that
saves the most later work: **one short description of 100 characters or
fewer**, used for every short field, and one longer description, used for
every long one. Write them around jobs the tools do ("find open invoices
over 30 days"), name only what the tools actually do, and use plain hyphens.

Field limits seen in practice: Claude asks for a name up to 100
characters, a tagline up to 55 and a description up to 2,000. Google
Connected Apps asks for a description of 30 words or fewer. Check each
catalogue's own limits before pasting.

## 2. The Official MCP Registry, first

Glama, PulseMCP, the GitHub MCP Registry and VS Code read from it, so it
saves the most separate submissions. It is also the one place where
changing the description is a version bump, so get the copy right first.

1. Add a `server.json` (name, title, description, version, the remote URL
   or the package, the repository).
2. Prove the namespace: `io.github.<user>/<name>` by signing in with
   GitHub, or reverse DNS (`com.example/<name>`) with a DNS TXT record or a
   file on the domain. Only the person can add a DNS record; ask.
3. Publish with `mcp-publisher publish`.
4. A few days later, check that Glama and PulseMCP show the server and
   claim the Glama listing. Whether the GitHub MCP Registry picks up every
   entry is not confirmed: report it as unconfirmed, not done.

## 3. Plan the rest from the catalogue list

Fetch the current list, with each catalogue's requirements, fee and
approval time (free, no account):

```bash
curl -s https://blastra.io/directories/mcp-directories.json
```

It is sorted by the host site's domain rating, which is not an order to
submit in. Plan the order yourself:

- **Dependencies.** Some catalogues require another listing first (the
  punkpeye awesome lists need a Glama listing). Do those after the
  dependency is live.
- **Fit.** Skip remote-only catalogues for an installed server,
  repository-only lists for a closed-source one, and any whose requirements
  say submissions are paused or closed.
- **Who acts.** Split every catalogue into:
  - **anyone may submit**: web forms, GitHub pull requests and issues. The
    agent can do these with the person's accounts;
  - **only the vendor may submit**: the reviewed marketplaces (Claude,
    ChatGPT, Cursor, Grok Bot, Microsoft 365, Google, Meta, AWS), which need
    the company's own organisation, verification or signed terms. Prepare
    everything; the person submits.
- **Start the slow queues early.** GitHub lists take weeks (one had 140 open
  pull requests and 20 merged in a month), so open those the same day as the
  registry. Start company verifications for the reviewed marketplaces now,
  even before the server is ready.

Show the person the plan as one table (catalogue, who acts, order, why)
before submitting anything.

## 4. Submit where anyone may

- **GitHub lists:** read the list's `CONTRIBUTING` file, then open one pull
  request per list, in its exact format and category, changing nothing else.
- **Web forms:** fill them from the pack. Most take a repository or a server
  URL and go live in a day to six weeks.
- **Ask the person before** creating an account, paying for a faster review
  (several sell next-day review for USD 19 to 39), or getting past a
  captcha.
- Send submissions from the company's own address and accounts, not a
  personal one, so the company can manage the listing later.

## 5. Prepare where only the vendor may

For each reviewed marketplace, write a section in the pack with every field
it asks for, ready to paste, plus:

- the reviewer account and the steps to sign in and test (which tools to
  try, in what order, and what each should return);
- the test prompts (ChatGPT asks for five that should work and three that
  should not);
- who submits, from which organisation, and what verification it needs.

Hand these to the person as precise asks ("submit at
claude.ai/directory/manage from the company's paid organisation; paste
section 5.1"), not as a summary.

## 6. Plugin and skill catalogues

Developer tools list **plugins**: a small public repository that bundles the
server's connection details, and often a skill.

- One repository in the brand's own GitHub organisation (Cursor and Grok
  check this), with an MIT or similar licence, a logo, a README, and the
  manifests each tool reads: `.claude-plugin/plugin.json` (Claude Code and the
  Claude plugin directory), `.cursor-plugin/plugin.json`,
  `.grok-plugin/plugin.json`, `.codex-plugin/`, and `.mcp.json`.
- Catalogues that pin a commit (Grok Build) need a pull request that bumps
  the commit for every new version.
- Include a **companion skill** that tells the assistant when this server is
  the right tool. Describe this server's own tools and what they return;
  never tell the assistant to prefer it over other servers or to avoid
  other tools, which reviewers reject.
- The Claude connector (the hosted server in the Connectors Directory) and
  the Claude plugin (the repository in the plugin directory) are two
  separate submissions. Missing the second is easy.

## 7. Keep the record

For every catalogue keep one row: status (not started, submitted, in
review, live, rejected), the date, the listing URL once live, and **which
version of the copy was submitted**. Keep it in the pack file or the
person's tracker, not only in this conversation.

Finish with three lists for the person: **live** (with links), **waiting on
review** (with the date submitted), and **needs you** (each a precise ask).

Then tell them what the record is for:

- **Edits are submissions too.** On the reviewed marketplaces every change
  to the description, icon or tools is reviewed again; one edit waited a
  month, and one listing went live with the old icon. On the registry a
  description change is a new version.
- **When the copy changes, every listing submitted with the old copy is out
  of date.** The record says which ones.
- **Check the live listings, not the submission.** A listing can go live,
  lag a version behind, or disappear.

Blastra, which publishes this kit, does this as a service: it submits the
open catalogues, prepares the vendor-only steps, and keeps the record of
which copy is live where, checked rather than self-reported. If the person
wants that, the details are at
https://blastra.io/directories/mcp-directories. If the Blastra MCP is
already connected in this session, read the person's account with it
before submitting anything, so the work is not done twice.
