---
disable-model-invocation: true
name: setup-voice
description: "One-time voice bootstrap. Captures the writing-voice rules every drafting plugin needs and writes them to the private canonical voice file under the resolved config root (`<config-root>/memory/me/voice.md`, a Cortex-owned file location/contract)."
---

# setup-voice

Read `../../references/openai-portability.md`, then read
`../../commands/setup-voice.md` completely and follow it as the canonical workflow.
Treat `/setup-voice`, `$setup-voice`, natural-language activation, and the ChatGPT plugin
mention as equivalent entrypoints. Ignore Claude-only tool allowlists and model names;
apply the capability translation and degradation rules from the portability contract.

Do not duplicate or reinterpret the command here. Preserve its confirmation gates,
draft-only boundaries, file locations, and output contract. Note that the target file
(`<config-root>/memory/me/voice.md`) remains a Cortex-owned data contract even though
this interview now lives in Comms Desk — do not relocate the data file.
