---
ddx:
  id: FEAT-003
  authoring:
    home: repo
  depends_on:
    - prd
---

# Feature Specification: FEAT-003 — Sequencing and Capacity

**Feature ID**: FEAT-003
**Priority**: P0 (FR-7–FR-10); P1 (FR-11, period close)
**Owner**: Not yet assigned; the operator names the owner
**Covered PRD Subsystem(s)**: Sequencing and Capacity
**Covered PRD Requirements**: FR-7, FR-8, FR-9, FR-10, FR-11
**Cross-Subsystem Rationale**: None — single subsystem.

## Overview

Sequencing and Capacity implements PRD P0-3 ("blocked by" dependencies with
cycles rejected, FR-8), P0-4 (planning periods with capacity budgets and
manual or automatic placement, FR-7 and FR-9), P0-5 (continuous
dependency-order and capacity checks, FR-10), and P1-3 (period close with the
on-plan delivery rate, FR-11). It turns a ranked backlog into a placed plan
and tells the PM, before committing, where that plan cannot be delivered.

## Ideal Future State

Maya opens a roadmap with Now, Next, and Later already in place and enters
the team's capacity for Now and Next. Maya declares which items block which;
a dependency that would loop is refused with the loop spelled out. One action
places every unplaced item in rank order, and each placement states why it
landed in that period. A findings list shows every dependency inversion and
every overloaded period, names the items and periods involved, and clears
the moment Maya fixes the plan. Same-period dependencies, unestimated items,
and periods without a budget appear as warnings that do not block a clean
plan. Every move a mouse user makes by dragging, a keyboard user makes with
the same result.

When a period ends, Maya marks each item shipped, slipped, or dropped. The
on-plan delivery rate for that period is recorded, slipped and dropped items
return to the unplaced pool, and the next plan starts from what actually
happened.

## Problem Statement

- **Current situation**: PMs on 5–50 person teams place items into quarters
  or Now/Next/Later columns in spreadsheets and boards that hold no
  dependency edges and no capacity budget (PRD Problem; competitive analysis
  F14, F16).
- **Pain points**: An item lands in a period before its blocker, or a
  period is planned past what the team can deliver, and nobody sees it until
  mid-quarter. Maya's billing change slipped because its data-model blocker
  sat in a later period, and Maya found out in week five (PRD persona).
- **Desired outcome**: Every dependency-order and capacity violation is
  visible before commit. A 50–100 item backlog reaches a plan with zero
  violations in one sitting (PRD Success Criteria). Each period close yields
  an on-plan delivery rate, the input to the vision North Star.

## Functional Areas

| Area | User question or job | Feature responsibility |
|------|----------------------|------------------------|
| Periods and Capacity (PER) | What periods am I planning, and how much can the team do in each? | Hold the ordered periods and each period's capacity budget; show used and remaining capacity. |
| Dependencies (DEP) | Which items must finish before which? | Hold "blocked by" edges within one roadmap; reject cycles and name them. |
| Placement and Auto-sequencing (SEQ) | Where does each item go, and why there? | Manual placement by pointer or keyboard; rank-order auto-sequencing with a stated reason per placement and per unplaced item. |
| Plan Checks (CHK) | Where does my plan break? | Continuously report violations (blocking) and warnings (non-blocking), each naming the items and periods involved. |
| Period Start and Close (CLS) | What did we commit to, and what did we deliver against it? | Snapshot the commitment when a period starts; at close, record shipped, slipped (with reason), or dropped per item, the on-plan delivery rate, and the preventable-slip share; archive shipped and dropped items. |

### Domain terms

- **Period**: a named, ordered slot in one roadmap's plan. A *horizon*
  (Now, Next, Later) and a *calendar period* (for example "Q1 2027") are
  both periods; the name is a label and carries no start or end date.
- **Capacity budget**: a period's limit in the roadmap's effort unit, the
  same unit as RICE Effort (FEAT-002). A period with no budget is
  *unbounded*.
- **Placed effort**: the sum of Effort over items placed in a period. An
  item without Effort contributes 0.
- **Blocker / dependent**: in "B is blocked by C", C is the blocker and B is
  the dependent.
- **Violation**: a blocking check finding (dependency-order or capacity, as
  FR-10 defines them). A *clean plan* has zero violations.
- **Warning**: a non-blocking check finding. Warnings never count against a
  clean plan.
- **Open / closed period**: a period is open until the PM closes it
  (FR-11). Only open periods take placements and are checked.
- **Started period**: an open period whose commitment has been recorded.
  At most one period is started at a time.
- **Committed items**: the items placed in a period at the moment the PM
  starts it. Moving a committed item out of the period later does not remove
  it from the commitment.
- **Added items**: items placed in a started period after it started. They
  get an outcome at close but do not count in the period's rates.
- **Slip reason**: why a committed item did not ship in its period:
  dependency, capacity, or other.
- **Archived item**: a shipped or dropped item after its period closes. It
  leaves the backlog and ranking and stays visible in the period's history.

## Task Model

| Task | Goal | Users | Needs | Rules | Realized by | Key |
|------|------|-------|-------|-------|-------------|-----|
| Declare which items block which | Every known "blocked by" edge is recorded, with no cycles | Sole or lead PM, team of 5–50 | "Tell me where my plan won't fit before I commit to it" | — | US-005 | declare-dependencies |
| Set period capacity and sequence the backlog | Ranked items are placed into periods within capacity and after their blockers | Sole or lead PM, team of 5–50 | "Tell me where my plan won't fit before I commit to it" | — | US-006 | set-capacity-and-sequence |
| Find and fix plan violations | The plan has zero violations and every warning is understood | Sole or lead PM, team of 5–50; Head of Product over 2–5 PMs | "Tell me where my plan won't fit before I commit to it" | — | US-007 | fix-plan-violations |
| Start a period, then close it | The commitment is recorded at start; at close each item has an outcome, each slip has a reason, and the period's rates are recorded | Sole or lead PM, team of 5–50 | "Tell me where my plan won't fit before I commit to it" | — | US-008 | close-period |

## Requirements

### Functional Requirements by Area

#### Periods and Capacity

PER-01. The system must let the PM keep an ordered list of periods for each
roadmap, with each period name unique within that roadmap.

PER-02. A new roadmap must start with three open horizon periods in the
order Now, Next, Later, none with a capacity budget.

PER-03. The PM must be able to add, rename, reorder, and delete periods,
including periods with calendar names, at any position in the order.

PER-04. The PM must be able to set a period's capacity budget to any number
greater than or equal to 0 in the roadmap's effort unit, or clear it to make
the period unbounded.

PER-05. The system must reject a negative or non-numeric capacity budget,
state why, and keep the period's previous budget.

PER-06. The system must show, for each bounded period, its placed effort,
its capacity budget, and its remaining capacity (for example "9 / 10, 1
left"); for each unbounded period it must show placed effort only.

PER-07. Reordering periods must keep every item in the period it was placed
in.

PER-08. Deleting a period that holds placed items must require the PM to
confirm, with the number of affected items stated; on confirm, those items
become unplaced.

#### Dependencies

DEP-01. The PM must be able to declare that one item is blocked by another
item in the same roadmap.

DEP-02. The system must reject a dependency that would create a cycle, and
the rejection must name every item in the cycle in path order, starting and
ending with the same item (for example "A → B → C → A").

DEP-03. A rejected dependency must leave the roadmap's dependencies exactly
as they were before the attempt.

DEP-04. The system must reject an item declared as blocked by itself, as a
cycle of one item.

DEP-05. Declaring a dependency that already exists must not create a second
edge, and the system must say that the dependency already exists.

DEP-06. Each item must show its blockers and its dependents.

DEP-07. The PM must be able to remove a dependency.

DEP-08. When an item is deleted, the system must remove every dependency in
which that item is a blocker or a dependent.

DEP-09. The system must accept a non-cyclic dependency that conflicts with
current placements; the conflict appears as a check finding (CHK-02 or
CHK-04), not as a rejection.

#### Placement and Auto-sequencing

SEQ-01. The PM must be able to place an unplaced item into any open period,
move a placed item to another open period, and return a placed item to
unplaced.

SEQ-02. Every placement change the PM can make by dragging must also be
completable by keyboard alone, with the same resulting placement.

SEQ-03. After a placement change made by keyboard, the system must announce
the item's new period to assistive technology.

SEQ-04. The PM must be able to run auto-sequencing on a roadmap; it must
consider only unplaced items that are not shipped, and must not move any
item that is already placed.

SEQ-05. Auto-sequencing must place items so that the result equals this
rule: repeatedly take the highest-ranked candidate whose blockers are all
placed or shipped, and place it into the earliest open period that comes no
earlier than every placed blocker's period and whose remaining capacity is
at least the item's Effort.

SEQ-06. Auto-sequencing must treat an unbounded period as having enough
remaining capacity for any item.

SEQ-07. Auto-sequencing must leave an item without Effort unplaced and give
the reason "no Effort estimate".

SEQ-08. Auto-sequencing must leave an item unplaced when no qualifying
period has enough remaining capacity, give the reason "no period with enough
remaining capacity", and continue with lower-ranked items.

SEQ-09. Auto-sequencing must leave unplaced an item whose blocker is still
unplaced when the run ends, and the reason must name that blocker.

SEQ-10. The placements one auto-sequencing run adds must not raise any
period's placed effort above its capacity budget.

SEQ-11. After a run, the system must show how many items it placed in each
period and list each item it left unplaced with its reason.

SEQ-12. Each item placed by auto-sequencing must show why it is in its
period: its rank, the latest blocker period that constrained it, and the
earlier periods skipped for lack of remaining capacity.

SEQ-13. The PM must be able to revert the most recent auto-sequencing run in
one action, restoring the placements from before the run, until any other
plan change is made.

SEQ-14. Given the same items, ranks, Effort values, dependencies, periods,
budgets, and existing placements, auto-sequencing must produce the same
placements.

#### Plan Checks

CHK-01. The system must re-run all plan checks after every change to a
placement, dependency, period, capacity budget, item Effort, or item
deletion, and show the updated findings without a manual refresh.

CHK-02. The system must report a dependency-order violation for each
dependent placed in an earlier open period than one of its blockers, naming
the dependent, the blocker, and both periods.

CHK-03. The system must report a capacity violation for each bounded period
whose placed effort exceeds its capacity budget, naming the period, the
placed effort, the budget (for example "12 / 10"), and the items placed in
it.

CHK-04. The system must report a same-period warning for each dependent
placed in the same period as one of its blockers, naming both items and the
period. Whether this is a warning, a violation, or allowed is a PRD open
question; the warning is this feature's proposal.

CHK-05. The system must report an unestimated-item warning for each placed
item without Effort, naming the item and its period.

CHK-06. The system must report an unplaced-blocker warning for each placed
dependent whose blocker is neither placed nor shipped, naming both items and
the dependent's period.

CHK-07. The system must report a capacity-not-set warning for each unbounded
open period that is not the last open period.

CHK-08. The system must not report a dependency finding for a blocker that
was marked shipped at a period close.

CHK-09. The system must show the number of violations and the number of
warnings separately, and must state that the plan is clean when violations
are zero.

CHK-10. From each finding, the PM must be able to go to each item and period
it names.

CHK-11. Plan checks, manual placement, and auto-sequencing must work when
the AI service is unavailable (PRD FR-17).

CHK-12. When the number of violations or warnings changes, the system must
announce the new counts to assistive technology without moving keyboard
focus.

#### Period Start and Close

CLS-01. The PM must be able to start the earliest open period when no other
period is started. Starting must require confirmation.

CLS-02. Starting a period must record the items placed in it at that moment
as its committed items and show their count. Later placement changes must not
change the recorded commitment.

CLS-03. A started period must stay open: it must keep taking placements and
must stay subject to plan checks. Items placed after start must be shown as
added, separate from committed items.

CLS-04. The PM must be able to close the started period, and only that
period.

CLS-05. Closing must require an outcome of shipped, slipped, or dropped for
every committed item and every added item. A committed item that is no
longer placed in the period at close must be preset to slipped. The close
must not complete while any item lacks an outcome.

CLS-06. Every slipped committed item must have a slip reason of dependency,
capacity, or other. The close must not complete while any slipped committed
item lacks a reason.

CLS-07. The close must require the PM to confirm, and the confirmation must
state that recorded outcomes and reasons cannot be changed afterwards.

CLS-08. On close, the system must record the period's on-plan delivery rate
as shipped committed items ÷ committed items. It must show the rate as both a
fraction and a percentage (for example "3 / 5, 60%"). Added items must not
count in the rate.

CLS-09. On close, the system must record the period's preventable-slip share
as committed items slipped with reason dependency or capacity ÷ committed
items, shown the same way.

CLS-10. A period closed with zero committed items must record both figures as
"no committed items", not as 0%.

CLS-11. On close, each shipped item and each dropped item must be archived.
It must leave the backlog, the ranking, and the pool of placeable items, and
must stay visible in the closed period's history.

CLS-12. On close, each slipped item must return to the backlog unplaced,
unless the PM already placed it in a later open period, where it must stay.
Its outcome and reason must be kept in the closed period's history.

CLS-13. A closed period must leave the list of open periods and must take no
further placements, checks, or auto-sequencing.

CLS-14. The system must keep each closed period's name, start date, close
date, committed items, added items, outcomes, slip reasons, and both
figures, and must show them to the PM.

### Non-Functional Requirements

- **Performance** *(working values accepted by the operator on 2026-11-10;
  not yet backed by measurement)*:
  - After any edit, updated check findings appear within 200 ms at the
    95th percentile for a roadmap of 500 items, 2,000 dependencies, and 24
    open periods.
  - A dependency declaration is accepted or rejected for a cycle within
    100 ms at the 95th percentile on the same roadmap.
  - An auto-sequencing run over 500 unplaced items completes, with its
    summary shown, within 1 s at the 95th percentile.
- **Security**: Periods, dependencies, placements, findings, and close
  records are readable and changeable only by members of the roadmap's
  workspace (PRD FR-18). Sequencing and checks send no roadmap data to the
  LLM provider.
- **Scalability**: Each roadmap supports at least 500 items, 2,000
  dependencies, and 24 open periods at the performance targets above, about
  four times the PRD persona's 120-item backlog.
- **Reliability**: An auto-sequencing run, a revert, and a period close each
  apply completely or not at all; a failure leaves placements, outcomes, and
  rates as they were. Check results are deterministic: the same plan always
  yields the same findings.
- **Usability**: Every drag interaction in this feature has a keyboard
  alternative, and the feature's screens meet WCAG 2.x AA (Web Content
  Accessibility Guidelines, W3C, level AA), per the `a11y-wcag-aa` concern.

## User Stories

- [US-005 — Declare dependencies](../user-stories/US-005-declare-dependencies.md)
- [US-006 — Set capacity and auto-sequence](../user-stories/US-006-set-capacity-and-auto-sequence.md)
- [US-007 — See plan violations](../user-stories/US-007-see-plan-violations.md)
- [US-008 — Start and close a period](../user-stories/US-008-close-a-period.md)

## Edge Cases and Error Handling

- **No open periods**: Auto-sequencing places nothing and leaves every
  candidate unplaced with the reason "no open period"; checks report
  nothing.
- **All periods bounded and full**: Every remaining candidate is left
  unplaced with "no period with enough remaining capacity"; no capacity
  violation is created by the run.
- **Period already over capacity before a run**: The run adds nothing to
  that period; the existing capacity violation stays reported.
- **Existing manual dependency-order violation before a run**: The run does
  not move either item; the violation stays reported, and new placements
  respect the blocker's current period.
- **Item Effort changed after placement**: The item stays where it is;
  capacity checks re-run with the new Effort.
- **Effort removed from a placed item**: The item stays placed, contributes
  0 to placed effort, and an unestimated-item warning appears.
- **Capacity budget set to 0**: Any placed item with Effort above 0 causes a
  capacity violation; auto-sequencing places no items into that period,
  since items without Effort are never auto-placed (A4).
- **Blocker shipped at period close**: The blocker is archived and its
  dependency is satisfied; its dependents show no finding for it.
- **Blocker dropped at period close**: The blocker is archived as dropped.
  Each placed dependent shows the unplaced-blocker warning (CHK-06), naming
  the blocker as dropped, until the PM removes the dependency.
- **Blocker slipped at period close**: The blocker becomes unplaced; its
  placed dependents show unplaced-blocker warnings until the blocker is
  placed again.
- **Period started with zero placed items**: The start is allowed; the
  commitment is empty and both figures at close read "no committed items".
- **Committed item deleted mid-period**: The item stays in the recorded
  commitment and is preset to dropped at close (assumption A14).
- **Starting a later period while an earlier one is started**: Not allowed;
  the PM is told to close the started period first (CLS-01).
- **Rank ties**: Auto-sequencing uses the ranked order exactly as FEAT-002
  defines it, including FEAT-002's tie-break; this feature adds no
  tie-break of its own.
- **AI-proposed placement or dependency changes**: An accepted AI diff
  changes placements or dependencies through the same rules (DEP-02, SEQ-01)
  and triggers CHK-01; a proposal that would create a cycle is withheld by
  PRD FR-16.

## Success Metrics

- Share of placed items with an Effort estimate at period start (PRD risk
  mitigation: "Measure the estimated-item share").
- Share of auto-sequenced placements the PM moves by hand before the period
  closes, as a signal of trust in the rank-order rule.
- On-plan delivery rate and preventable-slip share per closed period
  (CLS-08, CLS-09), the inputs to the PRD's North Star metric.
- Share of slips by reason (dependency, capacity, other) across closed
  periods.
- Share of roadmaps whose non-final open periods all have a capacity budget.

## Constraints and Assumptions

- **Constraint**: Sequencing and checks are deterministic and work without
  the LLM (PRD FR-17; principle 8, deterministic core).
- **Constraint**: Every drag interaction has a keyboard alternative
  (`a11y-wcag-aa`).
- **Assumption A1**: A new roadmap starts with Now, Next, Later and no
  budgets, so the PM edits a default instead of configuring periods
  (Experience principle "Useful in one sitting").
- **Assumption A2**: Any period may be unbounded. An unbounded period never
  produces a capacity violation, and auto-sequencing treats it as always
  fitting. The last open period (Later by default) is unbounded without a
  warning; any other unbounded period raises CHK-07.
- **Assumption A3**: Auto-sequencing defers an item until all its blockers
  are placed, so an item ranked above its blocker is placed after it
  (SEQ-05). This reproduces the PRD P0-4 sketch result.
- **Assumption A4**: Auto-sequencing never places items without Effort; the
  PM may place them by hand, where they count as 0 effort and raise CHK-05.
- **Assumption A5**: An item that fits no period is skipped, and smaller
  lower-ranked items may still fill remaining capacity (SEQ-08).
  Auto-sequencing is greedy in rank order, not an optimal packing.
- **Assumption A6**: Auto-sequencing may place a dependent in its blocker's
  period, as FR-9's "no earlier than" allows; that placement raises the
  CHK-04 warning. The operator confirmed on 2026-11-10 that same-period is
  a warning (PRD FR-10).
- **Assumption A7**: A placed dependent whose blocker is unplaced is a
  warning (CHK-06), not a violation, because FR-10 defines violations only
  by period order.
- **Decision A8** (operator, 2026-11-10): Committed items are those placed
  in the period when the PM starts it (CLS-02). Committed items moved out
  mid-period count as slipped (CLS-05).
- **Assumption A9**: Both figures count items, not effort. Dropped committed
  items count in the denominator.
- **Assumption A10**: Only the earliest open period can be started, only one
  period is started at a time, and outcomes are final once the close is
  confirmed. Horizons do not rename themselves after a close; the PM renames
  or adds periods.
- **Assumption A11**: A one-step revert of the latest auto-sequencing run
  (SEQ-13) satisfies "prefer reversible decisions"; there is no general
  undo history in this feature.
- **Assumption A12**: The performance and scale targets in Non-Functional
  Requirements are set at about four times the PRD persona backlog. The
  operator accepted them as working values on 2026-11-10; measurement in
  Test confirms or revises them.
- **Decision A13** (operator, 2026-11-10): At close, shipped and dropped
  items are archived out of the backlog and ranking, and slipped items return
  to the backlog unplaced (CLS-11, CLS-12). FEAT-002 ranks only non-archived
  items.
- **Assumption A14**: A committed item deleted mid-period stays in the
  recorded commitment and is preset to dropped at close, so deletion cannot
  improve the on-plan delivery rate.
- **Decision** (operator, 2026-11-10): Every slipped committed item records
  a reason of dependency, capacity, or other (CLS-06), so the North Star can
  separate preventable slips.

## Dependencies

- **Other features**: FEAT-002 (Prioritization) supplies each item's Effort,
  the effort unit, the ranked order, and its tie-break. FEAT-001 (Backlog
  Intake) supplies items and item deletion, which removes the item's
  dependencies and placement (PRD FR-1, FR-2). FEAT-005 (Accounts and
  Workspaces) scopes access (PRD FR-18). FEAT-004 (AI Assistance) reads plan
  check findings for critique and applies accepted placement and dependency
  diffs through this feature's rules (PRD FR-14–FR-16).
- **External services**: None. Sequencing and checks call no external
  service.
- **PRD requirements**: P0-3 (FR-8), P0-4 (FR-7, FR-9), P0-5 (FR-10), P1-3
  (FR-11). PRD Constraints: FR-17 independence from AI and the WCAG AA
  keyboard alternative.

## Out of Scope

- **Day-level Gantt scheduling**: periods carry no start or end dates, and
  items have no start dates or durations (PRD non-goal).
- **Named-person allocation**: capacity is one team budget per period;
  there are no per-person, per-skill, or per-goal budgets (PRD non-goal).
- **Cross-roadmap dependencies**: a blocker and its dependent must belong
  to the same roadmap. Priya's three team roadmaps are checked
  independently; cross-team edges would need a shared model across
  roadmaps that no PRD requirement asks for.
- **Other dependency types**: no start-to-start, "relates to", or soft
  dependencies; "blocked by" is the only edge type.
- **Splitting an item across periods**: an item sits in one period with
  its full Effort.
- **Automatic re-sequencing**: placements change only on a PM action or an
  accepted diff; editing ranks or Effort never moves placed items.
- **Optimal packing**: auto-sequencing does not maximize capacity use or
  minimize unplaced items.
- **Velocity forecasting**: past on-plan delivery rates do not suggest
  future capacity budgets.
- **Slip reasons and reopening**: no cause is recorded for a slip, and a
  closed period cannot be reopened or its outcomes edited.
- **AI critique of the plan**: owned by FEAT-004 AI Assistance (PRD
  FR-14).
