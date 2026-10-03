---
name: find-hiring-people
description: For one company, find the founders and the person most likely hiring for the target role, using LinkedIn and other public sources, and record them for outreach. Use when asked who to contact at a company or who is hiring.
---

# Find hiring people

Input: a company name and its brief at `data/briefs/<company>.md`. Output: rows in `data/people.md`. Reads `config.md` for target roles.

## Who to look for, in priority order

1. A person who posted the open role, or is named on the job page as hiring manager or recruiter.
2. The head of the function the role reports to (Head of Engineering, VP Eng, CTO for engineering roles; Head of Customer or Solutions for deployed engineer roles).
3. Founders and co-founders. At companies under about 20 people, founders usually hire.
4. A recruiter or talent partner at the company.

Pick at most 3 people per company. More than that reads as spam.

## Where to look

Web search and fetch first:
- The job posting itself (Ashby, Greenhouse, Lever, Work at a Startup pages often name the hiring manager or founder).
- The company's About or Team page.
- YC company page: lists founders with LinkedIn links.
- Launch posts, podcasts, Twitter or X bios.
- Search: `"<company>" hiring <role> site:linkedin.com/posts`.

Then the LinkedIn browser MCP:
1. Company page: `https://www.linkedin.com/company/<slug>/people/` and filter by title keywords.
2. Posts: `https://www.linkedin.com/search/results/content/?keywords=<company>%20hiring` and look for posts by employees saying they are hiring.
3. People search: `https://www.linkedin.com/search/results/people/?keywords=<company>%20<title>`. Use the snapshot headline to confirm they currently work there.
4. Check the Jobs tab on the company page. LinkedIn job posts show "Meet the hiring team" when set.

## Verifying a person

Record a person only if:
- Their current headline or experience shows the company, and
- You can say why they are the right contact (named on job page, posted the role, founder, or function head).

If you cannot verify the current employer, mark `unverified` and do not pass to `linkedin-connect`.

## Record

Append to `data/people.md`:

| Name | Role | Company | Why this person (with source URL) | LinkedIn URL | Handle | Degree (1st/2nd/3rd) | Mutual connections | Status |

Status starts as `to_connect` for 2nd and 3rd degree, `connected` for 1st. For 1st-degree people, skip the invite and go straight to `draft-outreach-message`.

## Hand off

- Invite list for `linkedin-connect`: all `to_connect` rows.
- Draft list for `draft-outreach-message`: all rows, with the company brief path.

## Do not

- Do not guess email addresses or scrape contact info.
- Do not pick people from outdated pages. A person who left the company is not a contact.
- Do not message anyone from this skill.
