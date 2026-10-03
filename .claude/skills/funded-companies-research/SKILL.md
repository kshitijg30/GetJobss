---
name: funded-companies-research
description: Find companies with recent funding rounds or recent YC batches that fit the user's target roles, and write a short research brief per company. Use when asked to research new funding, find startups to apply to, or build a target company list.
---

# Funded companies research

Builds `data/companies.md` and one brief per company in `data/briefs/<company>.md`. Reads `config.md` for batches, stages, sectors, roles and `funding_window_months`.

## Step 1: Find candidates

Use web search and fetch. Try several sources, because no single one is complete:
- YC company directory filtered by batch: `https://www.ycombinator.com/companies?batch=Summer%202025` (change the batch). Check which of these have a Jobs link.
- Work at a Startup: `https://www.workatastartup.com/jobs` (filter by role, batch).
- Funding news: search `"raises" "seed" OR "Series A" <sector> <month year>`, plus TechCrunch, Crunchbase News, Axios Pro Rata, Entrepreneur First and a16z/Sequoia/Accel portfolio news pages.
- LinkedIn posts: search `"we just raised" <sector>` in the browser MCP if web search is thin.
- Hacker News "Who is hiring" for the current month.

Keep a company only if all hold:
- Funding or a YC batch inside `funding_window_months`.
- Stage is in `funding_stages` (or a listed YC batch).
- Sector overlaps `sectors`, or the company lists a role matching `roles`.
- You can name the source URL for the funding claim.

## Step 2: Write the company list

`data/companies.md`, one row per company:

| Company | Website | Funding (amount, stage, date) | Investors | Source URL | Hiring page | Fit (high/med/low) |

Funding amounts and dates are recorded only as the source states them. If two sources disagree, write both and flag it. If a number is not found, write `unknown`. Do not estimate.

## Step 3: Brief per company

For each `high` or `med` fit, read their site, docs or changelog, latest launch post and one or two blog posts or founder posts. Write `data/briefs/<company>.md`:

```
# <Company>
- What they build (2 sentences, from their own words)
- Who it is for
- Funding: <amount, stage, date, investors> (<source URL>)
- Recent launches or changes (with dates and URLs)
- Open roles that match config.roles (with URLs), or "none listed"
- Product details worth referencing in a message (3 bullets, each with the page it came from)
- Where the user's resume could fit (2 bullets, each tied to a resume item)
- Open questions (things you could not verify)
```

Anything in the brief must be traceable to a page you read. Mark inferences as `Inference:`.

## Step 4: Hand off

Return the ranked list (top 10 first) with one line each. Then tell the orchestrator which companies are ready for `find-hiring-people`.

## Do not

- Do not include companies from a funding rumor with no source.
- Do not copy marketing claims as facts about traction. Quote them as the company's claim.
- Do not stop at the first source. Cross-check funding in at least two places when possible.
