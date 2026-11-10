---
ddx:
  id: US-008
  authoring:
    home: repo
  depends_on:
    - FEAT-003
---

# US-008: Close a Period

**Feature**: FEAT-003 — Sequencing and Capacity
**Feature Requirements**: CLS-01, CLS-02, CLS-03, CLS-04, CLS-05, CLS-06, CLS-07, CLS-08, CLS-09, CHK-06, CHK-08
**PRD Requirements**: FR-11
**Priority**: P1

## Story

**As a** Maya, the sole PM at a 12-person B2B SaaS company
**I want** to mark each item in a finished period as shipped, slipped, or
dropped and close the period
**So that** Maya can report the period's on-plan delivery rate (shipped ÷
committed) to leadership and plan the next period from what was actually
delivered.

## Context

The vision's North Star is the share of committed items that ship in their
planned period, and the PRD measures it from period-close records. Today
Maya reconstructs what shipped from a tracker after the fact, and slipped
items stay in last quarter's spreadsheet column. This story exercises the
CLS requirements of FEAT-003 and the two checks a close changes: a shipped
blocker satisfies its dependents (CHK-08), and a dropped or slipped blocker
leaves its placed dependents with an unplaced-blocker warning (CHK-06).
Committed items are the items placed in the period when Maya closes it
(FEAT-003 A8).

## Walkthrough

1. Maya opens roadmap "Q1 2027" with open periods Jan 2027 (capacity 10),
   Feb 2027 (capacity 10), and Later. Jan 2027 holds A (Effort 3), B
   (Effort 2), C (Effort 2), E (Effort 2), and G (Effort 1). Feb 2027 holds K
   (Effort 4, blocked by C) and L (Effort 3, blocked by E).
2. Maya chooses to close Jan 2027.
3. Roadsmith lists the five items placed in Jan 2027, each awaiting an
   outcome.
4. Maya marks A, C, and G shipped, B slipped, and E dropped.
5. Maya chooses to complete the close.
6. Roadsmith asks Maya to confirm and states that recorded outcomes cannot
   be changed afterwards.
7. Maya confirms.
8. Roadsmith records Jan 2027's on-plan delivery rate as "3 / 5, 60%" and
   removes Jan 2027 from the open periods; Feb 2027 is now the earliest
   open period.
9. B and E appear unplaced in the backlog with their Jan 2027 outcomes;
   A, C, and G are marked shipped.
10. Roadsmith reports an unplaced-blocker warning naming L, E, and Feb 2027,
    and no finding for K and C.

## Acceptance Criteria

- [ ] **US-008-AC1** — Given Jan 2027 is the earliest open period and holds
  5 items, when Maya starts closing Jan 2027, then Roadsmith lists those 5
  items for an outcome each.
- [ ] **US-008-AC2** — Given Jan 2027 is open and earlier than Feb 2027,
  when Maya tries to close Feb 2027, then Roadsmith does not start the
  close and states that Jan 2027 must close first.
- [ ] **US-008-AC3** — Given 4 of Jan 2027's 5 items have an outcome, when
  Maya tries to complete the close, then the period stays open and Roadsmith
  names the item without an outcome.
- [ ] **US-008-AC4** — Given every item in Jan 2027 has an outcome, when
  Maya completes the close, then Roadsmith asks for confirmation stating
  that outcomes cannot be changed afterwards.
- [ ] **US-008-AC5** — Given the confirmation is showing, when Maya cancels
  it, then Jan 2027 stays open with all 5 items placed in it.
- [ ] **US-008-AC6** — Given 3 shipped, 1 slipped, and 1 dropped outcome,
  when Maya confirms the close, then Roadsmith records the rate "3 / 5,
  60%".
- [ ] **US-008-AC7** — Given Jan 2027 was closed with zero placed items,
  when Maya views its record, then the rate reads "no committed items".
- [ ] **US-008-AC8** — Given A was marked shipped in a confirmed close, when
  Maya runs auto-sequencing, then A is not placed.
- [ ] **US-008-AC9** — Given B was marked slipped in a confirmed close, when
  Maya views the backlog, then B is unplaced.
- [ ] **US-008-AC10** — Given E was marked dropped in a confirmed close,
  when Maya views the backlog, then E is still in the backlog and unplaced.
- [ ] **US-008-AC11** — Given Jan 2027 was closed, when Maya views the open
  periods, then Jan 2027 is absent and Feb 2027 is first.
- [ ] **US-008-AC12** — Given K in Feb 2027 is blocked by C and C shipped in
  the closed Jan 2027, when Maya views the findings, then no finding names
  both K and C.
- [ ] **US-008-AC13** — Given L in Feb 2027 is blocked by E and E was
  dropped at close, when Maya views the findings, then an unplaced-blocker
  warning names L, E, and Feb 2027.
- [ ] **US-008-AC14** — Given Jan 2027 was closed, when Maya opens the close
  history, then it shows Jan 2027's close date, its 5 items, each item's
  outcome, and the rate "3 / 5, 60%".
- [ ] **US-008-AC15** — Given Maya uses only the keyboard, when Maya sets
  all 5 outcomes and confirms the close, then the close completes as with a
  pointer.

## Edge Cases

- **Item moved out before close**: An item Maya moved from Jan 2027 to Feb
  2027 before closing is not committed to Jan 2027 and does not appear in
  its close (FEAT-003 A8).
- **Slipped blocker with placed dependent**: When B slips and an item in
  Feb 2027 is blocked by B, that dependent shows an unplaced-blocker
  warning until Maya places B again.
- **All items slipped**: The rate records "0 / 5, 0%", which differs from a
  period with no committed items.
- **Unestimated item in the period**: It needs an outcome like any other
  item and counts in the rate, since the rate counts items, not effort
  (FEAT-003 A9).
- **Close fails part-way**: No outcome, placement, or rate changes, and Jan
  2027 stays open (FEAT-003 Reliability).
- **Only one open period**: Maya can close it; the roadmap then has no open
  periods until Maya adds one.

## Test Scenarios

| Scenario | AC ID | Input / State | Action | Expected Result |
|----------|-------|---------------|--------|-----------------|
| Start close | US-008-AC1 | Jan 2027 holds A, B, C, E, G | Start closing Jan 2027 | 5 items listed, none with an outcome |
| Out-of-order close | US-008-AC2 | Jan 2027 and Feb 2027 open | Close Feb 2027 | Refused; message says Jan 2027 must close first |
| Missing outcome | US-008-AC3 | A, B, C, E marked; G unmarked | Complete close | Jan 2027 open; G named as missing an outcome |
| Confirmation | US-008-AC4 | All 5 marked | Complete close | Confirmation says outcomes cannot be changed |
| Cancel | US-008-AC5 | Confirmation showing | Cancel | Jan 2027 open; A, B, C, E, G placed in it |
| Rate recorded | US-008-AC6 | A, C, G shipped; B slipped; E dropped | Confirm close | Rate "3 / 5, 60%" |
| Empty period | US-008-AC7 | Jan 2027 holds no items | Close and confirm | Rate "no committed items" |
| Shipped leaves the pool | US-008-AC8 | A shipped at close; Feb 2027 has room | Run auto-sequencing | A not placed |
| Slipped returns | US-008-AC9 | B slipped at close | View backlog | B unplaced |
| Dropped kept | US-008-AC10 | E dropped at close | View backlog | E in backlog, unplaced |
| Period leaves the plan | US-008-AC11 | Jan 2027 closed | View open periods | Feb 2027, Later |
| Shipped blocker satisfied | US-008-AC12 | K (Feb 2027) blocked by C; C shipped | View findings | No finding names K and C |
| Dropped blocker warns | US-008-AC13 | L (Feb 2027) blocked by E; E dropped | View findings | Unplaced-blocker warning: L, E, Feb 2027 |
| Close history | US-008-AC14 | Jan 2027 closed on 2027-01-31 | Open close history | Jan 2027, 2027-01-31, 5 items with outcomes, "3 / 5, 60%" |
| Keyboard only | US-008-AC15 | Jan 2027 holds 5 items | Set outcomes and confirm by keyboard | Jan 2027 closed; rate "3 / 5, 60%" |

## Dependencies

- **Stories**: US-006 (periods and placements), US-005 (dependencies, for
  AC12 and AC13), US-007 (findings display).
- **Feature Spec**: FEAT-003 — Sequencing and Capacity
- **Feature Requirements**: CLS-01 through CLS-09; CHK-06, CHK-08
- **PRD Requirements**: FR-11
- **External**: None.

## Out of Scope

- Reopening a closed period or editing its recorded outcomes.
- Recording why an item slipped or was dropped.
- Aggregating rates across periods, roadmaps, or teams.
- Renaming horizons automatically after a close.
- Importing shipped status from an issue tracker.
