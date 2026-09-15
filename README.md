# voice

Adaptive voice plugin for Claude. Learns your voice from real edits over time, in addition to whatever you captured up front via cortex's `/setup-voice`.

## What it does

- **Draft-first workflow** — every written output (email, social post, doc) is presented as a draft for your review. Never sent directly.
- **Edit detection** — when you edit a draft inline in chat OR externally (Gmail, Drive, LinkedIn), the plugin compares draft-vs-final and looks for style patterns.
- **Pattern recognition** — only proposes style-guide updates after a pattern appears 2+ times. One-offs don't pollute the guide.
- **Per-medium style files** — separate `style-email.md`, `style-social.md`, etc., so what works for a LinkedIn post doesn't get applied to a client email.
- **Two-stage triage** — cheap classifier decides if an edit reveals a pattern; expensive synthesis only runs if it does. Cost-efficient at scale.
- **Audit command** — `/style-review` scans your style files for contradictions, stale rules, and unvalidated patterns. Run monthly.

## How it works with the rest of the marketplace

| Plugin | What it does | How voice integrates |
|---|---|---|
| **cortex `/setup-voice`** | One-time capture of high-level voice descriptors (3 words, banned phrases, hook patterns) | Baseline. voice refines over time on top of this. |
| **bizdev-outreach** | Drafts per-contact outreach | When you edit a draft before sending, run `/style-learn` to feed the pattern back. |
| **Relationships `/draft-touchpoint`** | Drafts relationship outreach | Same — `/style-learn` after you tweak. |
| **news-curator `post-assembler`** | Drafts LinkedIn roundup posts | Same — `/style-learn` after you publish your edited version. |
| **All drafting plugins** | Read voice rules at draft time | Read `<config-root>/memory/me/voice.md` (canonical) + medium-specific `<config-root>/style-{medium}.md` (refined by voice). |

## Install

Recommended: via the [BrightWayAI marketplace](https://github.com/BrightWayAI/nucleus).

```
/plugin marketplace add BrightWayAI/nucleus
/plugin install voice@nucleus
```

## First-time setup

Run `/setup-style`. The interview:

1. Reads `<config-root>/memory/me/voice.md` if it exists (from cortex's `/setup-voice`). Uses it as baseline.
2. Asks you for 5-10 writing samples across mediums (work email, casual email, LinkedIn post, doc, slack DM, etc.) — OR pulls them from Gmail/Drive if connectors are available.
3. Analyzes patterns: voice, structure, sentence style, common phrases, "what you never do."
4. Writes/refines `<config-root>/memory/me/voice.md` (high-level) and creates medium-specific files (`style-email.md`, `style-social.md`) if 3+ samples exist for that medium.

Saved at:
- `<config-root>/memory/me/voice.md` — high-level voice (shared with all drafters)
- `<config-root>/style-email.md` — email-specific (voice only)
- `<config-root>/style-social.md` — social-specific
- `<config-root>/style-{medium}.md` — any other medium

Plus plugin config at `<config-root>/plugins/voice.user-context.md` (edit-detection paths, confidence threshold, per-medium tracking opt-ins).

## Commands

| Command | Purpose |
|---|---|
| `/setup-style` | Bootstrap or update style guides. Pulls writing samples, analyzes, writes initial files. |
| `/style draft [medium] [purpose]` | Draft something in your voice. Reads voice + medium-specific style files. Presents as draft for review. |
| `/style-learn` | Analyze a draft-vs-final diff and propose style-guide updates if a pattern emerges. Run after sending an edited version of any draft. |
| `/style-review` | Audit existing style files. Flags contradictions, unvalidated rules, suggested additions based on recent writing history. Run monthly. |

<!-- OPENAI-SUPPORT:START -->
## ChatGPT and Codex

Voice ships as a native OpenAI plugin as well as a Claude plugin. In
ChatGPT desktop Local Work, enable **Voice** and ask naturally or mention
`@Voice`. In Codex, use natural language or the namespaced skills exposed
by the plugin. Claude slash-command names in this README remain workflow aliases.

All hosts resolve the same `<config-root>` used by Cortex, so Claude, ChatGPT desktop,
and Codex can share identity, voice, memory, and per-plugin settings without copying
them. The installed plugin directory is read-only at runtime. See
[`references/openai-portability.md`](references/openai-portability.md) for capability
mapping, connector checks, permissions, and honest degraded behavior.

Import the full catalog from
[`BrightWayAI/nucleus`](https://github.com/BrightWayAI/nucleus); Nucleus is the master
marketplace, while each plugin remains independently installable.
<!-- OPENAI-SUPPORT:END -->


## License

MIT.
