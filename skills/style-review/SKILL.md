---
name: style-review
description: Audit your style files for contradictions, stale rules, uncommitted patterns. Auto-fires on "/style-review", "audit my style guide", "is my style guide healthy", "check my voice rules", or any phrase about reviewing the style files. Read-only by default; produces a report you can action.
---

<!-- OPENAI-ADAPTER:START -->
## OpenAI host binding

Before acting, read `../../references/openai-portability.md`. That file translates
host-specific tools, agents, artifacts, scheduling, connectors, and config-root
access for ChatGPT and Codex. It overrides concrete Claude/Cowork tool names only;
the workflow, safety gates, and output contract in this skill remain canonical.
<!-- OPENAI-ADAPTER:END -->


See `commands/style-review.md` for the full audit workflow.

## When this skill fires

- User runs `/style-review` directly
- User says: "audit my style guide", "check my style files", "is my voice consistent"
- A scheduled task triggers this (common pattern: monthly)

## What this skill is NOT for

- Active style enforcement. The audit produces recommendations; doesn't auto-fix.
- Drafting. Use `/style` for that.
- Bulk style file rewrites. The audit suggests changes one at a time.
