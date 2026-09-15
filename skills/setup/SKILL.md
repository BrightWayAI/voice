---
name: setup
description: Bootstrap writing-style's analysis of your voice. Auto-fires on "/setup-style", "set up writing style", "analyze my writing voice", "bootstrap my style guide", or when /style reports that voice or style files are missing. Pulls 5-10 writing samples (from Gmail/Drive if available, else asks the user to paste), analyzes patterns, writes voice + medium-specific style files.
---

<!-- OPENAI-ADAPTER:START -->
## OpenAI host binding

Before acting, read `../../references/openai-portability.md`. That file translates
host-specific tools, agents, artifacts, scheduling, connectors, and config-root
access for ChatGPT and Codex. It overrides concrete Claude/Cowork tool names only;
the workflow, safety gates, and output contract in this skill remain canonical.
<!-- OPENAI-ADAPTER:END -->


See `commands/setup-style.md` for the full interview.

## When this skill fires

- User runs `/setup-style` directly
- User says: "set up writing style", "analyze my voice", "bootstrap my style guide"
- The `/style` or `/style-learn` commands report missing style files → auto-route here

## Pre-flight

Best results when `<config-root>/voice.md` already exists from cortex's `/setup-voice` (provides high-level baseline; this plugin refines). If missing, recommend running `/setup-voice` first, then return here for the granular pattern analysis.

## Quick path

If the user wants to skip the bootstrap and just write rules manually: tell them to edit `<config-root>/voice.md` and create medium-specific files at `<config-root>/style-email.md` etc. directly. The plugin reads them at draft time regardless of how they got there.
