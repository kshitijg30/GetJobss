# Job search config

Every skill reads `config.md` first. On first run the orchestrator copies this file to `config.md` and fills the blanks from your resume and one question in chat. If a value is still missing later, ask the user once and write the answer back to `config.md`.

## Resume

- resume_path:    <!-- blank = use the first PDF, DOCX or Markdown file in ./resume/, else ask -->

## Who I am (used in messages, filled from the resume on first run)

- name:
- current_role:
- strongest_impact_points:
  - <!-- 2 to 4 concrete things from the resume, with numbers if you have them -->
- links: <!-- portfolio, GitHub, product link -->

## Targets

- roles:          <!-- blank = ask on first run, suggest from the resume -->
- locations:      <!-- blank = ask on first run -->
- batches: YC W25, YC S25, YC X25, YC F25, YC W26, YC S26
- funding_window_months: 9
- funding_stages: Pre-seed, Seed, Series A
- sectors:        <!-- blank = infer from the resume -->

## Limits per day

- max_invites: 10
- max_message_drafts: 15
- max_messages: 15
- seconds_between_actions: 20 to 45 (random)

## Behaviour switches

- send_invites_without_note: true
- auto_send_messages: false     <!-- false = save drafts and wait for approval -->
- approve_first_n: 3            <!-- with auto_send on, show this many drafts before sending on its own -->
- skip_already_connected: true
- skip_people_with_pending_invite: true

## Message template

Fixed shape. Keep to 3 or 4 lines, under 70 words, one person per message. Fill the slots from the resume and the company brief. If you want your own wording, replace this block and keep the `{slots}`.

```
Hi {first_name}, I'm {current_role}. I worked on {impact_1}, and it relates to what {company} is doing with {specific_product_detail}.
I read through {specific_thing_researched} and liked {genuine_observation}.
I think {resume_skill} could fit into {product_area}. Would you be open to a quick chat?
```

Rules for slots:
- `specific_product_detail` and `specific_thing_researched` must come from something actually read (their site, docs, launch post, blog, changelog). If nothing was read, do not draft.
- `genuine_observation` is one concrete sentence, not praise.
- No claims about the company that are not in the company brief.

## Connection message

Used by `message-connections` for people who are already 1st-degree connections. Two short paragraphs, under 90 words.

```
Hey {first_name}, I'm a {current_role}, where I {one_line_of_work}.

Came across {company} and what you're building around {specific_thing_from_brief}. It's close to {resume_detail}, especially around {detail}. {question_about_hiring_or_contributing}. I'd love to learn more about what you're building and see if there's a way I could contribute.
```
