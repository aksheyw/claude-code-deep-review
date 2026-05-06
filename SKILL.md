---
name: deep-review
description: Iterative deep review using 14-lens methodology. Use when reviewing ANY plan, code review, architecture doc, test plan, or security assessment — before claiming it is complete. Runs multiple passes from different analysis angles until no new findings emerge. Each lens catches issues invisible to previous lenses. Found 14 production bugs (2 ship-stoppers) in its first use. ALWAYS use this when the user asks to review something thoroughly, verify completeness, audit a document, or check if anything was missed. Also triggers on "did you miss anything", "is this thorough", "are you sure", "check again", or similar phrases.
---

# Deep Review — 14-Lens Iterative Methodology

Apply 14 sequential lenses to the target artifact. Each lens examines the work from a different angle. Continue until a full round produces ZERO new findings.

## Rules

1. State which lens you are applying at the start of each round
2. Run specific checks for that lens — use code tools (grep, find) if available, otherwise reason systematically through each item
3. Document every finding with specific references (file:line if code, section name if document)
4. Flag CRITICAL/ship-stopper issues immediately — do not wait until all lenses are done
5. Save findings after every round
6. Stop ONLY when a complete round finds zero new findings

## The 14 Lenses

### Lens 1: File / Section Completeness
Does the artifact cover every relevant file, feature, section, or topic? Cross-reference against the actual codebase, spec, or requirements. Anything missing entirely?

### Lens 2: Function / Item-Level Audit
For items that ARE covered — is each individual function, endpoint, component, or requirement actually addressed? Or just the file/section name listed without the details?

### Lens 3: Category Gaps
Are entire CATEGORIES of concerns missing? For test plans: unit, integration, E2E, smoke, regression, performance, security, accessibility, cleanup, config. For architecture reviews: scalability, monitoring, disaster recovery. For code reviews: correctness, security, robustness, maintainability.

### Lens 4: Cleanup, Performance, and Resource Management
Timers without cleanup. Listeners without removal. Memory leaks. O(n^2) in hot paths. Connections not closed. Caches that grow unbounded.

### Lens 5: Client-Server Contract Alignment
Does the client send what the server expects? Headers, auth tokens, field names, body shapes. **This is where ship-stoppers hide** — one side changes but the other does not.

### Lens 6: Platform / Environment-Specific Paths
Code that behaves differently on different platforms (mobile vs web, native vs browser, dev vs prod). Are BOTH paths covered? Is the primary platform tested more, not less?

### Lens 7: Network and Error Conditions
Offline behavior. Rate limiting UX. Timeout handling. What does the user see when things fail? Generic errors or helpful messages?

### Lens 8: Business Logic State Transitions
Every state machine in the system: free to premium, active to deleted, consented to revoked, draft to published. Is each transition AND its reverse covered?

### Lens 9: UX and Interaction Details
Back button in modals. Image load failures. Double-tap prevention. Form data loss on navigation. Loading-to-data transitions (not just "loading renders"). Empty states for every list.

### Lens 10: Infrastructure and Configuration
Every config file read cover-to-cover. Version numbers, security settings, stale references, deprecated flags, missing entries.

### Lens 11: Security and Secrets
Credentials in source or docs. Git history leaks. CORS misconfigurations. Missing security headers. External links without noopener. User input not sanitized.

### Lens 12: Data Layer and Access Rules
Database security rules, access controls, CORS configs. Every collection AND subcollection must have explicit rules. Default-deny means missing rules = silently broken features.

### Lens 13: Existing Work Quality
Things that appear to be covered but are actually wrong. Tests that pass but test the wrong thing. Documentation that references moved files. Stale assertions that no longer match the code.

### Lens 14: Internal Consistency
Does the document contradict itself? Do numbers add up? Does the table of contents match the actual sections? Do cross-references point to real targets?

## Output Format

After each round:

```
## Round N: [Lens Name]
Findings: X new
- Finding 1 — Reference: [specific location] — Severity: CRITICAL/HIGH/MEDIUM/LOW
- Finding 2 — ...

Running total: Y findings, Z bugs
```

When complete:

```
## Deep Review Complete
- Rounds completed: N (stopped at first empty round)
- Total findings: X
- Bugs found: Y (Z ship-stoppers)
```

## When to Stop

Stop when a full round produces zero findings. Expected rounds by scope:
- Small artifact (single file, short doc): 5-8 rounds
- Medium artifact (multi-file, full plan): 10-15 rounds
- Large artifact (full codebase, launch readiness): 15-20+ rounds

**Never stop immediately after finding a ship-stopper.** There is usually a second one nearby — they cluster around contract boundaries and security rules.

## Origin

This methodology was developed during a production React application code review where 28 rounds found 14 production bugs — including 2 ship-stoppers that would have broken every API call and every image upload in production. None were caught by the initial single-pass review.

The key insight: a single review pass catches roughly 60% of issues. Each subsequent pass with a DIFFERENT LENS catches 5-15% more. The most critical bugs hide behind assumptions that feel obviously correct.
