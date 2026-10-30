# One knowledge base, every agent

brainattic doesn't care which AI agent you use. Any client that speaks the
[Model Context Protocol](https://modelcontextprotocol.io) connects to the same endpoint,
`https://app.brainattic.ai/api/mcp`, and works on the same documents, under the same rules.
Each part of a company keeps its own agent and tools, and they still work from one shared,
governed source of truth.

We built brainattic at **Finite Software Systems** because we needed exactly this. Our sales,
analysts, developers, testers, operations and support people each work in a different AI
client. Knowledge kept scattering across private chat histories that nobody else could see,
check or build on. brainattic is the shared attic they all read from and write to.

| Who | Typical agent | What they do in brainattic |
|---|---|---|
| Sales | ChatGPT | File customer requests and call notes, check what was promised |
| Business analysis | Claude | Turn requests into analysis, scope and acceptance criteria |
| Developers | VS Code with GitHub Copilot, Claude Code, Cursor | Read the analysis, record design decisions, update status |
| Testing | Claude Code, Cursor | Derive test plans from acceptance criteria, record results |
| Operations | Hermes, or any terminal agent | Follow release procedures, record what shipped and when |
| Support | Whatever they prefer | Answer customers from the latest documentation, with citations |

Nobody has to switch tools. The shared parts are the documents, the rules, and the history of
who changed what.

## A change request, end to end

*An illustrative example of how a team at Finite Software Systems works with brainattic. The
customer, "Acme Logistics", is fictional.*

**1. Sales takes the request (ChatGPT).** After a call, our salesperson tells ChatGPT: *"Acme
wants their delivery report exported as CSV with their own columns. File it as a change
request."* Before writing, the agent reads the rules for the *Acme* project and the
*change-requests* group. It files **"Acme – CR-014 Delivery export"** using the group's template
(requester, business reason, deadline, status `new`), and sets a reminder for the analyst.

**2. Analysis (Claude).** The business analyst asks Claude: *"Analyse CR-014. Check what we
already know about Acme's reporting and any past decisions on exports."* Claude searches the
workspace, finds an earlier export decision and Acme's integration notes, and drafts the
analysis: scope, acceptance criteria, impact on existing reports, and two open questions for the
customer. It's tagged *needs-review*. After the next call, the salesperson's ChatGPT adds the
customer's answers to the same document, so the history shows both steps.

**3. Implementation (VS Code with GitHub Copilot).** The developer reads the analysis and
acceptance criteria straight from brainattic inside VS Code and writes the code, which still
lives in git as usual. Copilot then records a short design note: *"Column mapping stored per
customer; why not a global setting."* It links the note to CR-014 and moves the status to
`in progress`, using the status words the workspace rules define.

**4. Testing (Claude Code).** The tester asks: *"Write a test plan for CR-014 from its acceptance
criteria."* After the run, the agent records the results and attaches a screenshot of the
export. A failed case goes back into the CR document, and the fix shows up as the next revision.

**5. Shipping (Hermes).** Operations asks Hermes to run the release using the team's *release
procedure* skill. Hermes follows the steps, files the release notes, records the version and
date, and sets a reminder to check Acme's usage a week later.

**6. Support (any agent).** A customer-facing how-to is drafted from the release notes and tagged
*customer-facing*. When Acme asks a question two months later, the support agent answers from the
current document and cites it.

Five people used five different agents, and the request never left one place. Anyone can open
CR-014 and read the whole story: what the customer asked, what was decided and why, what was
built, how it was tested, and when it shipped. Every step is a revision, and every action is in
the audit log.

## A new team member joins

A new developer joins the Acme project. An admin invites her to the workspace, and she connects
her own agent (Gemini CLI) in a couple of minutes, with no keys to copy. Her first question:

> "Explain the Acme project: the rules, where things live, and what happened with CR-014 and why."

She gets a cited walkthrough of the project overview, the conventions, the decision history and
the open work. When she files her first document, her agent reads the same rules as everyone
else's, so it lands in the house style from day one. Getting her up to speed takes one
conversation, with no onboarding deck or hand-me-down chat history.

## What is built in, and what is convention

- **Status and workflow are conventions.** Status words such as `new`, `in progress` and
  `shipped` are defined in the rules, and *needs-review* is a group. brainattic records every
  change but does not enforce a sequence of steps.
- **Reminders** are the built-in way to hand work on to a colleague.
- **Code, tests and deployments** stay in each team's own tools (git, CI and so on). brainattic
  holds what the team knows about them, and the agents do the work.
- **Skills** such as a release procedure are written by the team for its own way of working.

To set up your own workspace this way, start with [Using it](../README.md#using-it) in the README.

---

© 2026 Finite Software Systems Ltd. Licensed under
[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). See [Ownership and licence](../README.md#ownership-and-licence).
