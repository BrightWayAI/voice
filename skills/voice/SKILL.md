---
name: voice
description: >-
  Draft or revise anything in your voice: email, reply, social post, Slack or DM message, intro, follow-up, thank-you note, document, blurb, bio, or comment. Fires on requests to write, draft, compose, reply, respond, reword, rewrite, revise, polish, tighten, shorten, or make human-facing prose sound like the user, even when the medium is unstated. Reads shared voice and medium-specific style files. Drafts go for review and are never sent directly. Does not fire for code, internal memory notes, or plain data.
---

<!-- OPENAI-ADAPTER:START -->
## OpenAI host binding

Before acting, read `../../references/openai-portability.md`. That file translates
host-specific tools, agents, artifacts, scheduling, connectors, and config-root
access for ChatGPT and Codex. It overrides concrete Claude/Cowork tool names only;
the workflow, safety gates, and output contract in this skill remain canonical.
<!-- OPENAI-ADAPTER:END -->


See `commands/style.md` for the full drafting workflow.

## When this skill fires

**Default to firing.** Any request that produces human-facing written text should run through this skill so it comes out in the user's voice — don't produce a generic draft and don't wait to be told "in my voice." When unsure whether a request yields user-facing prose, fire it.

Fires when the user:
- Runs `/style [medium] [purpose]` directly.
- Asks to **create** text: "draft/write/compose/put together/help me word a [email/reply/post/message/note/intro/follow-up/blurb/bio/comment]", "draft an email to [name]", "write a post about [topic]", "send a note to [name]".
- Asks to **respond**: "reply to this", "respond to [name]", "answer this", "follow up with [name]".
- Asks to **revise** existing text: "reword/rewrite/revise/polish/tighten/shorten/punch up this", "make this sound like me", "say this better".
- Asks for any written output that a person will read — even short ones (a one-line Slack reply, a LinkedIn comment, a subject line).

**Edge cases — still fire:** short messages; the user pastes text and says only "reply" or "fix this"; the medium is unstated (infer it — email/Slack/LinkedIn/doc — from context).

**Do NOT fire for:** code or commit messages, internal memory/cortex notes, raw data/tables/lists, or pure analysis with no human-facing deliverable.

## Pre-flight

Confirm `<config-root>/memory/me/voice.md` exists. If missing, recommend running `/setup-voice` (cortex) or `/setup-style` first. Without voice rules, drafts will be generic.

## What this skill is NOT for

- Bulk drafting. One-at-a-time. For bulk outreach use `weekly-outreach` or `lead-engine`.
- Auto-sending. Drafts only.
- Style enforcement on existing content. Use `/style-review` for that.
