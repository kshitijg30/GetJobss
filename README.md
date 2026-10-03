# GetJob

Claude Code skills that run a job search through your browser. They find recently funded startups, find the founders and the people hiring, send LinkedIn connection invites, and draft one short message per person from your resume. Messages are saved as drafts and only sent after you approve them.

## Setup

You need Claude Code, Node.js 18 or newer, and Chrome with LinkedIn logged in.

1. Paste this into Claude Code from any folder:

   ```
   Set up https://github.com/kshitijg30/GetJobss
   ```

   The agent follows the steps under "For the agent doing setup" below.

2. Install the [Browser MCP extension](https://browsermcp.io) in Chrome. Open LinkedIn, click the extension icon and press Connect. Do not press Cancel on the "started debugging this browser" banner, because that disconnects it.

3. Start Claude Code in the cloned folder and approve the `browsermcp` server if asked:

   ```bash
   cd ~/GetJobss && claude
   ```

   Then say `start my job search`. On the first run it asks for your resume (or uses the file you dropped in `resume/`) and your target roles and locations. It fills in everything else from the resume.

Manual alternative to step 1: `git clone https://github.com/kshitijg30/GetJobss ~/GetJobss`. Nothing else needs to be installed.

## For the agent doing setup

If a user asks you to set up this repo, do these steps and nothing else:

1. Clone it: `git clone https://github.com/kshitijg30/GetJobss ~/GetJobss`. If `~/GetJobss` already exists, run `git -C ~/GetJobss pull` instead.
2. Check `node --version` is 18 or newer. If Node is missing, tell the user to install it from https://nodejs.org. Do not install it without asking.
3. Ask the user where their resume is. Copy it into `~/GetJobss/resume/`. If they don't have the file handy, skip this; the first run will ask.
4. Tell the user to do steps 2 and 3 of Setup above: connect the Browser MCP extension on a LinkedIn tab, then run `cd ~/GetJobss && claude` and say `start my job search`.

Do not edit `config.md` or create `data/` during setup. The `job-search-orchestrator` skill does that on its first run.

## What is in the repo

| Path | What it does |
|---|---|
| `.claude/skills/job-search-orchestrator/` | Entry point. First-run setup, then dispatches the other skills to separate agents and keeps the tracker. |
| `.claude/skills/funded-companies-research/` | Finds companies with new funding rounds or recent YC batches and writes a brief for each. |
| `.claude/skills/find-hiring-people/` | For one company, finds the founders and the person most likely hiring. |
| `.claude/skills/linkedin-connect/` | Sends connection invites through the browser and verifies each one shows Pending. |
| `.claude/skills/draft-outreach-message/` | Writes one short message per person from the resume, the company brief and the template. |
| `.claude/skills/message-connections/` | Goes through existing connections, skips anyone already messaged, and drafts or sends a personal message. |
| `.mcp.json` | Registers the Browser MCP server (`npx @browsermcp/mcp`) for this folder. |
| `.claude/settings.json` | Enables that server and allows web search, web fetch and browser tools without a prompt each time. |
| `config.example.md` | Defaults. Copied to `config.md` on first run. |
| `config.md`, `resume/`, `data/` | Your settings, resume and results. Gitignored. |

## Defaults you may want to change

Edit `config.md` after the first run.

- Invites go out without a note. Messages are drafted, not sent, until you approve them (`auto_send_messages: false`).
- Daily caps are low on purpose: 10 invites and 15 messages. LinkedIn limits weekly invites and can flag fast automated clicking.
- The skills stop and report on a CAPTCHA, an unusual-activity notice, the weekly invite limit, or a lost browser tab.

## Using Claude in Chrome instead

The skills work with the Claude in Chrome tools too. If you use that extension, you can skip Browser MCP. The `browsermcp` server will fail to connect to a browser, and the skills use whichever browser tools are available.
