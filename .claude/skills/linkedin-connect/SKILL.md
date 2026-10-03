---
name: linkedin-connect
description: Send LinkedIn connection invites through a browser MCP (Browser MCP or Claude in Chrome) and verify each one shows Pending. Use when asked to connect with founders, YC batch members, hiring managers or any list of LinkedIn profiles.
---

# LinkedIn connect

Sends invites to a list of people, or finds people by a search and invites them. Every invite is verified. Reads `config.md` for limits and switches.

## Input

One of:
- A list of profile URLs or vanity handles (from `data/people.md`).
- A search query, for example `founder "YC S25"`.

## Before starting

1. Confirm the browser MCP is connected: navigate to `https://www.linkedin.com/feed/` and take a snapshot. If it errors with "No connection to browser extension", stop and ask the user to click Connect in the extension on their LinkedIn tab.
2. Read `config.md`: `max_invites`, `seconds_between_actions`, `send_invites_without_note`.
3. Count invites already sent today from `data/tracker.md`. Stop at `max_invites`.

## Finding people from a search

Navigate to:
```
https://www.linkedin.com/search/results/people/?keywords=founder%20%22YC%20S25%22&network=%5B%22S%22%5D
```
- `network=["S"]` limits to 2nd-degree, which skips people already connected.
- Change `keywords` for other batches or roles: `"YC W25"`, `"YC S26"`, `CTO "Series A"`, `hiring manager <company>`.
- The snapshot lists name, headline, degree (`2nd`, `3rd`) and each profile URL. Keep only people whose headline matches the target (for example a 2025 or 2026 batch tag, or a founder title at the target company).
- Use "Next" for more results. Do not run more than 3 result pages per query.
- Write each kept person to `data/people.md`: name, headline, company, profile URL, vanity handle, source query.

## Sending one invite (the sequence that works)

Clicking the Connect button on a profile often does nothing. Use the direct invite URL.

1. Open `https://www.linkedin.com/in/<handle>/` and take a snapshot.
   - If a `Pending` link is present: skip, mark `already_pending`.
   - If a `Message` button and a `1st` degree marker: skip, mark `already_connected`.
   - If there is no Connect option (only Follow): skip, mark `no_connect_option`.
2. Navigate to `https://www.linkedin.com/preload/custom-invite/?vanityName=<handle>`.
   - The navigate call often returns `WebSocket response timeout after 30000ms`. This is normal. Take a snapshot; the dialog "Add a note to your invitation?" is usually there.
3. Click `Send without a note` using the ref from the snapshot (it has been `s1e24`, but read it from the snapshot each time).
   - If the click errors with `Unable to get parent` or times out, go to step 4 and check the result anyway.
4. Verify: navigate to `https://www.linkedin.com/in/<handle>/` and take a snapshot. The invite counts as sent only if a `Pending, click to withdraw invitation` link is present.
5. If it still shows `Connect`, repeat steps 2 to 4 once. In earlier runs, the second pass was the one that went through. After two failed passes, mark `failed` and move on.
6. Append a row to `data/tracker.md`: date, name, company, handle, status (`sent_pending`, `already_pending`, `already_connected`, `no_connect_option`, `failed`).
7. Wait a random number of seconds in the `seconds_between_actions` range before the next person.

If `send_invites_without_note` is false, click `Add a note` instead and type the note from `draft-outreach-message` (LinkedIn limits connection notes to 300 characters on free accounts, and some accounts get only a few per month, so check the dialog for a limit message).

## Stop conditions

Stop and tell the user if any of these happens:
- A CAPTCHA, "unusual activity" notice, or a prompt to verify identity.
- A message that you have reached the weekly invitation limit.
- The extension loses the tab (`No tab with given id`). Ask the user to reconnect, then resume from the next person in the list.
- Three `failed` results in a row.

## Do not

- Do not send messages from this skill. Messaging belongs to `draft-outreach-message`.
- Do not invite anyone not on the list or not matching the search the user approved.
- Do not mark an invite as sent without seeing `Pending`.
- Do not click the Cancel button on the browser's "started debugging" banner.

## Report

Return a short table: name, company, status. Then the counts for the day and how many invites remain under the cap.
