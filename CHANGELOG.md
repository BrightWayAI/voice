# Changelog

All notable changes to voice (formerly writing-style) are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/). Versions match `plugin.json`.

## [0.2.1] — Skill auto-invocation audit (2026-09-15)

Nucleus Operating Model Refactor Phase 3 step 3.7. Ritual and side-effecting
skills marked `disable-model-invocation: true` so they only run on explicit
invocation, not loose natural-language matching — the model can still be
asked to run them by name. Read-mostly, low-stakes, or high-frequency
conversational skills are left auto-invocable. Marketplace-wide this brings
model-invocable skills from ~81 to 27, under the ≤30 target audited with
`/skill-doctor`.

### Changed
- Marked `disable-model-invocation: true` on: `setup`, `setup-style`.

## [0.1.5] — Identity/voice moved to memory/me/ (2026-09-15)

### Changed
- Path references updated from `<config-root>/identity.md` / `<config-root>/voice.md` to `<config-root>/memory/me/identity.md` / `<config-root>/memory/me/voice.md`, per the Nucleus Operating Model Refactor Phase 2 scopes restructure (identity/voice are personal, not org-shared facts). No behavior change beyond the path.

## [0.1.4] — Genericize example sign-off name (2026-09-15)

### Fixed
- `references/analysis-template.md`, `commands/style-learn.md`,
  `commands/setup-style.md` — draft/final comparison tables used "Derek" as
  the example sign-off name, coincidentally matching a real contact from
  another plugin's docs. Swapped to "Alex" to remove any ambiguity, per the
  Nucleus Operating Model Refactor privacy audit. No functional change.

## [0.1.3] — OpenAI host adapter (2026-09-14)

### Added
- Native Codex/ChatGPT plugin manifest, durable `AGENTS.md` entrypoint, and an explicit OpenAI capability/degradation contract.
- GPT-discoverable skill aliases for canonical command workflows and read-only Codex role bindings where this plugin ships agents.
- Shared config-root resolution compatible with Cortex and Claude; all GPT tests use repository fixtures or temporary directories only.

## [0.1.2] — Broader auto-trigger (2026-06-08)

### Changed
- `skills/writing-style/SKILL.md` — widened the auto-trigger so the skill fires on far more natural phrasings without an explicit `/style` call. The `description` now covers create / respond / revise verbs (write, draft, compose, reply, respond, reword, rewrite, polish, tighten, "make this sound like me", etc.), short and one-line messages, and cases where the user doesn't say "in my voice" or name the medium. Added a **"default to firing"** rule in the body plus explicit edge-cases (paste-and-say-"reply", unstated medium) and non-triggers (code, internal memory notes, raw data). Addresses real-use feedback that the skill had to be invoked explicitly too often.

## [0.1.1] — Platform-agnostic Step 0 (2026-05-12)

### Changed
- **`/setup-style` Step 0 and `/style` directory-access language now fully platform-agnostic.** Every `request_cowork_directory(...)` call is conditional: "In Cowork, call `request_cowork_directory(...)`. In Claude Code (or any environment with direct filesystem access), no mount is needed." Same plugin source works in both runtimes.

### Why this matters
Phase 0 of SECOND-BRAIN-V2-SPEC. Removes the implicit Cowork-only assumption so Claude Code users do not hit unsupported tool calls during setup or drafting.

## [0.1.0] — Initial release

### Added
- Adaptive writing-style plugin. Learns your voice from real edits over time, complementing cortex's `/setup-voice` (which captures the high-level baseline once).
- **`/setup-style`** — bootstrap interview. Reads `<config-root>/voice.md` if present (cortex setup-voice output). Pulls 5-10 writing samples from Gmail (or asks the user to paste). Analyzes voice, structure, sentence style, common phrases, "what NOT to do." Writes/refines `<config-root>/voice.md` and creates medium-specific files (`style-email.md`, `style-social.md`) if 3+ samples exist per medium.
- **`/style [medium] [purpose]`** — draft something in your voice. Reads voice + medium-specific style files. Presents as draft for review. Never sends directly; Gmail emails are written as drafts via the connector.
- **`/style-learn`** — accepts a draft + final-version pair (provided inline or pulled from sent/published source via connector). Two-stage triage: cheap classifier asks "is this a style pattern?" → expensive synthesis only if pattern detected. After 2+ recurrences of the same pattern, surfaces a style-guide update proposal for user confirmation before commit.
- **`/style-review`** — audit existing style files. Flags contradictions, stale rules, unvalidated patterns, missing rules implied by recent writing history.
- **Per-medium style files** — `<config-root>/style-email.md`, `<config-root>/style-social.md`, `<config-root>/style-{medium}.md` for any other medium. Separated so email rules don't bleed into LinkedIn posts.
- **Config-root path convention** — uses `~/Documents/.claude-plugin-config-root` pointer like every other marketplace plugin. Style files live alongside `identity.md` and `voice.md`.
- **Privacy** — first occurrence of any pattern captured privately (not committed). Second occurrence triggers user-confirmed commit. Drafts are review-then-send-by-user; the plugin never sends anything.
- Reference: `references/analysis-template.md` documents the diff-vs-final analysis format.
