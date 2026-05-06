# Sample Output — what a deep-review session looks like

Below is a simulated session showing the cadence of the skill. Real findings are anonymized.

---

## Round 1: File Completeness

**Findings:** 3 new

- Missing test plan entry for `src/api/auth.ts` — Reference: test-plan.md §3 — Severity: HIGH
- `src/components/EmptyState.tsx` exists but has no test coverage section — Reference: test-plan.md §5 — Severity: MEDIUM
- New file `src/lib/imageUpload.ts` (added last week) not mentioned anywhere — Reference: file system vs plan — Severity: HIGH

**Running total:** 3 findings, 0 ship-stoppers.

---

## Round 2: Function-Level Audit

**Findings:** 2 new

- `useDebounce` hook is listed but only the happy path is tested — missing cleanup test — Reference: src/hooks/useDebounce.test.ts — Severity: MEDIUM
- `Auth.signIn()` covered, but `Auth.refreshToken()` is not in the test plan — Reference: test-plan.md §3 — Severity: HIGH

**Running total:** 5 findings, 0 ship-stoppers.

---

## Round 3: Category Gaps

**Findings:** 1 new

- No accessibility tests anywhere in the plan — Reference: test-plan.md (entire) — Severity: MEDIUM

**Running total:** 6 findings.

---

## Round 4: Cleanup & Performance

**Findings:** 0 new.

---

## Round 5: Client-Server Contract Alignment

**Findings:** 2 new

- 🚨 **CRITICAL** — Client sends `{ user_id }`, server expects `{ userId }` — every API call to `/profile/update` will silently 400 — Reference: src/api/profile.ts:42 vs server/handlers/profile.go:18 — Severity: SHIP-STOPPER
- Auth header missing on `/upload` endpoint client-side — server requires it — Reference: src/api/upload.ts:23 — Severity: HIGH

**Running total:** 8 findings, **1 ship-stopper**.

> ⚠ Found a ship-stopper. Continuing — there is usually a second one nearby.

---

## Round 6: Platform-Specific Paths

**Findings:** 1 new

- 🚨 **CRITICAL** — Image upload path branches on `Capacitor.isNativePlatform()` — the native branch (Android) calls a method that does not exist on the bridge — Reference: src/lib/imageUpload.ts:67 — Severity: SHIP-STOPPER

**Running total:** 9 findings, **2 ship-stoppers**.

---

## Rounds 7-13

(Each lens produced 0-2 additional findings — abbreviated for this example.)

---

## Round 14: Internal Consistency

**Findings:** 0 new.

---

## Round 15: File Completeness (re-run)

**Findings:** 0 new.

---

## Deep Review Complete

- Rounds completed: 15 (stopped at first empty round across all 14 lenses)
- Total findings: 14
- Bugs found: 14 (2 ship-stoppers — both fixed before merge)

Without the deep review, both ship-stoppers would have shipped to production:
1. Every profile update would silently fail
2. Every Android image upload would crash

Single-pass review caught zero of these.
