---
ddx:
  id: US-007
  authoring:
    home: repo
  depends_on:
    - FEAT-003
---

# US-007: See Plan Violations

**Feature**: FEAT-003 — Sequencing and Capacity
**Feature Requirements**: CHK-01, CHK-02, CHK-03, CHK-04, CHK-05, CHK-06, CHK-07, CHK-09, CHK-10, CHK-11, CHK-12
**PRD Requirements**: FR-10
**Priority**: P0

## Story

**As a** Maya, the sole PM at a 12-person B2B SaaS company
**I want** to see every place my placed plan breaks dependency order or
exceeds a period's capacity, and fix each one in place
**So that** Maya walks into the leadership review with zero violations and
can name the items and periods behind any problem raised.

## Context

Maya's last quarter slipped because a blocker sat in a later period than its
dependent and nothing flagged it. This story exercises the CHK requirements
of FEAT-003: continuous re-checking after every plan edit, the two FR-10
violation types, the four non-blocking warnings, the clean-plan state, and
navigation from a finding to its items and periods. The same-period warning
(CHK-04) is FEAT-003's proposal for a PRD open question; the operator may
turn it into a violation or remove it. The AI critique (PRD FR-14) reads
these findings but is not part of this story.

## Walkthrough

1. Maya opens roadmap "Q1 2027" with periods Now (capacity 10) and Next
   (capacity 10). Now holds A (Effort 6); Next holds C (Effort 3). B (Effort
   6) is blocked by C and unplaced.
2. Roadsmith shows "Plan clean: 0 violations, 0 warnings".
3. Maya moves B into Now.
4. Without a refresh, Roadsmith shows 2 violations: a dependency-order
   violation naming B in Now and its blocker C in Next, and a capacity
   violation naming Now at "12 / 10" with items A and B.
5. Maya selects the dependency-order violation and goes to item B from it.
6. Maya moves B to Next.
7. Roadsmith clears both violations, shows Next at "9 / 10", and reports a
   same-period warning naming B, C, and Next.
8. Roadsmith shows "Plan clean: 0 violations, 1 warning".

## Acceptance Criteria

- [ ] **US-007-AC1** — Given B is blocked by C and C is in Next, when Maya
  places B in Now, then Roadsmith reports a dependency-order violation
  naming B, C, Now, and Next.
- [ ] **US-007-AC2** — Given Now has capacity 10 and holds A (Effort 6),
  when Maya places B (Effort 6) in Now, then Roadsmith reports a capacity
  violation naming Now, "12 / 10", A, and B.
- [ ] **US-007-AC3** — Given the plan shows a dependency-order violation for
  B in Now, when Maya moves B to Next, then the violation disappears without
  a page reload.
- [ ] **US-007-AC4** — Given Now holds A (Effort 6) and B (Effort 6) against
  capacity 10, when Maya raises Now's capacity to 12, then the capacity
  violation disappears.
- [ ] **US-007-AC5** — Given Now holds A (Effort 4) against capacity 10,
  when Maya changes A's Effort to 11, then Roadsmith reports a capacity
  violation naming Now and "11 / 10".
- [ ] **US-007-AC6** — Given B is blocked by C, when Maya places B and C in
  the same period Next, then Roadsmith reports a same-period warning naming
  B, C, and Next.
- [ ] **US-007-AC7** — Given the plan's only finding is a same-period
  warning, when Maya views the findings summary, then it reads 0 violations
  and states that the plan is clean.
- [ ] **US-007-AC8** — Given item E has no Effort, when Maya places E in
  Now, then Roadsmith reports an unestimated-item warning naming E and Now.
- [ ] **US-007-AC9** — Given H is blocked by E and E is unplaced, when Maya
  places H in Next, then Roadsmith reports an unplaced-blocker warning
  naming H, E, and Next.
- [ ] **US-007-AC10** — Given periods Now, Next, Later where Next has no
  capacity budget, when Maya views the findings, then Roadsmith reports a
  capacity-not-set warning naming Next.
- [ ] **US-007-AC11** — Given Later is the last open period and has no
  capacity budget, when Maya views the findings, then no capacity-not-set
  warning names Later.
- [ ] **US-007-AC12** — Given Roadsmith reports a dependency-order violation
  naming B and C, when Maya chooses C from that violation, then Roadsmith
  takes Maya to item C.
- [ ] **US-007-AC13** — Given a plan with 2 violations and 1 warning, when
  Maya views the findings summary, then it shows 2 violations and 1 warning
  as separate counts.
- [ ] **US-007-AC14** — Given B is blocked by C, B is in Now, and C is in
  Next, when Maya deletes the dependency, then the dependency-order
  violation disappears.
- [ ] **US-007-AC15** — Given the AI service is unavailable, when Maya
  places B in Now ahead of its blocker C in Next, then Roadsmith reports the
  dependency-order violation.
- [ ] **US-007-AC16** — Given a screen reader is running on the plan view,
  when Maya's edit creates a violation, then the new violation count is
  announced without moving keyboard focus.

## Edge Cases

- **Two blockers in later periods**: B blocked by C (Next) and D (Later),
  with B in Now, yields two dependency-order violations, one per blocker.
- **Chain inversion**: A blocked by B blocked by C, with A in Now, B in
  Next, C in Later, yields two violations: A–B and B–C.
- **Unestimated item in an over-capacity period**: The item counts 0 toward
  placed effort; the period's capacity violation lists it among placed items
  and the unestimated-item warning also names it.
- **Unbounded period with heavy load**: An unbounded period never produces a
  capacity violation, whatever its placed effort.
- **Period deleted**: Findings that named the deleted period disappear;
  items it held become unplaced and produce no placement findings.
- **Same-period ruling changes**: If the operator rules same-period
  dependencies a violation, AC6 and AC7 change; no other AC depends on that
  decision.

## Test Scenarios

| Scenario | AC ID | Input / State | Action | Expected Result |
|----------|-------|---------------|--------|-----------------|
| Dependency order (PRD P0-5 sketch) | US-007-AC1 | Now cap 10 holds A (E=6); Next holds C (E=3); B (E=6) blocked by C | Place B in Now | Violation names B, C, Now, Next |
| Capacity (PRD P0-5 sketch) | US-007-AC2 | Same state | Place B in Now | Violation names Now, "12 / 10", A, B |
| Fix by moving | US-007-AC3 | After the two rows above | Move B to Next | Dependency-order violation gone; Next 9 / 10 |
| Fix by budget | US-007-AC4 | Now holds A, B (12) vs cap 10 | Set Now cap 12 | Capacity violation gone; Now 12 / 12 |
| Effort change | US-007-AC5 | Now holds A (E=4), cap 10 | Set A's Effort to 11 | Capacity violation "11 / 10" |
| Same-period warning | US-007-AC6 | B blocked by C | Place B and C in Next | Warning names B, C, Next |
| Warning is not blocking | US-007-AC7 | Only the same-period warning | View summary | "0 violations, 1 warning"; plan clean |
| Unestimated item | US-007-AC8 | E has no Effort | Place E in Now | Warning names E, Now |
| Unplaced blocker | US-007-AC9 | H blocked by E; E unplaced | Place H in Next | Warning names H, E, Next |
| Missing budget | US-007-AC10 | Now cap 10; Next no budget; Later no budget | View findings | Capacity-not-set warning names Next |
| Last period exempt | US-007-AC11 | Same as above | View findings | No capacity-not-set warning for Later |
| Navigate from finding | US-007-AC12 | Violation for B in Now, C in Next | Choose C in the violation | Item C opens |
| Separate counts | US-007-AC13 | B in Now with C in Next; Now 12 / 10; E (no Effort) in Now | View summary | 2 violations; 1 warning |
| Remove the edge | US-007-AC14 | B in Now; C in Next; B blocked by C | Delete the dependency | No dependency-order violation |
| AI outage | US-007-AC15 | AI service stubbed to fail | Place B in Now; C in Next | Dependency-order violation shown |
| Screen reader announcement | US-007-AC16 | Clean plan; screen reader on | Place B in Now ahead of C | Count change announced; focus stays on B's control |

## Dependencies

- **Stories**: US-005 (dependencies), US-006 (periods, budgets, placement).
- **Feature Spec**: FEAT-003 — Sequencing and Capacity
- **Feature Requirements**: CHK-01 through CHK-07, CHK-09 through CHK-12
  (CHK-08 is exercised in US-008)
- **PRD Requirements**: FR-10
- **External**: FEAT-002 (Prioritization) for Effort values.

## Out of Scope

- AI critique findings and proposed diffs (FEAT-004; PRD FR-14, FR-15).
- Automatic fixes; Roadsmith reports findings and Maya changes the plan.
- Suppressing or dismissing an individual finding.
- Checks against dates, people, or other roadmaps.
- Dependency findings involving closed periods (US-008).
