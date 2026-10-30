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

Ask in plain language. For example:

- "Search brainattic for our deployment checklist and summarise the steps, citing the documents."
- "Read the onboarding guide and add a section on VPN access. Follow the workspace rules."
- "Show me the revision history of the pricing page and what changed last week."
- "Restore the previous revision of the release notes."
- "Remind the team on Friday at 10:00 to review the incident log."

Good habits:

- **Start read-only.** Try a search on a document you know exists before letting the agent write.
- **Review write actions.** Some tools create, change, archive or delete content. Your client will
  usually ask before running them; read the request before approving.
- **Rely on revisions.** Every save is recorded, so a wrong edit can be inspected and rolled back.
- **Set rules once.** Write your conventions as workspace or project rules. Agents read them
  before they write.

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
