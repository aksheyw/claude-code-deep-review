# Deep Review — a 14-lens review methodology for Claude Code

> **A single-pass review catches ~60% of issues. The other 40% hide behind assumptions that feel obviously correct.**

This is a Claude Code skill that runs reviews through 14 sequential lenses — file completeness, contract alignment, security, state transitions, infra config, and 9 more — until a full pass produces zero new findings.

In its first real-world use it found **14 production bugs across 28 rounds**, including 2 ship-stoppers that would have broken every API call and every image upload in production. None were caught by the initial single-pass review.

## Why I built this

I was about to ship a release that had been "code-reviewed" once. Something felt off, so I tried reviewing it again with a different mental angle — and found a critical bug. So I tried a third angle. Found another.

By the time I was done, I had a list of 14 lenses, 28 rounds of review, and 14 bugs that wouldn't have been caught by traditional review. The pattern was clear: each lens catches a different *category* of issue. Run them all and stop only when an entire round comes up empty.

I codified the lenses into a Claude Code skill so I'd never ship without running them. This repo is that skill.

## What's in this repo

| File | Purpose |
|------|---------|
| `SKILL.md` | The skill itself — drop into `~/.claude/skills/deep-review.md` |
| `rule.md` | A short rule that auto-loads the skill on review tasks — drop into `~/.claude/rules/deep-review.md` |
| `examples/sample-output.md` | What a deep-review session looks like in practice |
| `LICENSE` | MIT |

## The 14 lenses (one-line summary)

1. **File / Section Completeness** — does the artifact cover every file/section?
2. **Function-Level Audit** — for files covered, is every exported function addressed?
3. **Category Gaps** — entire categories missing (e.g. no E2E tests at all)?
4. **Cleanup & Performance** — timers, listeners, leaks, O(n²) in hot paths
5. **Client-Server Contract** — ship-stoppers live here
6. **Platform-Specific Paths** — both branches of platform `if` covered?
7. **Network & Error Conditions** — offline, rate limit, timeout
8. **State Transitions** — every state machine, both directions
9. **UX Details** — back button in modals, empty states, image fallbacks
10. **Infrastructure & Config** — every config file, cover-to-cover
11. **Security & Secrets** — credentials, CORS, headers, sanitization
12. **Data Layer Rules** — every collection AND subcollection
13. **Existing Work Quality** — tests that test the wrong thing
14. **Internal Consistency** — does the document contradict itself?

Full descriptions: see [SKILL.md](SKILL.md).

## Install

Drop the files into your Claude Code config:

```bash
# Clone this repo
git clone https://github.com/aksheyw/claude-code-deep-review.git
cd claude-code-deep-review

# Install the skill
cp SKILL.md ~/.claude/skills/deep-review.md

# Optional: install the auto-trigger rule
cp rule.md ~/.claude/rules/deep-review.md
```

After install, in any Claude Code session run:

```
Use the deep-review skill on this PR
```

…or trigger it implicitly with phrases like *"did you miss anything?"*, *"is this thorough?"*, *"check again"*.

## When to use it

- Before merging a PR that touches contract boundaries (API ↔ client, DB rules, auth)
- After writing a test plan — to verify you covered all categories
- After an architecture doc — to find inconsistencies and unaddressed failure modes
- After a security review — to make sure no entire category was skipped
- Anywhere a single-pass review feels insufficient

## When NOT to use it

- Trivial single-line changes — overkill
- Time-boxed exploration where you'll iterate later — defer until ship time
- When you're still in design phase and the artifact is intentionally incomplete

## Expected runtime

| Scope | Rounds |
|-------|--------|
| Small artifact (single file, short doc) | 5-8 |
| Medium (multi-file, full plan) | 10-15 |
| Large (full codebase, launch readiness) | 15-20+ |

Each round is one lens. The skill stops when a full lens pass produces zero new findings.

## Companion repos

This skill pairs with two other Claude Code repos I've published:
- [`claude-code-pm-agents`](https://github.com/aksheyw/claude-code-pm-agents) — 7 product-builder subagents (PM, growth, brand, ASO, SEO, YouTube, comms triage)
- [`claude-code-rules`](https://github.com/aksheyw/claude-code-rules) — opinionated global rules including the deep-review review-discipline rule that auto-loads this skill

## License

MIT — see [LICENSE](LICENSE).

---

Built by [Akshey Walia](https://github.com/aksheyw). If you use this and find bugs the lenses missed, open an issue — the lens list is meant to grow.
