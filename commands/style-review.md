---
description: Audit your style files. Flag contradictions, unvalidated rules (no recurrences in 30+ days), patterns observed in history but not yet committed, and missing rules implied by recent writing. Run monthly or when style feels off.
---

# /style-review

Periodic audit of `<config-root>/memory/me/voice.md` and `<config-root>/style-*.md`. Read-only over the style files; output is a checklist for you to action.

---

## Step 0 — Resolve config root

Resolve `<config-root>` through the shared precedence chain. Read:
- `<config-root>/memory/me/voice.md`
- All `<config-root>/style-*.md` files
- `<config-root>/plugins/voice.history.md` (the pattern-history log from `/style-learn`)
- `<config-root>/plugins/voice.user-context.md`

---

## Step 1 — Check for contradictions

Scan all style files for rules that contradict each other. Examples:
- `voice.md` says "prefer short sentences" but `style-email.md` says "expand context paragraphs"
- One file says "always use em-dashes for asides" but another says "avoid em-dashes; use commas"
- Banned phrase in voice.md appears as a recommendation in a medium-specific file

For each contradiction, output:

```
⚠ **Contradiction**
- `voice.md` line N: "[rule A]"
- `style-email.md` line M: "[rule B]"
- **Resolution suggestion:** keep A as cross-medium default; add medium-specific override in B with explicit condition, OR drop one.
```

---

## Step 2 — Check for unvalidated rules

A rule is "unvalidated" if it was committed to a style file but no recent diff in history.md has reinforced it (or contradicted it) in the last 30 days.

For each unvalidated rule:

```
⏳ **Unvalidated**
- `[file]`: "[rule]"
- Last reinforced: [date or "never since commit on [date]"]
- **Consider:** is this still you? Remove if you've drifted away from it.
```

---

## Step 3 — Surface uncommitted patterns

Read `voice.history.md`. Find patterns that have recurred 2+ times but were NOT committed (user skipped).

For each:

```
💡 **Pattern observed but not committed**
- Change: "[draft]" → "[final]"
- Occurrences: [N] (dates: [...])
- **Consider:** ready to commit to [suggested file], or still watching?
```

---

## Step 4 — Suggest additions from recent writing

If `claude-cortex` is installed and the user has been actively drafting via `/style` or other plugins, scan cortex memory for any captured PATTERN observations about writing style that haven't made it into style files yet.

For each:

```
🌱 **Suggested addition from cortex memory**
- Cortex observed: "[pattern]"
- Source: [conversation date, project node]
- **Consider:** add to [suggested file]?
```

---

## Step 5 — Output the audit report

Render as a single scannable report:

```markdown
## Style audit — [date]

### Contradictions ([N])
[items]

### Unvalidated rules ([N])
[items]

### Uncommitted patterns ([N])
[items]

### Suggested additions ([N])
[items]

### Files audited
- `<config-root>/memory/me/voice.md` ([N] rules)
- `<config-root>/style-email.md` ([N] rules)
- `<config-root>/style-social.md` ([N] rules)
- [...]

### Recommended actions (ranked)
1. [Highest-impact action — e.g., "Resolve the em-dash contradiction; it's affecting 60% of recent drafts"]
2. [Next]
3. [Next]
```

---

## Step 6 — Offer to apply changes

After the user reads the audit, ask:

> "Want me to:
> - **Resolve a contradiction** (specify which)
> - **Remove an unvalidated rule** (specify which)
> - **Commit an observed pattern** (specify which)
> - **Audit only — make no changes** (default)"

Apply per the user's instruction. All changes go through the same diff-confirm flow as `/style-learn`.

---

## Behavior rules

- **Read-only by default.** Audit produces a report; doesn't modify files unless the user explicitly approves.
- **Rank by impact.** If you find 12 unvalidated rules, surface the 3 most-recent or most-cited first. Don't dump a wall of equally-weighted items.
- **Honor the user's intent.** If a rule has been deliberately preserved despite no recurrences (e.g., it's a baseline aspirational rule), don't push to remove it — flag it with "consider, but you may want to keep it as intent rather than observation."
- **No fake findings.** If there are no contradictions, no unvalidated rules, etc., say so explicitly: "✓ No contradictions found. ✓ All rules have been reinforced in the last 30 days." A clean audit is good news.

## When to run this

- **Monthly** — a 5-min check-in to keep style files honest
- **After a substantial writing burst** (e.g., 20+ /style invocations in a week)
- **When something feels off** — drafts that don't sound like you anymore are usually a style-file-drift issue
- **Before sharing your stack** — if you fork this marketplace for someone else, audit first so your style files reflect your actual voice
