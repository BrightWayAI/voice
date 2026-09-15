# Security Policy

## What this plugin does with your data

voice learns your writing voice from real edits over time. Read-only against your existing content; writes only style-guide files plus drafts you review before sending.

**Reads:**
- **Email** (Gmail or equivalent) — sent emails as bootstrap samples and for diff analysis after you edit a draft externally.
- **Drive** (Google Drive or equivalent, optional) — recent docs as bootstrap samples.
- **Web** (`WebSearch`) — recent LinkedIn posts (if accessible publicly) for bootstrap and diff analysis.
- **Plugin references** — immutable `references/user-context.template.md` and `references/analysis-template.md`.
- **Shared user-level config** — `<config-root>/memory/me/voice.md` (canonical voice), `<config-root>/style-*.md` (medium-specific), `<config-root>/memory/me/identity.md` (read-only).
- **Other plugin drafts** — when you invoke `/style-learn` after editing a draft from Relationships, Delivery, News Curator, etc., the plugin reads the draft + the final edited version for diff analysis.

**Writes:**
- **Style files** — `<config-root>/memory/me/voice.md` (refined), `<config-root>/style-{medium}.md` (created/updated as patterns emerge).
- **Plugin user-context** — `<config-root>/plugins/voice.user-context.md` (after `/setup-style`).
- **Drafts** — produced inline for review. Drafts that need to be persistent (e.g., email drafts) are written via the appropriate connector (Gmail draft, Drive doc), never sent directly.

**Does not:**
- **Send any messages on your behalf.** Always drafts; user reviews and sends manually.
- **Commit style-guide updates without confirmation.** First occurrence: captured privately. Second+ occurrence: user confirms before any file write.
- **Modify your sent/published content.** Diff analysis is read-only on final versions.
- **Apply rules without context.** Style guides are referenced at draft time, not blindly enforced.

## Where data lives

- Plugin reference files inside the installed plugin directory.
- Style files at `<config-root>/memory/me/voice.md` and `<config-root>/style-*.md`.
- Plugin user-context at `<config-root>/plugins/voice.user-context.md` (gitignored — never committed).

## What gets sent off your machine

- Whatever your authorized Gmail, Drive, and WebSearch connectors send when invoked.

## Supported versions

| Version | Supported |
|---------|-----------|
| 0.1.x   | Yes       |

## Reporting a vulnerability

Report privately via GitHub Security Advisories:

https://github.com/BrightWayAI/voice/security/advisories/new

Do not open a public issue for security concerns. We aim to respond within 5 business days.
