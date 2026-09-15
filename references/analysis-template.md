# Draft vs Final Analysis Template

Used by `/style-learn` to structure the diff between a draft and the version the user actually sent.

## Diff Summary

| Aspect | Draft | Final | Classification |
|--------|-------|-------|---------------|
| Greeting | "Hi Sarah," | "Hey Sarah," | Tone shift (more casual) |
| Opening | 2 sentences of context | 1 sentence | Structure (more concise) |
| Body | 3 paragraphs | 2 paragraphs + bullet list | Structure (prefers lists) |
| Sign-off | "Best, Alex" | "Alex" | Pattern (drops formality) |
| Word choice | "utilize" | "use" | Voice (simpler words) |
| Em-dash | none | "— but here's the thing" | Style (em-dashes for asides) |

## Classification categories

- **Tone** — shift in formality, warmth, directness
- **Structure** — paragraph count, list use, ordering of sections
- **Voice** — word choice, sentence length, hedging vs. directness
- **Greeting / sign-off** — opener and closer patterns
- **Length** — overall message length compared to medium average
- **Hook** — opening-line pattern (for social posts and outbound)
- **CTA** — closing call-to-action style
- **Other** — anything that doesn't fit above

## Pattern Check

Before proposing a commit:

- [ ] Seen this change before? (check `<config-root>/plugins/writing-style.history.md`)
- [ ] Affects tone, structure, or word choice?
- [ ] Specific to this medium or universal?
- [ ] First occurrence → log to history only, don't commit yet
- [ ] Second+ occurrence → surface for user confirmation before commit

## Style Guide Update Proposal (only after 2+ recurrences)

```
**Rule:** [concise description — what to do / what NOT to do]
**Example:** "[draft phrasing]" → "[final phrasing]"
**File:** `<config-root>/memory/me/voice.md` | `<config-root>/style-email.md` | `<config-root>/style-{medium}.md`
**Section:** [where it fits in the file — e.g., "## Greeting patterns", "## Banned phrases"]
**Reinforced:** [N] occurrences, prior dates: [...]
```

## Anti-patterns (what NOT to do)

- **Don't commit on a single occurrence.** Wait for 2+ recurrences.
- **Don't write the draft/final content verbatim into history.md.** Extract the pattern only; full text stays in conversation/connector.
- **Don't apply contradicting rules silently.** If a new rule conflicts with an existing one, surface and let the user resolve.
- **Don't over-attribute.** A factual correction is not a style pattern. A tone shift on one message because the recipient was a friend (not a client) is context, not pattern.
