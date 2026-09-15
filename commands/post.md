---
description: Draft a LinkedIn-ready roundup post (plus first-comment sources) from staged research candidates or pasted material, in your voice. Reads `<config-root>/memory/me/voice.md` directly and uses the post-assembler agent. Companion to research's `/roundup`, which finds and stages candidates but does not draft them — Research finds, Comms Desk writes.
---

# /post [date]

Draft the weekly roundup post from staged candidates.

**Usage examples:**
- `/post` — use today's staged file, or the most recent one if today's isn't staged yet
- `/post 2026-09-12` — use a specific date's staged file
- `/post` (with pasted material in the same message) — draft directly from what the user pasted, skipping the staged-file lookup

---

## Step 0 — Resolve config root

Resolve `<config-root>` through explicit override → `CORTEX_CONFIG_ROOT` →
`~/.cortex/config-root` → legacy pointer → default. Request access only to the
resolved directory when required by the host.

---

## Step 1 — Find the input

1. If the user pasted candidate material directly in the invocation, use that as `candidates` and skip to Step 2.
2. Otherwise, look for `<config-root>/staged/roundup/[date].md`:
   - If a date was given, use that file.
   - If no date was given, use today's date; if today's file doesn't exist, fall back to the most recent file in `<config-root>/staged/roundup/`.
   - If no staged file exists at all and nothing was pasted, tell the user: "No staged roundup found. Run research's `/roundup` first to scan and pick candidates, or paste the candidates you want drafted directly."  Stop.
3. Parse the staged file's candidates, themes, and notable-tools sections.

---

## Step 2 — Draft (delegate to post-assembler)

Use the Task tool with `subagent_type="post-assembler"` and pass:
- `candidates-source` — the staged file path (or "pasted" if the user supplied material inline)
- `candidates` — the parsed candidate list
- `themes` — themes of the week, if present in the staged file
- `user-context-path` — `<config-root>/plugins/research.user-context.md`, if it exists (optional; use defaults and note the gap in Drafting Notes if not)
- `hook-style` — default "auto," override if user specified
- `length-target` — from research's user-context if available, override if user specified, else default "medium"

Wait for the draft to return.

---

## Step 3 — Show the draft and offer iteration

Render the draft:

```
**Draft:**

[full post]

**First comment (sources):**

[sources list]

**Alternate hook:**

[the alt hook]
```

Then offer:
> "What now? — 'ship it' (copy-paste ready) — 'use the alt hook' — 'shorter' / 'longer' — 'sharper hook' (regenerates with a different style) — 'redo with X' (your direction)"

For most edits, iterate inline without re-invoking post-assembler. For "redo with X" or major angle shifts, re-invoke post-assembler with updated guidance in the brief.

---

## Step 4 — Done

When the user says "ship it," do a final pass:
- Verify no banned phrases (per `voice.md` and research's user-context if available)
- Verify length is within target
- Verify hashtags per user-context (if configured)
- Output the final post + first-comment cleanly, ready to copy-paste.

Then ask:
> "Save this run to `<config-root>/comms/runs/[date].md` for the archive? (Y/N)"

If yes, write to `<config-root>/comms/runs/[YYYY-MM-DD].md` with: timestamp, staged-file source (or "pasted"), candidates selected, final post, alternate hook. Never write run data into the installed plugin directory.

---

## Behavior rules

- **Voice over speed.** If the draft doesn't sound like the user, fix it. A delayed post in their voice beats a fast post in generic LinkedIn-ese.
- **This command doesn't find or rank candidates.** If no staged file exists and nothing was pasted, point the user at research's `/roundup` rather than trying to scan the web yourself.
- **`voice.md` is canonical.** Always read `<config-root>/memory/me/voice.md` directly for voice rules; research's user-context only supplies roundup-specific format preferences (audience, hashtags, sign-off) when research is installed.
