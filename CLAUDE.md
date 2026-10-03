# GetJob

Job search agent. Skills are in `.claude/skills/`. Personal state lives in `config.md`, `resume/` and `data/`, all gitignored.

- To start or continue the search, use the `job-search-orchestrator` skill. Its Step 0 does first-run setup: creates `config.md` from `config.example.md`, finds the resume, fills identity fields from it, and checks the browser connection.
- Browser actions go through the `browsermcp` server in `.mcp.json` (Browser MCP). Claude in Chrome tools work the same way if Browser MCP is not loaded.
- Do not commit `config.md`, `resume/` or `data/`.
