---
name: draft-outreach-message
description: Draft one short, individual LinkedIn message per person from the user's resume, the company research brief and a fixed template, and save it as a Markdown file. Use when asked to write outreach, draft LinkedIn messages or prepare messages for founders or hiring managers.
---

# Draft outreach message

One message per person. Never a bulk template with only the name swapped. Reads `config.md` for resume path, identity, impact points and the template.

## Inputs

- A person row from `data/people.md`.
- The company brief at `data/briefs/<company>.md`.
- The resume. Path comes from `config.md` (`resume_path`). If it is blank or the file is missing, ask the user once: "Where is your resume? A path anywhere on disk is fine." Write the answer back to `config.md`.

## Step 1: Read the resume once

Read the file (PDF, DOCX or Markdown) and write `data/resume_facts.md` if it does not exist yet:
- Current role and company.
- 3 to 5 impact items, each as: what was built or scaled, the scale or number, the tools.
- Skills.

Use only what the resume says. If an impact item has no number, do not invent one.

## Step 2: Check the brief is deep enough

The brief must contain at least 2 "Product details worth referencing" with source pages. If not, run more research on that company (site, docs, changelog, launch posts) and update the brief first. If research still finds nothing specific, do not draft. Mark the person `needs_research`.

## Step 3: Pick the match

Choose the one resume impact item closest to what the company builds, and the one product detail it connects to. Write down the link in one line before drafting, for example: "cut voice agent failure rate at my current job, relevant to their voice agent evals".

## Step 4: Draft

Follow the template in `config.md`. Constraints:
- 3 or 4 lines, under 70 words.
- Starts with the person's first name.
- Says who the user is in one clause.
- One concrete impact item from the resume.
- One specific thing about the company that came from the brief.
- One honest observation, a concrete sentence and not praise.
- One light ask. No attachments, no "I'd love to pick your brain".
- Plain words. No em dashes, no "I hope this finds you well", no "passionate", no "game-changing".
- If the user is replying to an open role, name the role.

If the person is not yet a connection and an invite note is wanted, also write a connection note under 280 characters using the same facts.

## Step 5: Save

Write `data/drafts/<company>-<person>.md`:

```
# <Person>, <Role> at <Company>
- Profile: <URL>
- Degree: <1st/2nd/3rd>
- Link between resume and company: <the one-line link>
- Sources used: <urls from brief>

## Message
<the message>

## Connection note (optional)
<note>

## Status
draft
```

## Step 6: Sending

- If `auto_send_messages` is false (default): stop after saving. Show the user the drafts and wait for approval of each one. Update Status to `approved` only when the user says so.
- If true and the person is a 1st-degree connection: open `https://www.linkedin.com/messaging/compose/?recipient=<urn>` via the Message button on their profile, type the message, take a snapshot to confirm the text is in the box, click Send, then reopen the conversation to confirm it appears. Mark `sent`.
- For non-connected people, the message goes out only after they accept the invite, or as the invite note.

## Do not

- Do not send the same text to two people.
- Do not make claims about the company that are not in the brief.
- Do not exaggerate resume items or add numbers that are not in the resume.
- Do not mention funding amounts unless the brief has a source for them.
