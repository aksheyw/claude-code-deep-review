# Deep Review Rule (Global)

Before claiming any test plan, security review, or architecture review is "complete":

1. Run the `deep-review` skill (14-lens methodology): one full pass of all fourteen lenses
2. If the user asks for the loop, repeat full passes until one produces zero new findings. If the work gates a release or a build and they did not ask, offer the loop after the pass
3. Save all findings to memory after each round
4. Flag CRITICAL/ship-stopper bugs immediately

**Origin:** developed while writing a test plan for a production React app. 28 rounds found 14 production bugs (2 ship-stoppers) that a code review of the same codebase, run the same day, did not catch.

**Methodology:** see `SKILL.md` in this repo.
