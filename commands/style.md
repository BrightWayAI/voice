---
description: Draft something in your voice — email, social post, doc, message. Reads `<config-root>/memory/me/voice.md` (canonical voice from cortex setup-voice) plus medium-specific style files (`<config-root>/style-{medium}.md` from writing-style). Presents as draft for review; never sends directly.
---

# /style [medium] [purpose]

Draft-first writing in your voice.

**Usage examples:**
- `/style email reply to Sarah's question about the AI Op Model timeline`
- `/style social a LinkedIn post about [topic]`
- `/style doc a one-pager describing our offering`
- `/style dm Slack DM to Tom about Tuesday's meeting`

---

## Step 0 — Resolve config root and read style files

Ensure access to `~/Documents`. In Cowork, call `request_cowork_directory(~/Documents)` once if not already granted. In Claude Code (or any environment with direct filesystem access), no mount is needed. Then read `~/Documents/.claude-plugin-config-root` for the config root path.

Then read:
- `<config-root>/memory/me/identity.md` — for name, title, company (signature material)
- `<config-root>/memory/me/voice.md` — canonical high-level voice (descriptors, banned phrases, hook patterns)
- `<config-root>/style-{medium}.md` — medium-specific style file if the user is drafting in a known medium
- `<config-root>/plugins/writing-style.user-context.md` — plugin config

If `voice.md` is missing, suggest running `/setup-voice` (cortex) or `/setup-style` first.

---

## Step 1 — Parse the user's request

Extract:
- **Medium** — email / social / doc / dm / slack / linkedin / etc.
- **Purpose** — reply / first-touch / re-engage / announcement / explainer / etc.
- **Audience** — who they're writing to (if specified)
- **Context** — what the message should accomplish

If the user just said "draft an email" without specifics, ask one clarifying question:

> "Quick — who's this to and what's the goal? (e.g., 'reply to Sarah at Acme confirming Tuesday's meeting time' or 'first-touch outreach to a new prospect about our agent build offering')"

---

## Step 2 — Draft

Apply, in priority order:

1. **Voice descriptors and banned phrases** from `voice.md` (cortex baseline)
2. **Medium-specific rules** from `style-{medium}.md` (writing-style refinements)
3. **Inferred patterns from recent samples** if the user has been writing a lot in this medium recently (use cortex memory or sample cache to inform)
4. **The user's purpose and audience** — override any rule if context demands

Length guidance:
- **Email** — 3-5 sentences for follow-ups, 4-7 for first-touch, 2-3 for thank-you/congrats
- **Social post** — per `style-social.md` length target (default LinkedIn: medium = 700-1500 chars)
- **DM / Slack** — 1-3 sentences, terse, drop the greeting if it's a casual channel
- **Doc** — whatever length the purpose demands; structure with headers/lists

Never use:
- Any banned phrase from `voice.md`
- Generic openers ("Hope this email finds you well", "Just checking in")
- Filler ("I wanted to reach out because", "I'd love to discuss")

---

## Step 3 — Present the draft for review

For emails (if Gmail MCP available):

1. Create a Gmail draft via the connector. Tell the user: "Draft created in Gmail. Review and let me know if it's good to send, or tell me what to change."
2. Also paste the full draft inline in the conversation so the user can review without switching apps.

For all other media:

```
**Draft — [medium]**

[Full draft, ready to copy or edit]

---

**Notes:**
- Length: [actual char count or word count]
- Voice rules applied: [list — banned phrases avoided, hook pattern chosen, etc.]
- Anything I had to guess: [if any]

Review and let me know:
- "Good to go" → I'll mark it ready
- "Change X to Y" → I'll revise inline
- After you edit the final version externally, run `/style-learn` so I can refine the style guide.
```

---

## Step 4 — Handle revisions

If the user says "change X to Y" or provides edits in chat:

1. Apply changes
2. Note the change in working memory (writing-style will analyze later via `/style-learn`)
3. Re-present the updated draft

If the user says "good to go" or signals approval:

1. For Gmail drafts: confirm the draft is ready in Gmail. Do NOT send.
2. For other media: confirm the user can copy from the chat output.
3. Suggest: "After you send/publish (with whatever edits you make externally), run `/style-learn` and pass me the final version so I can compare to the draft and learn from any patterns."

---

## Behavior rules

- **Never send directly.** Drafts only. Gmail drafts go to the user's Drafts folder, not the Sent folder.
- **Voice fidelity > cleverness.** A draft that sounds like the user beats a "cleverer" alternative.
- **Honor banned phrases ruthlessly.** If a banned phrase appears in the draft, rewrite the line — don't just flag it.
- **One clear ask per message** if it's outbound. Don't bury two CTAs.
- **Match the medium's structure.** Don't put bulleted lists in an email if the user's email style is prose. Don't write a 1500-char LinkedIn post when their average is 600.
- **Single shot.** Don't ask clarifying questions unless genuinely ambiguous. Draft your best version; let the user iterate.

## What this is NOT for

- **Mass-mailing.** This is for one-at-a-time drafting. For bulk outreach use `weekly-outreach` or `lead-engine`.
- **Auto-sending.** Strictly drafts. User reviews and sends.
- **Style enforcement against existing files.** This drafts new content. To audit existing content use `/style-review`.
