---
description: Analyze a draft-vs-final pair to detect style patterns. Two-stage triage — cheap classifier first ("is this a pattern or one-off?"), expensive synthesis only if pattern detected. Updates style files only after 2+ recurrences of the same pattern. Run after editing any draft before sending/publishing.
---

# /style-learn

Compare a draft against the final version the user actually sent. Detect style patterns. Propose style-guide updates after 2+ recurrences.

This is what makes writing-style adaptive. Without `/style-learn`, the plugin only knows what you captured during `/setup-style`. With `/style-learn` running after edits, the plugin compounds.

---

## Step 0 — Resolve config root

Read `~/Documents/.claude-plugin-config-root`. Read `<config-root>/memory/me/voice.md` and any `<config-root>/style-*.md` files. Read `<config-root>/plugins/writing-style.user-context.md` for the confidence threshold (default: 2 recurrences) and per-medium tracking opt-ins.

---

## Step 1 — Get both versions

Two input modes:

### Mode A — User pastes both versions

User runs `/style-learn` and the plugin asks:

> "Paste the draft I gave you, then paste the final version you actually sent. Or, if I can pull the final from your inbox/drive, tell me where and I'll fetch it."

### Mode B — Fetch from connector

If the user just sent an email from a Gmail draft I created, ask:

> "Was this the [Gmail draft from earlier today]? Want me to pull the sent version and compare?"

Then via Gmail MCP: search `in:sent` for the matching subject/recipient within the last 24h. Compare against the draft I have in working context (or stored at `<config-root>/plugins/writing-style.drafts/[id].md` if the plugin caches drafts — proposed for v0.2).

If neither version is retrievable, fall back to Mode A.

---

## Step 2 — Triage (cheap call)

Before expensive diff analysis, run a cheap classifier on the two versions:

> Cheap model prompt:
> "Compare draft and final. Is the difference substantive (style pattern worth learning) or trivial (single typo, factual correction, recipient-specific tweak)?
> Return JSON: `{pattern: true|false, category: 'tone' | 'structure' | 'word_choice' | 'length' | 'greeting' | 'signoff' | 'other', medium: 'email'|'social'|'doc'|'dm'|'other'}` or `{pattern: false, reason: 'trivial'}`."

If `pattern: false` → respond to the user: "Looks like minor edits, no pattern to learn. Logged for future reference." Update plugin's pattern-history with this observation but don't write to any style file.

If `pattern: true` → continue to Step 3.

---

## Step 3 — Synthesize the diff (expensive call)

For each substantive change, fill out the analysis per `references/analysis-template.md`:

```markdown
## Diff: [date]

### Changes
| Aspect | Draft | Final | Classification |
|---|---|---|---|
| Greeting | "Hi Sarah," | "Hey Sarah," | Tone shift (more casual) |
| Body | 3 paragraphs | 2 paras + bullet list | Structure (prefers lists) |
| Sign-off | "Best, Alex" | "— Alex" | Pattern (no honorific) |
| Word choice | "utilize" | "use" | Voice (simpler words) |

### Medium
[email / social / doc / dm]

### Pattern history
- [Looked up in plugin's pattern-history log at <config-root>/plugins/writing-style.history.md]
- This is occurrence: [1st / 2nd / 3rd ...]
- Prior occurrences (if any): [dates]
```

---

## Step 4 — Decide: capture privately or commit to style files

Check `<config-root>/plugins/writing-style.history.md` (the pattern-history log — append-only).

**First occurrence of a pattern** → capture in history.md but DO NOT update voice.md or style-*.md yet. Tell the user:

> "Spotted a pattern — you changed [X] to [Y]. First time I've seen this, so I'll watch for it. If it happens again, I'll suggest updating your style guide."

**Second+ occurrence** (per confidence threshold) → propose the style update for user confirmation:

> "This is the second time I've seen you change [X] to [Y] (also on [prior date]). Should I add this to your style guide?
> - **Add to `<config-root>/memory/me/voice.md`** (cross-medium rule)
> - **Add to `<config-root>/style-email.md`** (email-only)
> - **Both**
> - **Skip — keep watching but don't commit yet**"

If user confirms, write the rule to the appropriate file in the right section. Examples:
- voice.md → "## Patterns" or "## Banned phrases" depending on rule type
- style-email.md → relevant medium-specific section

If the rule contradicts an existing rule, surface the conflict: "This contradicts the existing rule [...]. Replace, keep both as conditional, or skip?"

---

## Step 5 — Update history.md

Regardless of commit decision, append to `<config-root>/plugins/writing-style.history.md`:

```markdown
## [YYYY-MM-DD HH:MM] [medium] — [contact-or-context]

**Change:** [aspect] — "[draft value]" → "[final value]"
**Classification:** [category]
**Action taken:** [private capture / committed to <file> / user skipped]
**Pattern history:** occurrence [N], prior dates: [...]
```

This file is the audit trail. `/style-review` reads it to detect contradictions and surface patterns that recur but haven't been committed.

---

## Step 6 — Confirm

Tell the user what was logged:

> "Logged. [If committed:] Added rule '[X]' to [file]. [If not committed:] Still watching for this pattern — [1 more] occurrence needed before I propose adding it."

---

## Behavior rules

- **Confidence threshold respected.** Default: 2 recurrences before any commit. Configurable per-user via setup.
- **User confirms every commit.** No silent writes to style files.
- **Don't apologize for trivial.** If the triage says "no pattern," don't pad the response — just log and move on.
- **Contradictions surfaced.** If a new rule contradicts an existing one, the user resolves explicitly.
- **History is append-only.** `<config-root>/plugins/writing-style.history.md` is never edited; only appended.
- **Privacy.** Diff content is analyzed in-session; only the extracted pattern is logged. Full draft/final text is not stored in history.md.

## Edge cases

- **User edited a lot.** If the final is substantively different (>50% changed), it's not really an edit — flag as "this is a rewrite, not an edit; want me to use the final as a new sample for `/setup-style` refresh instead?"
- **User edits because of factual correction, not style.** Triage should classify as `pattern: false`. If a non-style edit slipped through, the user can override: "this was just a typo fix, ignore."
- **Medium not yet tracked.** If the diff is on a medium without a `style-{medium}.md` file, the first commit creates the file.
