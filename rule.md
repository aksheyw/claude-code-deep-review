# Deep Review Rule (Global)

Before claiming any test plan, security review, or architecture review is "complete":

1. Run the `deep-review` skill (14-lens iterative methodology)
2. Continue until a full round produces zero new findings
3. Save all findings to memory after each round
4. Flag CRITICAL/ship-stopper bugs immediately

**Origin:** developed while writing a test plan for a production React app. 28 rounds found 14 production bugs (2 ship-stoppers) that a code review of the same codebase, run the same day, did not catch.

**Methodology:** see `SKILL.md` in this repo.
