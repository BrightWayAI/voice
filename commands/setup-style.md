---
description: Bootstrap or refine your writing style guides. Pulls writing samples, analyzes patterns, writes high-level voice + medium-specific style files. Reads cortex's `/setup-voice` output as baseline if present. Re-run anytime to refresh.
---

# /setup-style

Bootstrap interview for writing-style. Captures samples, analyzes patterns, writes initial style files.

---

## Step 0 — Resolve plugin config root

Per-plugin config in this marketplace lives under a user-chosen folder, recorded at `~/Documents/.claude-plugin-config-root` (single-line text file containing the absolute path).

### A — Try the pointer

Ensure access to `~/Documents`. In Cowork, call `request_cowork_directory(~/Documents)` once if not already granted. In Claude Code (or any environment with direct filesystem access), no mount is needed. Then read `~/Documents/.claude-plugin-config-root`.

- **Pointer exists**: read line 1 → that's the config root path. Ensure access to `<config-root>`. If running in Cowork and the folder isn't already mounted in this session, call `request_cowork_directory(<config-root>)`. If running in Claude Code or another environment with direct filesystem access, no mount call is needed. Skip to section C.
- **Pointer missing**: continue to section B.

### B — First-time bootstrap

Prompt: "First-time plugin setup. Where should I store your plugin config — identity, voice, style files, and per-plugin settings? Pick a folder you control (e.g., `~/Documents/Claude/` or `~/Documents/PluginConfig/`). The folder will hold `identity.md`, `voice.md`, style files, and a `plugins/` subdirectory."

Then:
1. Ensure access to `<path>`. If running in Cowork and the folder isn't already mounted in this session, call `request_cowork_directory(<path>)`. If running in Claude Code or another environment with direct filesystem access, no mount call is needed.
2. Create `<path>/plugins/` if it doesn't exist.
3. Write the absolute path to `~/Documents/.claude-plugin-config-root`.
4. Confirm.

### C — Read existing baseline

Read `<config-root>/memory/me/voice.md` if it exists (created by cortex's `/setup-voice`). If present, this is your baseline — writing-style will refine it, not overwrite. If missing, recommend running `/setup-voice` first for the high-level descriptors, then return here for the granular patterns.

For the rest of this document, **`<config-root>`** refers to the resolved path. This plugin's config file lives at **`<config-root>/plugins/writing-style.user-context.md`**.

---

## Step 1 — Gather writing samples

Goal: 5–10 representative samples spanning the mediums the user writes in (work email, casual email, LinkedIn post, Slack DM, doc, etc.).

### Option A — Pull from Gmail (if connector available)

If a Gmail MCP is available, ask:

> "Want me to pull your last 10 sent emails as samples? I'll scan for voice patterns and won't store the email content beyond this analysis."

If yes: fetch `in:sent` last 10. Group by recipient-type if possible (work domain vs. personal vs. external).

### Option B — Pull from Drive (if available, for docs)

> "Pull your last 3-5 docs from Drive as bootstrap samples? Look for ones you wrote yourself, not drafts written by others."

### Option C — Manual paste

If no connector pulls work, ask the user to paste 5-10 samples across mediums. Format suggestion:

```
**Email 1 (work, to client):**
[paste sample]

**Email 2 (casual, to peer):**
[paste sample]

**LinkedIn post:**
[paste sample]
```

The user can paste as many as they want. More samples → better baseline.

---

## Step 2 — Analyze patterns

For each sample, capture:

- **Voice & tone** — formal/casual, warm/direct, hedged/blunt
- **Structure** — greeting style, paragraph length, list usage, sign-off
- **Sentence style** — contractions, length, hedging, em-dash usage, emoji
- **Common phrases** — opening lines, transitions, closing phrases that recur
- **What they NEVER do** — phrases/patterns conspicuously absent

Categorize patterns by medium. If the user has 3+ samples in a medium (e.g., 3+ work emails), that medium gets its own style file.

If `<config-root>/memory/me/voice.md` already exists from cortex's `/setup-voice`: don't duplicate the high-level rules. Add only the granular patterns the bootstrap reveals (e.g., specific opening-line patterns, recurring word choices, sentence-rhythm observations).

---

## Step 3 — Present findings for user confirmation

Show the analysis as a structured summary:

```markdown
## Voice analysis from [N] samples

### Cross-medium patterns (will go in <config-root>/memory/me/voice.md)

- **Greeting:** "Hey [name]," for casual; "Hi [name]," for first-touch work
- **Sign-off:** "— Alex" (no honorific, just first name)
- **Sentence length:** short to medium; rarely runs more than 2 commas
- **Word choices:** prefers "use" over "utilize", "help" over "leverage"
- **Em-dash usage:** frequent, particularly mid-sentence

### Email-specific (will go in <config-root>/style-email.md)

- **Opening line for follow-ups:** references something specific they did, never generic "hope this finds you well"
- **CTA style:** "Worth a quick chat?" / "Happy to send the teardown if useful"

### Social-specific (will go in <config-root>/style-social.md)

- **Hook pattern:** opens with a contrarian or observation hook; never "Excited to share..."
- **List structure:** numbered, never bulleted; max 5 items
- **Closing:** one-line synthesis, never a hard CTA

Look right? Any of these wrong, or anything I missed?
```

The user reviews and corrects. Apply corrections.

---

## Step 4 — Write style files

After user confirms:

1. **Refine `<config-root>/memory/me/voice.md`** — add cross-medium patterns observed. Don't overwrite existing content; merge. If cortex's `/setup-voice` already filled in voice descriptors and banned phrases, append a new "## Patterns observed from samples" section with the new findings.

2. **Create medium-specific files** — `<config-root>/style-email.md`, `<config-root>/style-social.md`, etc., for any medium with 3+ samples.

3. **Write `<config-root>/plugins/writing-style.user-context.md`** — plugin's own config:

```markdown
# writing-style user context

_Last updated: [date]_

## Edit detection sources
- **Gmail:** [enabled / disabled — if Gmail MCP available]
- **Drive:** [enabled / disabled]
- **LinkedIn / web:** [enabled / disabled]
- **Manual only:** [Y/N]

## Confidence threshold
- **Recurrences before commit:** [default 2]

## Per-medium tracking
- **email:** [tracked / not tracked]
- **social:** [tracked / not tracked]
- **doc:** [tracked / not tracked]
- **other:** [list]

## Sample sources used in bootstrap
- [date] — [N] samples from Gmail
- [date] — [N] samples from Drive
- [date] — [N] manual pastes
```

---

## Step 5 — Confirm and offer next step

Summarize:

> "Voice file refined. Created [N] medium-specific style files: [list]. From here on, when you draft via `/style`, the plugin reads these files. When you edit a draft before sending, run `/style-learn` so the plugin sees what changed and proposes style-guide updates after 2+ recurrences. Run `/style-review` monthly to audit."

---

## Behavior rules

- **Don't overwrite cortex's voice.md.** Merge or append. The high-level descriptors (3 words, banned phrases) belong to cortex's `/setup-voice`; the granular patterns belong here.
- **3+ samples threshold for medium-specific files.** Fewer than that → not enough signal, skip the file.
- **Skip what doesn't apply.** If the user only writes emails, don't manufacture a `style-social.md`.
- **Idempotent.** Re-running `/setup-style` refreshes patterns from new samples; doesn't reset existing rules.
- **Privacy.** Sample content is read in-session for analysis. Not stored beyond extracted patterns. Plugin never copies email/doc content into style files.
