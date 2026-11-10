---
ddx:
  id: US-006
  authoring:
    home: repo
  depends_on:
    - FEAT-003
---

# US-006: Set Capacity and Auto-sequence

**Feature**: FEAT-003 — Sequencing and Capacity
**Feature Requirements**: PER-01, PER-02, PER-03, PER-04, PER-05, PER-06, PER-07, PER-08, SEQ-01, SEQ-02, SEQ-03, SEQ-04, SEQ-05, SEQ-06, SEQ-07, SEQ-08, SEQ-09, SEQ-10, SEQ-11, SEQ-12, SEQ-13, SEQ-14
**PRD Requirements**: FR-7, FR-9
**Priority**: P0

## Story

**As a** Maya, the sole PM at a 12-person B2B SaaS company
**I want** to set the team's capacity for each planning period and place
the ranked backlog into those periods, by hand or in one auto-sequencing
action
**So that** Maya gets a first sequenced plan that respects capacity and
blockers within the PRD's one-sitting target of under 60 minutes for a
50–100 item backlog.

## Context

Maya plans one six-person team per quarter and today places items into
spreadsheet columns with no capacity limit. This story covers FR-7 and FR-9
together because auto-sequencing cannot be shown without periods that hold
a capacity budget; the PRD P0-4 sketch tests them as one scenario. It
exercises the PER and SEQ requirements of FEAT-003: default horizons,
budgets, keyboard placement, the rank-order placement rule, the reasons for
each placement and non-placement, and the one-step revert. Effort and rank
come from FEAT-002; findings on the resulting plan belong to US-007.

## Walkthrough

1. Maya opens a new roadmap; Roadsmith shows three open periods in order:
   Now, Next, Later, each without a capacity budget.
2. Maya sets Now's capacity to 10 and Next's capacity to 10, leaving Later
   unbounded.
3. Roadsmith shows "0 / 10, 10 left" for Now and Next and "0 placed" for
   Later.
4. The backlog holds three unplaced items in rank order: A (Effort 6), B
   (Effort 6, blocked by C), C (Effort 3).
5. Maya runs auto-sequencing.
6. Roadsmith places A and C in Now and B in Next, shows Now at "9 / 10, 1
   left" and Next at "6 / 10, 4 left", and summarizes "Now: 2 placed, Next:
   1 placed, 0 left unplaced".
7. Maya opens item B; Roadsmith states B is in Next because Now had 1 left
   against B's Effort of 6, and B's blocker C is in Now.
8. Maya moves C from Now to Next using only the keyboard; Roadsmith
   announces "C moved to Next" and shows Now at "6 / 10" and Next at "9 /
   10".

## Acceptance Criteria

- [ ] **US-006-AC1** — Given Maya creates a new roadmap, when the roadmap
  opens, then it shows open periods Now, Next, Later in that order with no
  capacity budgets.
- [ ] **US-006-AC2** — Given period Now has no budget, when Maya sets its
  capacity to 10, then Now shows "0 / 10, 10 left".
- [ ] **US-006-AC3** — Given Now has capacity 10, when Maya enters -5 as its
  capacity, then Roadsmith rejects the value with a reason and Now keeps
  capacity 10.
- [ ] **US-006-AC4** — Given periods Now, Next, Later, when Maya adds a
  period named "Q2 2027" between Next and Later, then the periods read Now,
  Next, Q2 2027, Later.
- [ ] **US-006-AC5** — Given item A is placed in Next, when Maya moves Next
  above Now, then A is still placed in Next.
- [ ] **US-006-AC6** — Given Next holds 2 placed items, when Maya deletes
  Next, then Roadsmith asks Maya to confirm and states that 2 items will
  become unplaced.
- [ ] **US-006-AC7** — Given Maya confirmed deleting Next with 2 placed
  items, when the deletion completes, then both items are unplaced.
- [ ] **US-006-AC8** — Given item A is unplaced, when Maya places A in Now
  using only the keyboard, then A is placed in Now.
- [ ] **US-006-AC9** — Given Maya moves item C to Next using only the
  keyboard, when the move completes, then assistive technology announces
  that C is in Next.
- [ ] **US-006-AC10** — Given Now (capacity 10), Next (capacity 10), and
  unplaced items ranked A (Effort 6), B (Effort 6, blocked by C), C (Effort
  3), when Maya runs auto-sequencing, then A and C are in Now and B is in
  Next.
- [ ] **US-006-AC11** — Given item D is placed in Next before a run, when
  Maya runs auto-sequencing, then D is still in Next.
- [ ] **US-006-AC12** — Given unplaced item E has no Effort, when Maya runs
  auto-sequencing, then E stays unplaced with the reason "no Effort
  estimate".
- [ ] **US-006-AC13** — Given Now and Next each have capacity 10 and no
  other period exists, when Maya auto-sequences an unplaced item F with
  Effort 12, then F stays unplaced with the reason "no period with enough
  remaining capacity".
- [ ] **US-006-AC14** — Given Now has 1 left after F was skipped, when the
  same run reaches lower-ranked item G with Effort 1, then G is placed in
  Now.
- [ ] **US-006-AC15** — Given item H is blocked by E and E has no Effort,
  when Maya runs auto-sequencing, then H stays unplaced with a reason naming
  E as an unplaced blocker.
- [ ] **US-006-AC16** — Given periods Now (capacity 10) and Later
  (unbounded), when Maya auto-sequences an item with Effort 40, then the
  item is placed in Later.
- [ ] **US-006-AC17** — Given a run has finished, when Maya views the run
  summary, then it shows the count placed in each period and each unplaced
  item with its reason.
- [ ] **US-006-AC18** — Given auto-sequencing placed B in Next, when Maya
  opens item B, then Roadsmith shows B's rank, that its blocker C is in Now,
  and that Now was skipped for lack of remaining capacity.
- [ ] **US-006-AC19** — Given Maya has just run auto-sequencing and made no
  other change, when Maya reverts the run, then every placement is as it was
  before the run.
- [ ] **US-006-AC20** — Given Maya moved an item after the last run, when
  Maya looks for the revert action, then it is unavailable.
- [ ] **US-006-AC21** — Given the same plan state, when Maya runs
  auto-sequencing, reverts, and runs it again, then both runs produce the
  same placements.
- [ ] **US-006-AC22** — Given the AI service is unavailable, when Maya runs
  auto-sequencing, then the run completes and places items.

## Edge Cases

- **Item ranked above its blocker**: B ranks above C but is blocked by C;
  auto-sequencing places C first and then B, no earlier than C's period
  (FEAT-003 A3).
- **Dependent fits in its blocker's period**: Auto-sequencing may place both
  in the same period; US-007 shows the resulting same-period warning
  (FEAT-003 A6).
- **No open periods**: The run places nothing and lists every candidate
  with the reason "no open period".
- **Capacity 0**: Auto-sequencing places nothing into that period.
- **Duplicate period name**: Roadsmith rejects a second period named "Next"
  in the same roadmap and keeps the existing name.
- **Revert after a failed run**: A run that fails part-way changes no
  placements, so there is nothing to revert.

## Test Scenarios

| Scenario | AC ID | Input / State | Action | Expected Result |
|----------|-------|---------------|--------|-----------------|
| Default horizons | US-006-AC1 | New roadmap | Open it | Now, Next, Later; no budgets |
| Set a budget | US-006-AC2 | Now unbounded | Set capacity 10 | "0 / 10, 10 left" |
| Reject negative budget | US-006-AC3 | Now capacity 10 | Enter -5 | Rejected with reason; Now capacity 10 |
| Add a calendar period | US-006-AC4 | Now, Next, Later | Add "Q2 2027" after Next | Now, Next, Q2 2027, Later |
| Reorder keeps placement | US-006-AC5 | A in Next | Move Next above Now | Order Next, Now, Later; A in Next |
| Delete period with items asks | US-006-AC6 | Next holds A, D | Delete Next | Confirmation states 2 items become unplaced |
| Delete period unplaces items | US-006-AC7 | Next holds A, D; deletion confirmed | Complete deletion | A, D unplaced |
| Keyboard placement | US-006-AC8 | A unplaced | Place A in Now by keyboard | A in Now |
| Keyboard move is announced | US-006-AC9 | C in Now | Move C to Next by keyboard | Announcement names C and Next |
| PRD P0-4 sketch | US-006-AC10 | Now cap 10, Next cap 10; ranked A (E=6), B (E=6, blocked by C), C (E=3); all unplaced | Run auto-sequencing | Now: A, C (9 / 10); Next: B (6 / 10) |
| Existing placement kept | US-006-AC11 | D (E=2) in Next; A, B, C unplaced as above | Run auto-sequencing | D in Next; Next 8 / 10 |
| No Effort | US-006-AC12 | E unplaced, no Effort | Run auto-sequencing | E unplaced; "no Effort estimate" |
| Fits nowhere | US-006-AC13 | Now cap 10, Next cap 10 only; F (E=12) unplaced | Run auto-sequencing | F unplaced; "no period with enough remaining capacity" |
| Smaller item fills gap | US-006-AC14 | Only Now (cap 10) and Next (cap 10); Now 9 / 10 from A, C; Next 6 / 10 from B; ranked F (E=12), G (E=1) unplaced | Run auto-sequencing | F unplaced; G in Now (10 / 10) |
| Blocker never placed | US-006-AC15 | E no Effort; H (E=2) blocked by E | Run auto-sequencing | H unplaced; reason names E |
| Unbounded Later | US-006-AC16 | Now cap 10, Later unbounded; I (E=40) unplaced | Run auto-sequencing | I in Later |
| Run summary | US-006-AC17 | P0-4 sketch plus E (no Effort) | Run auto-sequencing | "Now: 2, Next: 1"; E listed with "no Effort estimate" |
| Placement reason | US-006-AC18 | After P0-4 sketch run | Open B | Rank shown; blocker C in Now; Now skipped, 1 left vs Effort 6 |
| Revert | US-006-AC19 | After P0-4 sketch run | Revert run | A, B, C unplaced |
| Revert unavailable | US-006-AC20 | After P0-4 run, Maya moved A to Next | Look for revert | Revert unavailable |
| Deterministic | US-006-AC21 | P0-4 sketch state | Run, revert, run | Identical placements both times |
| AI outage | US-006-AC22 | AI service stubbed to fail; P0-4 sketch state | Run auto-sequencing | A, C in Now; B in Next |

## Dependencies

- **Stories**: US-005 (dependencies must exist for blocker-aware placement).
- **Feature Spec**: FEAT-003 — Sequencing and Capacity
- **Feature Requirements**: PER-01 through PER-08; SEQ-01 through SEQ-14
- **PRD Requirements**: FR-7, FR-9 (bundled: FR-9 auto-sequencing needs
  FR-7 periods with budgets to be demonstrated; PRD sketch P0-4 covers both)
- **External**: FEAT-002 (Prioritization) for Effort, effort unit, and rank
  order.

## Out of Scope

- Reporting violations or warnings on the placed plan (US-007).
- Closing a period (US-008).
- Choosing or renaming the effort unit (FEAT-002).
- Dates on periods, or splitting one item across periods.
- An auto-sequencing mode that moves already placed items or optimizes
  packing.
