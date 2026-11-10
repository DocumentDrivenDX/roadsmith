---
ddx:
  id: FEAT-002
  authoring:
    home: repo
  depends_on:
    - prd
---

# Feature Specification: FEAT-002 — Prioritization

**Feature ID**: FEAT-002
**Priority**: P0 (FR-4); FR-5 and FR-6 are P1
**Owner**: Roadsmith product owner (unassigned; assumption)
**Covered PRD Subsystem(s)**: Prioritization
**Covered PRD Requirements**: FR-4, FR-5, FR-6
**Cross-Subsystem Rationale**: None — single subsystem.

## Overview

Prioritization implements PRD P0-2 (FR-4): every item can carry RICE inputs,
and the system keeps a deterministic, always-current ranked order of the
roadmap's items. It also implements PRD P1-1 (FR-5) and P1-2 (FR-6), which
expose score inputs that lack evidence and show how far a rank depends on a
guessed Confidence. RICE is the Reach, Impact, Confidence, Effort scoring
framework: score = (Reach × Impact × Confidence) ÷ Effort.

## Ideal Future State

Maya enters or accepts RICE inputs for each item and sees one ranked list
that is always current and never depends on who opened it or when. Every
rank shows its reason: the four inputs, the score, and which inputs rest on
written evidence. Before the leadership review, Maya picks a top-ranked item,
lowers its Confidence to a skeptical value, and sees the rank it would hold.
That answers "would this still be first if we were less sure?" without
touching the stored plan. Priya reviewing the roadmap sees at a glance which
ranks rest on unsupported inputs.

## Problem Statement

- **Current situation**: PMs score backlogs with RICE in spreadsheets. The
  formula, input scales, and sort order vary by sheet, and a re-sort is a
  manual step that is easy to skip.
- **Pain points**: Confidence and Effort get inflated or bent toward the PM's
  existing preference [competitive analysis F19, F20, F21]. Leadership cannot
  tell a well-evidenced rank from a guessed one, and the PM cannot show how
  fragile a rank is.
- **Desired outcome**: Identical inputs always yield an identical ranked
  order. Every unsupported input is visible wherever its score appears. The
  rank effect of a lowered Confidence takes one action to see.

## Functional Areas

| Area | User question or job | Feature responsibility |
|------|----------------------|------------------------|
| Scoring | What is this item's score, and are its inputs valid? | Accept RICE inputs within allowed ranges and compute the score. |
| Ranking | Where does this item sit relative to the others, and why? | Keep a deterministic, always-current order with tie-breaking and a defined place for unscored items. |
| Evidence | Which of these inputs are guesses? | Hold an evidence note per input and flag inputs without one as unsupported. |
| Sensitivity | Would this item keep its rank if Confidence were lower? | Show a hypothetical rank for a lowered Confidence without changing stored data. |

Two similar domain objects are kept separate:

- **Score**: the number computed from an item's four stored inputs. Only
  stored inputs produce a score; AI-proposed inputs awaiting acceptance
  (FR-12) do not.
- **Rank**: an item's position in the roadmap's ordered list. A rank depends
  on every other item's score, so it changes when another item's inputs
  change even if this item's score does not.

## Task Model

| Task | Goal | Users | Needs | Rules | Realized by | Key |
|------|------|-------|-------|-------|-------------|-----|
| Score and rank the backlog | Every fully scored item holds a rank, and the list reflects the latest inputs | Sole or lead PM, team of 5–50 | "Show me which scores I'm guessing at" | None defined yet | US-003 | score-and-rank |
| Check which inputs lack evidence and test rank sensitivity | The PM knows which ranks rest on guessed inputs before defending them | Sole or lead PM, team of 5–50; Head of Product over 2–5 PMs | "Show me which scores I'm guessing at" | None defined yet | US-004 | check-score-support |

## Requirements

### Functional Requirements by Area

#### Scoring

SCORE-01. Each item must accept four RICE inputs: Reach, Impact, Confidence,
and Effort. Each input may be empty independently of the others.

SCORE-02. The system must accept a Reach value only when it is a whole number
from 0 to 1,000,000,000 (assumption A1).

SCORE-03. The system must accept an Impact value only when it is one of 0.25,
0.5, 1, 2, or 3 (assumption A1).

SCORE-04. The system must accept a Confidence value only when it is a whole
number percentage from 0 to 100 (assumption A1).

SCORE-05. The system must accept an Effort value only when it is greater than
0 and at most 10,000, with at most two decimal places, in the roadmap's effort
unit (assumption A1). Effort is the same quantity that Sequencing (FEAT-003)
counts against period capacity (FR-7).

SCORE-06. When a submitted value is outside its allowed range, the system
must reject it, name the input and its allowed range, and keep the previously
stored value. The ranges in SCORE-02 to SCORE-05 apply to every path that
writes an input, including manual entry, import (FR-1), and accepting an
AI-proposed change (FR-12, FR-16).

SCORE-07. For an item with all four inputs stored, the system must compute
score = (Reach × Impact × Confidence ÷ 100) ÷ Effort. Item A with Reach 500,
Impact 2, Confidence 80%, and Effort 4 scores 200.

SCORE-08. An item missing one or more inputs must have no score. The system
must mark it unscored and name each missing input.

SCORE-09. The system must display a score rounded to at most one decimal
place. Ranking must compare unrounded scores, and two scores that are
mathematically equal must compare as equal (assumption A2).

#### Ranking

RANK-01. The system must order a roadmap's scored items by score, highest
first, and give each a distinct position from 1 to N, where N is the number
of scored items. Items archived at period close (shipped or dropped, FR-11)
must not appear in the ranking or count in N.

RANK-02. When two scored items have equal scores, the system must order them
by lower Effort first, then higher Confidence, then earlier creation in the
roadmap (assumption A3). No two items share a position.

RANK-03. The system must list unscored items after all scored items, in
order of creation in the roadmap, without a rank position (assumption A4).
This combined order is the "rank order" that auto-sequencing (FR-9) consumes.

RANK-04. When an input is stored or cleared, or an item is added to or
deleted from the roadmap, the ranked order must update without a page reload.

RANK-05. The ranked order must be a function of stored inputs and creation
order only. The same stored data must produce the same order for every
viewer, session, and edit history.

RANK-06. The ranked view must show, for each scored item, its rank position,
score, and four input values.

#### Evidence

EVID-01. Each of the four RICE inputs must accept an optional plain-text
evidence note of up to 2,000 characters (assumption A5).

EVID-02. The system must flag an input as unsupported when the input has a
value and its evidence note is empty or contains only whitespace. A missing
input is reported as missing (SCORE-08), not unsupported.

EVID-03. Wherever an item's score appears, the system must show which of its
inputs are unsupported. In FEAT-002 that covers the ranked view, the item
detail view, and the sensitivity check. Other features that display the
score, such as Sequencing (FEAT-003) and AI critique (FR-14), must show the
same flag.

EVID-04. The system must show an input's evidence note next to that input's
value in the item detail view.

EVID-05. Adding, editing, or removing an evidence note must not change the
item's score or rank.

EVID-06. Accepting an AI-proposed input (FR-12) must not supply an evidence
note. The AI rationale is not evidence, so the accepted input is unsupported
until the PM writes a note (assumption A6).

#### Sensitivity

SENS-01. For a selected scored item, the PM must be able to choose a
Confidence value that is a whole number from 0 up to one less than the stored
Confidence, and see the score and rank the item would hold with that value.

SENS-02. The system must compute the hypothetical rank with the rules in
RANK-01 and RANK-02, using the chosen Confidence for the selected item and
the stored inputs for every other item.

SENS-03. The system must show the item's current rank and score beside the
hypothetical rank and score.

SENS-04. A sensitivity check must not change the stored Confidence, the
stored score, or the ranked order shown for the roadmap, and nothing from the
check is saved when the PM leaves it.

SENS-05. The system must reject a chosen Confidence equal to or above the
stored value, or outside 0–100, and state the allowed range.

SENS-06. The system must not offer a sensitivity check for an unscored item.
It must name the missing inputs instead.

### Non-Functional Requirements

- **Performance**: For a roadmap of 500 items, the updated ranked order must
  be visible within 300 ms at the 95th percentile after an input is stored
  (assumption A7; no measurement backs this target yet). A sensitivity result
  for the same roadmap must appear within 300 ms at the 95th percentile after
  the PM chooses a value.
- **Scalability**: Scoring, ranking, and sensitivity must meet the
  performance target for roadmaps of up to 500 items. The PRD's primary
  persona has 60–120 items, and the v1 success fixture has 100 rows.
- **Reliability**: Scoring, ranking, evidence flags, and sensitivity must work
  with the AI service unavailable (FR-17). For any fixed set of stored inputs,
  1,000 repeated rankings must produce an identical order.
- **Security**: Score inputs and evidence notes are roadmap content. They are
  readable and writable only by members of the roadmap's workspace (FR-18) and
  are sent to the LLM provider only on an explicit AI request (PRD
  Constraints).
- **Usability**: Input entry, evidence entry, and the sensitivity check must
  be operable by keyboard alone and meet WCAG 2.2 AA (Web Content
  Accessibility Guidelines, W3C). The unsupported flag must carry a text or
  accessible-name label and must not rely on color alone (WCAG success
  criterion 1.4.1).

## User Stories

- [US-003 — Score and rank items with RICE](../user-stories/US-003-score-and-rank-with-rice.md)
- [US-004 — Flag unsupported scores and test rank sensitivity](../user-stories/US-004-flag-unsupported-scores-and-test-sensitivity.md)

## Edge Cases and Error Handling

- **Zero Reach or zero Confidence**: The item is scored with score 0 and
  ranks below every item with a positive score, above all unscored items.
- **Input cleared on a scored item**: The item loses its score, leaves the
  ranked positions, and joins the unscored items in creation order. Items
  below it move up one position.
- **Item deleted**: Remaining scored items are renumbered without gaps.
- **All items unscored**: The ranked view shows no rank positions and lists
  every item as unscored, naming its missing inputs.
- **Out-of-range value from import**: The value is not stored. Backlog Intake
  (FR-1) reports it at row level, and the item stays unscored for that input.
- **AI-proposed input out of range**: The proposal fails validation and is
  never offered as an applicable change (FR-16), using the ranges in
  SCORE-02 to SCORE-05.
- **AI-proposed inputs awaiting acceptance**: They do not affect any score,
  rank, or unsupported flag until accepted (FR-12).
- **Effort edited**: The score and rank update under RANK-04. The same edit
  changes the placed effort that Sequencing (FEAT-003) checks against capacity.
- **Stored Confidence lowered below an open sensitivity value**: The open
  check no longer meets SENS-01. The system must discard the chosen value and
  ask for a new one.
- **Teammate edits the same item (FR-19, P1)**: The last stored value wins.
  Live co-editing is a PRD non-goal, so each viewer's ranking reflects stored
  values when their view next updates.

## Success Metrics

- Unsupported-score share at commit (PRD learning goal): share of items
  placed in a period at period start with at least one unsupported input.
  Observed from period records (FR-11).
- Sensitivity checks per planning session: whether PMs use FR-6 before
  committing. Observed through product analytics; a learning goal with no
  target.

## Constraints and Assumptions

- **A1 — Input ranges**: Reach is a whole number 0–1,000,000,000; Impact uses
  the common RICE scale 0.25, 0.5, 1, 2, 3; Confidence is a whole-number
  percentage 0–100; Effort is greater than 0 and at most 10,000, with at most
  two decimal places. The operator accepted these ranges, and the other
  numeric limits and targets in this spec, as working values on 2026-11-10.
- **A2 — Exact comparison**: Mathematically equal scores tie even when
  computed from different inputs, for example 300 × 1 × 100% ÷ 3 and
  100 × 1 × 100% ÷ 1.
- **A3 — Tie-breaking**: Lower Effort first favors the cheaper item at equal
  value; higher Confidence next favors the better-evidenced estimate; creation
  order makes the order total. Assumption, not a PRD decision.
- **A4 — Unscored placement**: Unscored items follow all scored items in
  creation order. Import creates items in source-row order.
- **A5 — Evidence note length**: 2,000 characters.
- **A6 — AI rationale is not evidence**: Matches the product vision, in which
  Roadsmith flags drafted scores whose inputs lack evidence.
- **A7 — Performance target**: 300 ms p95 at 500 items is an unbacked target.
- **A8 — No manual rank override**: Rank is always computed from inputs.
  The PM changes a rank only by changing inputs; period placement is manual in
  Sequencing (FR-9).
- **A9 — Evidence kept on value change**: Editing an input's value keeps its
  evidence note. The PM is responsible for updating a stale note.
- **Constraint**: Scoring and ranking are deterministic and independent of the
  LLM (PRD Constraints; principle 8, "Deterministic core, AI at the edge").
- **Decision** (operator, 2026-11-10): Shipped and dropped items are
  archived at period close and leave the ranking (RANK-01; FEAT-003 CLS-11).
  Slipped items stay in the ranking.

## Dependencies

- **Other features**: Sequencing and Capacity (FEAT-003) consumes the rank
  order from RANK-03 for auto-sequencing (FR-9) and shares Effort as its
  capacity unit (FR-7); a change to either rule affects both features. Backlog
  Intake (FR-1, FR-2) creates items and may supply RICE values through
  optional estimate fields. AI Assistance (FR-12, FR-14, FR-16) proposes
  inputs, reports the unsupported-score finding, and validates proposals
  against SCORE-02 to SCORE-05. Accounts and Workspaces (FR-18) scopes access.
- **External services**: None. Prioritization does not call the LLM.
- **PRD requirements**: P0-2 (FR-4); P1-1 (FR-5); P1-2 (FR-6).

## Out of Scope

- **Alternative scoring models**: ICE, weighted formulas, and custom formulas
  are PRD P2 and are not framed.
- **Voting or group estimation**: Stakeholder voting, Priority Poker, and
  averaging several people's estimates are excluded; the PRD non-goals rule
  out voting and feedback collection.
- **Manual rank override**: Dragging an item to a rank position is not
  supported (A8).
- **Sensitivity for other inputs**: FR-6 covers Confidence only. Reach,
  Impact, and Effort what-ifs and multi-item scenarios are excluded.
- **Saved scenarios and score history**: Sensitivity results are not stored,
  and v1 keeps no history of past input values.
- **Evidence attachments or links to feedback tools**: Evidence is a plain-text
  note; feedback portals are a PRD non-goal.
- **Cross-roadmap ranking**: Ranking is per roadmap; Priya cannot rank items
  from three teams' roadmaps in one list.
- **AI drafting of inputs**: Owned by AI Assistance (FR-12).
