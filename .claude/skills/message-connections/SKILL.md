---
name: message-connections
description: Go through the user's existing LinkedIn connections, skip anyone already messaged, research each person's company, and send a short personal message based on the resume. Use when asked to message connections, follow up on accepted invites, or reach founders and hiring people the user is already connected to.
---

# Message connections

For people who are already 1st-degree connections. Reads `config.md` for resume, identity, message template, caps and switches. Uses the browser MCP on a logged-in LinkedIn tab.

## Step 1: Build the candidate list

1. Open `https://www.linkedin.com/mynetwork/invite-connect/connections/`. Sort by "Recently added" so newly accepted invites come first.
2. Snapshot, scroll and snapshot again to read names, headlines and profile URLs. Read at most 60 connections per run.
3. Keep a person only if their headline suggests a useful contact: founder or co-founder, CTO, head of engineering, engineering manager, recruiter or talent, or a senior engineer at a company in the target sectors in `config.md`. Skip people with no company in the headline, students, and anyone whose company is the user's own employer.
4. Write the kept people to `data/connections.md`: name, headline, company, profile URL, handle.

## Step 2: Check whether they were messaged before

Do all three checks. Skip the person if any one finds an earlier message in either direction.

1. `data/tracker.md` and `data/drafts/`: any row for this person with status `sent`.
2. LinkedIn inbox search: open `https://www.linkedin.com/messaging/`, type the person's name into the search box, and snapshot. A result means a thread exists.
3. Open the person's profile and click Message. The compose overlay shows prior messages if a thread exists. If it shows earlier messages, close the overlay and skip.

Mark skipped people in `data/tracker.md` as `already_messaged` with the date. Do not reply to threads that the other person started, and tell the user about any unread reply from a target person instead.

## Step 3: Research the company

For each remaining person, check `data/briefs/<company>.md`. If it exists and has at least 2 specific product details with sources, use it. Otherwise research:
- The company site, docs, changelog, latest launch or blog post, and the role the person has there.
- What the product does in one sentence, and one detail that connects to the user's work.
- Whether they are hiring for roles in `config.md` (careers page, Work at a Startup, LinkedIn Jobs).

Save or update the brief in `data/briefs/`. If nothing specific can be found, skip the person and mark `needs_research`. Never send a message built on a guess about the company.

## Step 4: Draft

Use the message template in `config.md` (the "connection message" block). Match this shape:

```
Hey <first name>, I'm a <current role>, where I <one line of what the user does at work>.

Came across <Company> and what you're building around <specific thing from the brief>. It's close to <something from the resume>, especially around <detail>. <A question about hiring or contributing>. I'd love to learn more about what you're building and see if there's a way I could contribute.
```

Constraints:
- Two short paragraphs, under 90 words total.
- The company detail must come from the brief. The resume detail must come from `data/resume_facts.md` (create it from the resume at `resume_path` if missing).
- One light ask about the team or role. No attachments, no links unless `config.md` has one.
- Plain words. No em dashes, no "I hope you're well", no "passionate".
- Each message is written for that one person. Two messages must not share the same second paragraph.

Save to `data/drafts/<company>-<person>.md` with status `draft`.

## Step 5: Send

Reading `config.md`:
- `approve_first_n` (default 3): show the first N drafts to the user and wait. After the user approves N drafts without edits, continue on your own for the rest of the run, within caps.
- If `auto_send_messages` is false, stop after drafting and wait for approval on every draft.

To send one message:
1. Open the person's profile and click `Message` to open the compose overlay. Snapshot to confirm the overlay's recipient name matches the person.
2. Click the message box and type the message. Snapshot to confirm the full text is in the box and nothing else is.
3. Click Send. If the click times out, snapshot and check whether the message appears before retrying, so it is never sent twice.
4. Verify by reopening the conversation: the message must appear as sent. Only then set status to `sent` in `data/tracker.md` with the date and time.
5. Wait a random number of seconds in `config.md` `seconds_between_actions`.

If the Enter key is the only way to send and the key press errors with `No tab with given id`, ask the user to reconnect the extension and resume from this person.

## Caps and stop conditions

- At most `max_messages` per day from `config.md`, counting sent rows in `data/tracker.md`.
- Stop and report on a CAPTCHA, an unusual-activity notice, a "message limit" or restriction banner, or three failures in a row.

## Do not

- Do not message anyone who has an existing thread with the user.
- Do not send a draft that was not shown to the user when approval is required.
- Do not claim funding, traction or hiring status that is not in the brief.
- Do not exaggerate what the user did at work; use only the resume facts.

## Report

A table: person, company, result (`sent`, `already_messaged`, `needs_research`, `failed`), and the one-line link used between the resume and the company. Then today's count against the cap.
