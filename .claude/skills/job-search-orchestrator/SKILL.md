---
name: job-search-orchestrator
description: Run the whole job search flow. Reads config, then dispatches separate agents to research recently funded companies, find founders and hiring people, connect with them on LinkedIn through a browser MCP, and draft one message per person from the resume. Use when asked to start or continue job applications or outreach.
---

# Job search orchestrator

You coordinate. The work is done by agents running the other skills. Skills live next to this one: `funded-companies-research`, `find-hiring-people`, `linkedin-connect`, `draft-outreach-message`.

## Step 0: Setup (do this yourself)

All paths are relative to the repo root (the folder that holds `config.example.md`).

1. If `config.md` does not exist, copy `config.example.md` to `config.md`.
2. Find the resume. Use `resume_path` if it points to a real file. Otherwise use the first PDF, DOCX or Markdown file in `resume/`. Otherwise ask the user once for a path (anywhere on disk, for example the Downloads folder). Save the path to `config.md`.
3. If `name`, `current_role` or `strongest_impact_points` are blank, read the resume and fill them. Write `data/resume_facts.md` at the same time (format in `draft-outreach-message`).
4. If `roles` or `locations` are blank, ask once in a single message, suggesting roles from the resume. Fill `sectors` from the resume if blank. Write the answers to `config.md`.
5. Create `data/`, `data/briefs/`, `data/drafts/` and `data/tracker.md` if missing.
6. Check the browser MCP: navigate to `https://www.linkedin.com/feed/` and snapshot. If no browser tool is available, tell the user the `browsermcp` server from `.mcp.json` is not loaded and to restart Claude Code in this folder and approve it. If the tool exists but reports no connected tab, ask the user to install the Browser MCP extension (https://browsermcp.io), open LinkedIn logged in, click the extension icon and press Connect. Then continue.
7. Tell the user in 3 lines what you are about to do and the daily caps from `config.md`.

## Step 1: Research agent

Dispatch one agent (Agent tool, general-purpose) with this prompt:

> Run the `funded-companies-research` skill using `config.md`. Write `data/companies.md` and briefs in `data/briefs/`. Return the top 10 companies, one line each, with the source for the funding claim.

Wait for it. Show the user the top 10 and ask which to pursue, or take the top 5 if the user said to proceed without asking.

## Step 2: People agents (one per company, in parallel)

For each chosen company, dispatch one agent:

> Run the `find-hiring-people` skill for <company>. Use `data/briefs/<company>.md`. Append verified people to `data/people.md`. Return the names, roles and why each is the right contact. Do not send invites or messages.

Parallel agents must not both drive the same browser tab. If the browser MCP has one tab, run the people agents one after another, and do the web-search-only parts in parallel.

## Step 3: Drafting agents (one per person, in parallel)

Drafting needs no browser, so run these in parallel. For each person with status `to_connect` or `connected`:

> Run the `draft-outreach-message` skill for <person> at <company>. Use `config.md`, the resume at <resume_path>, and `data/briefs/<company>.md`. Save the draft to `data/drafts/`. Do not send.

## Step 4: Connect agent (single agent, serial)

LinkedIn is one logged-in session, so use exactly one agent for browser actions:

> Run the `linkedin-connect` skill on every `to_connect` row in `data/people.md`, respecting `max_invites` in `config.md`. Verify each invite shows Pending. Update `data/tracker.md`. Stop on any stop condition in the skill and report.

## Step 5: Review with the user

Show a table: company, person, role, invite status, draft path. Ask for approval on each draft. Send only approved drafts, following `draft-outreach-message` Step 6. When `auto_send_messages` is true, still show the drafts first for the first run.

## Step 5b: Existing connections

Run this at the start of each day and whenever invites were accepted. One agent, serial, because it uses the browser:

> Run the `message-connections` skill using `config.md`. Skip anyone with an earlier message thread. Research each person's company first. Follow `approve_first_n` and the daily cap. Return the table of results.

## Step 6: Continue

Next day or next run: read `data/tracker.md`, skip anyone already handled, and repeat from Step 1 or Step 2 for new companies. When a pending invite is accepted (profile shows 1st degree), move that person to drafting and sending.

## Rules for every agent

- Use only facts from the resume and from pages actually read.
- Verify before recording a result. An invite is sent only if the profile shows Pending. A message is sent only if it appears in the conversation.
- Respect the caps and delays in `config.md`.
- Stop and report on CAPTCHA, unusual-activity notices, weekly invite limit, or a lost browser tab.
- Never send a message the user has not approved while `auto_send_messages` is false.
- Never put the user's resume contents or contact details into a message beyond what the template contains.
- Keep reports short: what was done, what failed, what needs the user.
