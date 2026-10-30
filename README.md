# brainattic MCP server

**brainattic is the governed knowledge base that agents read and write.** Your team keeps its
knowledge in versioned documents, and AI clients connect over the
[Model Context Protocol (MCP)](https://modelcontextprotocol.io) to search, read and update them.
Workspace rules, roles and revision history decide what an agent may change.

This repository documents the hosted brainattic MCP server: what it does, how to connect a
client, and how to use it. It contains no server code. The service is hosted and operated by
Finite Software Systems Ltd.

| | |
|---|---|
| MCP endpoint | `https://app.brainattic.ai/api/mcp` |
| Transport | Streamable HTTP |
| Authentication | OAuth 2.1, discovered automatically (dynamic client registration, no API key or client secret) |
| Official MCP Registry name | `ai.brainattic/knowledge-base` |
| Discovery card | <https://brainattic.ai/.well-known/mcp/server.json> |
| Product and sign-up | <https://brainattic.ai> |
| Connect guide | <https://brainattic.ai/docs/connect> |
| Support | <https://brainattic.ai/support> · hello@brainattic.ai |

## What the server does

When you connect, your agent works inside your brainattic workspace with your own permissions.
Each person signs in with their own account, so an agent can only reach what that person can.

The tools fall into these groups:

- **Search and retrieval.** Hybrid keyword and semantic search across documents, plus passage
  retrieval for grounded answers with citations.
- **Documents.** Create, read, list and update Markdown documents. Edits can be precise: open
  an edit session, apply changes in a batch, then commit or discard them.
- **Revisions.** Every saved change is a revision. List the history, read any past revision, and
  restore an earlier one.
- **Rules.** Human-written workspace, project and group rules that the agent reads before it
  writes, so changes follow your team's conventions.
- **Projects and groups.** Organise documents, and archive or restore them.
- **Attachments.** Attach files and images to documents, and view them.
- **Skills.** Store reusable agent procedures as versioned skills with supporting files.
- **Reminders.** Set one-off or recurring reminders for workspace members.

Your client discovers the current tool list when it connects. Every tool carries MCP
annotations (`readOnlyHint`, `destructiveHint`, `openWorldHint`), so clients can ask before
running a tool that changes or deletes data.

## Requirements

- A brainattic account and access to a workspace. [Sign up](https://brainattic.ai) if you don't have one.
- An MCP client that supports remote Streamable HTTP servers with OAuth.
- A browser for the one-time sign-in.

## Setup

Every client uses the same URL: `https://app.brainattic.ai/api/mcp`. On first use the client
opens a browser window. Sign in to brainattic and approve access. You never paste a key or a
secret.

### VS Code (GitHub Copilot)

Add the server to `.vscode/mcp.json` in your project, or to your user configuration with the
**MCP: Add Server** command:

```json
{
  "servers": {
    "brainattic": {
      "type": "http",
      "url": "https://app.brainattic.ai/api/mcp"
    }
  }
}
```

Start the server from the file or the MCP view, complete the sign-in, and the brainattic tools
appear in Copilot's agent mode.

### Claude (claude.ai and Claude Desktop)

Open **Settings → Connectors → Add custom connector**. Name it `brainattic`, paste the URL and
connect. Claude walks you through the sign-in.

### Claude Code

```bash
claude mcp add --transport http brainattic https://app.brainattic.ai/api/mcp
```

Then run `/mcp` inside Claude Code and choose brainattic to sign in.

### ChatGPT

Open **Settings → Connectors**, add a custom connector with the URL above and complete the
sign-in. Custom MCP connectors need a ChatGPT plan that supports them.

### Cursor

Add to `~/.cursor/mcp.json` (all projects) or `.cursor/mcp.json` (one project):

```json
{
  "mcpServers": {
    "brainattic": {
      "url": "https://app.brainattic.ai/api/mcp"
    }
  }
}
```

### Mistral Vibe

A workspace administrator opens **Connectors**, clicks **+ Add Connector → Custom MCP
Connector**, names it `brainattic` and pastes the URL. In a chat, enable brainattic under
**Tools**.

### Gemini CLI

```bash
gemini extensions install https://github.com/rutash/brainattic-gemini-cli
```

Then run `/mcp auth brainattic` inside Gemini CLI.

### Any other MCP client

Point the client at `https://app.brainattic.ai/api/mcp` (Streamable HTTP). OAuth 2.1 is
discovered from the server, as the MCP authorization specification describes. An
unauthenticated request returns `401` with a bearer challenge that tells the client where to
sign in.

## Using it

brainattic works best as a conversation. Your agent proposes, you adjust and approve, and the
agent carries it out. Everything it writes is saved as a revision you can inspect or roll back.
The steps below are a good first session.

### 1. Start with the workspace rules

Rules are short, human-written conventions that your agent reads **before it writes**. Examples
are the language to write in, date formats, naming, status words, and what must never be written
down (secrets, credentials, personal data). Each workspace comes with a workspace rules document
from day one, so your first question should be:

> "What rules apply before writing in this workspace? Read them and explain them to me in plain words."

If the rules are filled in, the agent summarises them and explains how it will follow them. If
they are still empty, it tells you so, which is your cue for step 2.

### 2. Adapt the rules to how you work

Shape the rules in conversation instead of writing them from scratch:

> "Suggest workspace rules for us: language, date format, document naming, status vocabulary and
> a never-write-down list. Base them on how I work. Draft them for my review and don't save anything yet."

Push back, add, remove and refine until the draft fits. Then say *"Save these as the workspace
rules."* Workspace rules need an admin role. Editors can set rules for a project or a group, and
an admin can also edit any rules in the web app under **Admin → Rules**. From the next write on,
every connected agent reads the approved rules, not just the one you talked to.

### 3. Ask how brainattic fits together

Ask the agent to explain the model using examples from your own work:

> "Explain how workspaces, projects, groups, documents, rules and skills relate in brainattic.
> Use examples from my own work."

The short version:

| Concept | What it is | Example |
|---|---|---|
| **Workspace** | The boundary around your team's knowledge and permissions. Each person has a role in it: Viewer, Editor or Admin. | "Acme Ltd" |
| **Project** | A document's optional *primary home*. A document belongs to at most one project, and a project can have a hand-written overview ("start here") document. | "Customer onboarding" |
| **Group** | A cross-cutting label. A document can carry several groups, or none, across projects. Groups are not sub-projects. | "customer-facing", "needs-review" |
| **Document** | A versioned Markdown page. Every saved change is a revision. | "Onboarding checklist" |
| **Rules** | Conventions at workspace, project or group scope. Agents read them in that order, and the more specific scope wins a direct conflict. | "Every procedure starts with a one-line purpose" |
| **Skills** | Reusable step-by-step procedures for agents, which rules can recommend. | "How to file meeting notes" |

### 4. Ask how to put brainattic to use, in the context of your work

Your agent already knows a lot about you: earlier conversations, the projects you've discussed,
files you've shared, and whatever memory your client keeps. Let it connect that to brainattic:

> "Based on what you know about my work from our conversations, how would I best put brainattic
> to use? What should live here, what should stay out, and where would it save us the most time?"

A good answer names concrete candidates, such as recurring decisions, procedures people keep
asking about, customer facts, or project histories. It also explains why each one belongs in a
shared, governed knowledge base rather than in a single chat.

> **Note:** your agent's memory lives in your AI client, not in brainattic. brainattic sees only what
> your agent reads or writes through the connector, under your account's permissions.

### 5. Let the agent propose a first project

Ask for a complete proposal before anything is created:

> "Propose a first brainattic project for me: its name and purpose, a project overview, the groups
> it needs, project rules, and three example documents drafted from things we've already worked on.
> Show me the whole plan and create nothing until I approve."

A typical proposal looks like this:

```text
Project:   Customer onboarding. Everything a new customer needs from signing to go-live.
Overview:  "Start here": scope, owners, where the checklist lives, how to propose changes.
Groups:    customer-facing · internal-only · needs-review
Rules:     Title pattern "<Customer> – <topic>". Every procedure starts with a one-line purpose
           and lists its owner. Dates as YYYY-MM-DD. Never record customer passwords or API keys.
Examples:  1. Onboarding checklist (customer-facing)
           2. Kick-off call notes template (internal-only)
           3. Lessons learned from our last three onboardings (needs-review)
```

Adjust it ("merge those two groups", "add a group for contracts", "make the rules stricter about
dates"), then approve. The agent creates the project, groups, overview and rules, and files the
example documents. Open the web app to see the result, check each document's revision history, and
ask for any change in plain language.

### 6. Keep the rhythm

From then on, work the same way: **the agent proposes, you adapt and approve, it executes.**
Everyday requests look like this:

- "Search brainattic for our deployment checklist and summarise the steps, citing the documents."
- "File these meeting notes in the right project with a clear title and the right groups."
- "Read the onboarding guide and add a section on VPN access. Follow the rules."
- "Show me what changed in the pricing page last week, and restore the previous revision."
- "Remind the team on Friday at 10:00 to review the incident log."

Good habits:

- **Start read-only.** Try a search on a document you know exists before letting the agent write.
- **Review write actions.** Some tools create, change, archive or delete content. Your client will
  usually ask before running them; read the request before approving.
- **Rely on revisions.** Every save is recorded, so a wrong edit can be inspected and rolled back.
- **Grow the rules as you go.** When you correct the agent twice about the same thing, ask it to
  propose a rule for it.

## Troubleshooting

- **Sign-in window doesn't open:** run the client on a machine with a local browser. OAuth
  sign-in doesn't suit an unattended SSH session.
- **"Unauthorized" after it worked before:** sign in again from your client's MCP or connector
  settings.
- **Tools missing:** check that the brainattic server is connected and enabled in the client,
  then reconnect.
- **Permission denied:** your brainattic role decides what you can do. Ask a workspace
  administrator; the client can't override it.

## Ownership and licence

brainattic is a product of **Finite Software Systems Ltd.**
(ФИНИТ Софтуер Системс ЕООД), Company No. 175276896, Sofia, Bulgaria.
Website: <https://brainattic.ai> · Company: <https://finite-soft.com> · Contact: hello@brainattic.ai

© 2026 Finite Software Systems Ltd. This documentation is the property of Finite Software
Systems Ltd and is licensed under the
[Creative Commons Attribution 4.0 International licence (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/)
(full text in [`LICENSE`](LICENSE)). You may copy and adapt it, including commercially, provided
you credit Finite Software Systems Ltd and link to this repository.

The brainattic service itself is proprietary and governed by the
[Terms of Service](https://brainattic.ai/terms). The name "brainattic" and its logo are not
licensed under CC BY 4.0.
