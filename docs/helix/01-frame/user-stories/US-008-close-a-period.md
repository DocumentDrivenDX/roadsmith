---
ddx:
  id: US-008
  authoring:
    home: repo
  depends_on:
    - FEAT-003
---

# US-008: Start and Close a Period

**Feature**: FEAT-003 — Sequencing and Capacity
**Feature Requirements**: CLS-01, CLS-02, CLS-03, CLS-04, CLS-05, CLS-06, CLS-07, CLS-08, CLS-09, CLS-10, CLS-11, CLS-12, CLS-13, CLS-14, CHK-06, CHK-08
**PRD Requirements**: FR-11
**Priority**: P1

## Story

**As a** Maya, the sole PM at a 12-person B2B SaaS company
**I want** to lock in what the team committed to when a period starts, and
at its end mark each item shipped, slipped (with a reason), or dropped
**So that** Maya can report an honest on-plan delivery rate and the share of
slips caused by dependency or capacity problems, and plan the next period
from what was actually delivered.

## Context

The vision's North Star is the share of committed items that ship in their
planned period without being re-sequenced because of a dependency or
capacity problem. The operator decided on 2026-11-10 that the commitment is
recorded when the period starts, that every slip records a reason, and that
shipped and dropped items are archived at close. This story exercises the CLS
requirements of FEAT-003 and the two checks a close changes. A shipped
blocker satisfies its dependents (CHK-08). A dropped blocker leaves its
placed dependents with an unplaced-blocker warning (CHK-06).

## Walkthrough

1. Maya opens roadmap "Q1 2027" with open periods Jan 2027 (capacity 10),
   Feb 2027 (capacity 10), and Later. Jan 2027 holds A (Effort 3), B
   (Effort 2), C (Effort 2), E (Effort 2), and G (Effort 1). Feb 2027 holds K
   (Effort 4, blocked by C) and L (Effort 3, blocked by E). Item H (Effort 1)
   is unplaced.
2. On 2027-01-04 Maya chooses to start Jan 2027 and confirms.
3. Roadsmith shows Jan 2027 as started with 5 committed items.
4. Mid-month, Maya moves G to Feb 2027 and places H in Jan 2027. Roadsmith
   shows H as added, and G still counts in Jan 2027's commitment.
5. On 2027-01-31 Maya chooses to close Jan 2027.
6. Roadsmith lists the 5 committed items and the 1 added item. G is preset
   to slipped.
7. Maya marks A, C, and H shipped, B slipped, and E dropped. She gives B the
   reason "capacity" and G the reason "dependency".
8. Maya chooses to complete the close. Roadsmith asks her to confirm and
   states that outcomes and reasons cannot be changed afterwards. Maya
   confirms.
9. Roadsmith records Jan 2027's on-plan delivery rate as "2 / 5, 40%" and its
   preventable-slip share as "2 / 5, 40%". Jan 2027 leaves the open periods.
10. A, C, H, and E disappear from the backlog and ranking, and appear in Jan
    2027's history. B is unplaced in the backlog. G stays placed in Feb
    2027.
11. Roadsmith reports an unplaced-blocker warning naming L, E (dropped), and
    Feb 2027. It reports no finding for K and C.

## Acceptance Criteria

- [ ] **US-008-AC1** — Given Jan 2027 is the earliest open period and no
  period is started, when Maya starts Jan 2027 and confirms, then Roadsmith
  records its 5 placed items as committed.
- [ ] **US-008-AC2** — Given Jan 2027 is started, when Maya tries to start
  Feb 2027, then Roadsmith refuses and states that Jan 2027 must close first.
- [ ] **US-008-AC3** — Given Jan 2027 started with 5 committed items, when
  Maya places H in Jan 2027, then H is shown as added and the committed count
  stays 5.
- [ ] **US-008-AC4** — Given committed item G was moved from Jan 2027 to Feb
  2027, when Maya starts closing Jan 2027, then G is listed with its outcome
  preset to slipped.
- [ ] **US-008-AC5** — Given 5 of the 6 listed items have an outcome, when
  Maya tries to complete the close, then the period stays open and Roadsmith
  names the item without an outcome.
- [ ] **US-008-AC6** — Given B is marked slipped with no reason, when Maya
  tries to complete the close, then the period stays open and Roadsmith names
  B as needing a slip reason.
- [ ] **US-008-AC7** — Given every item has an outcome and every slip has a
  reason, when Maya completes the close, then Roadsmith asks for confirmation
  stating that outcomes and reasons cannot be changed afterwards.
- [ ] **US-008-AC8** — Given the confirmation is showing, when Maya cancels
  it, then Jan 2027 stays started with its placements unchanged.
- [ ] **US-008-AC9** — Given A and C shipped, H (added) shipped, B and G
  slipped, and E dropped, when Maya confirms the close, then the on-plan
  delivery rate reads "2 / 5, 40%".
- [ ] **US-008-AC10** — Given B slipped for capacity and G slipped for
  dependency, when Maya confirms the close, then the preventable-slip share
  reads "2 / 5, 40%".
- [ ] **US-008-AC11** — Given Jan 2027 was started with zero placed items,
  when Maya closes it, then both figures read "no committed items".
- [ ] **US-008-AC12** — Given A shipped and E was dropped in a confirmed
  close, when Maya views the backlog and ranking, then neither A nor E
  appears.
- [ ] **US-008-AC13** — Given B slipped and is not placed in a later period,
  when the close is confirmed, then B is unplaced in the backlog.
- [ ] **US-008-AC14** — Given G slipped and is placed in Feb 2027, when the
  close is confirmed, then G stays placed in Feb 2027.
- [ ] **US-008-AC15** — Given Jan 2027 was closed, when Maya views the open
  periods, then Jan 2027 is absent and Feb 2027 is first.
- [ ] **US-008-AC16** — Given K in Feb 2027 is blocked by C and C shipped,
  when Maya views the findings, then no finding names both K and C.
- [ ] **US-008-AC17** — Given L in Feb 2027 is blocked by E and E was
  dropped, when Maya views the findings, then an unplaced-blocker warning
  names L, E as dropped, and Feb 2027.
- [ ] **US-008-AC18** — Given Jan 2027 was closed, when Maya opens its
  history, then it shows the start and close dates, committed and added
  items, each outcome and slip reason, and both figures.
- [ ] **US-008-AC19** — Given Maya uses only the keyboard, when she starts
  Jan 2027, sets every outcome and reason, and confirms the close, then the
  result matches the pointer flow.

## Edge Cases

- **Committed item deleted mid-period**: It stays in the commitment and is
  preset to dropped at close (FEAT-003 A14).
- **All committed items slipped**: The rate reads "0 / 5, 0%", which differs
  from a period with no committed items.
- **Unestimated committed item**: It needs an outcome like any other item and
  counts in both figures, since they count items, not effort (FEAT-003 A9).
- **Close fails part-way**: No outcome, placement, archive, or figure
  changes, and Jan 2027 stays started (FEAT-003 Reliability).
- **Only one open period**: Maya can start and close it. The roadmap then has
  no open periods until Maya adds one.
- **Every slip reason is "other"**: The preventable-slip share reads
  "0 / 5, 0%" even when the on-plan rate is low.

## Test Scenarios

| Scenario | AC ID | Input / State | Action | Expected Result |
|----------|-------|---------------|--------|-----------------|
| Start records commitment | US-008-AC1 | Jan 2027 holds A, B, C, E, G | Start Jan 2027, confirm | Started; committed = 5 |
| One started period | US-008-AC2 | Jan 2027 started | Start Feb 2027 | Refused; Jan 2027 must close first |
| Added item | US-008-AC3 | Jan 2027 started | Place H (Effort 1) in Jan 2027 | H marked added; committed count 5 |
| Moved-out preset | US-008-AC4 | G moved Jan → Feb mid-period | Start closing Jan 2027 | G listed, preset slipped |
| Missing outcome | US-008-AC5 | A, B, C, E marked; G preset slipped; H unmarked | Complete close | Period open; H named as missing an outcome |
| Missing reason | US-008-AC6 | B slipped, no reason | Complete close | Period open; B named as needing a reason |
| Confirmation | US-008-AC7 | All outcomes and reasons set | Complete close | Confirmation says outcomes and reasons are final |
| Cancel | US-008-AC8 | Confirmation showing | Cancel | Jan 2027 started; placements unchanged |
| On-plan rate | US-008-AC9 | A, C, H shipped; B, G slipped; E dropped | Confirm | "2 / 5, 40%" |
| Preventable share | US-008-AC10 | B capacity, G dependency | Confirm | "2 / 5, 40%" |
| Empty commitment | US-008-AC11 | Jan 2027 started with no items | Close, confirm | Both figures "no committed items" |
| Archive | US-008-AC12 | A shipped, E dropped | View backlog and ranking | A and E absent |
| Slipped returns | US-008-AC13 | B slipped, unplaced elsewhere | Confirm close | B unplaced in backlog |
| Slipped stays placed | US-008-AC14 | G slipped, placed in Feb 2027 | Confirm close | G in Feb 2027 |
| Period leaves plan | US-008-AC15 | Jan 2027 closed | View open periods | Feb 2027, Later |
| Shipped blocker | US-008-AC16 | K blocked by C; C shipped | View findings | No finding names K and C |
| Dropped blocker | US-008-AC17 | L blocked by E; E dropped | View findings | Warning: L, E (dropped), Feb 2027 |
| History | US-008-AC18 | Started 2027-01-04, closed 2027-01-31 | Open history | Dates, 5 committed + 1 added, outcomes, reasons, both figures |
| Keyboard only | US-008-AC19 | Same as walkthrough | Start, set outcomes and reasons, confirm by keyboard | Same figures as AC9 and AC10 |

## Dependencies

- **Stories**: US-006 (periods and placements), US-005 (dependencies, for
  AC16 and AC17), US-007 (findings display).
- **Feature Spec**: FEAT-003 — Sequencing and Capacity
- **Feature Requirements**: CLS-01 through CLS-14; CHK-06, CHK-08
- **PRD Requirements**: FR-11
- **External**: None.

## Out of Scope

- Reopening a closed period or editing its recorded outcomes or reasons.
- Free-text slip explanations beyond the three reasons.
- Aggregating figures across periods, roadmaps, or teams.
- Renaming horizons automatically after a close.
- Importing shipped status from an issue tracker.
