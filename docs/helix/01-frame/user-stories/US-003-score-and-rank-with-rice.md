---
ddx:
  id: US-003
  authoring:
    home: repo
  depends_on:
    - FEAT-002
---

# US-003: Score and Rank Items with RICE

**Feature**: FEAT-002 — Prioritization
**Feature Requirements**: SCORE-01, SCORE-02, SCORE-03, SCORE-04, SCORE-05, SCORE-06, SCORE-07, SCORE-08, SCORE-09, RANK-01, RANK-02, RANK-03, RANK-04, RANK-05, RANK-06
**PRD Requirements**: FR-4
**Priority**: P0

## Story

**As a** Maya, sole PM at a 12-person B2B SaaS company
**I want** to enter Reach, Impact, Confidence, and Effort for each backlog
item and read one ranked list built from them
**So that** I can answer "why this before that?" in the leadership review
from the score inputs, with the same order every time the list is opened.

## Context

Maya has imported 60–120 items and needs a defensible order before
sequencing them into periods. Today the order lives in a spreadsheet sort
that is easy to forget to re-run. This story exercises Scoring and Ranking in
FEAT-002: input ranges, the score formula, tie-breaking, the place of
unscored items, and live re-ranking. The resulting rank order feeds
auto-sequencing in FEAT-003 (FR-9), and Effort is the same unit that FEAT-003
counts against capacity.

## Walkthrough

1. Maya opens the roadmap "Q1 plan", which holds three imported items with no
   RICE inputs: A "Usage-based billing", B "SAML single sign-on", and F
   "Audit log export", created in that order.
2. Roadsmith lists all three as unscored, in creation order, each naming
   Reach, Impact, Confidence, and Effort as missing.
3. Maya enters A: Reach 500, Impact 2, Confidence 80%, Effort 4.
4. Roadsmith shows A at rank 1 with score 200 and its four inputs. B and F
   remain unscored below it.
5. Maya enters B: Reach 300, Impact 3, Confidence 100%, Effort 3.
6. Roadsmith shows B at rank 1 with score 300 and A at rank 2 with score 200,
   without a page reload.
7. Maya enters F: Reach 100, Impact 1, Effort 2, and leaves Confidence empty.
8. Roadsmith keeps F below the ranked items with no rank position and names
   Confidence as missing.
9. Maya tries to set A's Effort to 0.
10. Roadsmith rejects the value, states that Effort must be greater than 0 and
    at most 10,000, and A keeps Effort 4.
11. Maya changes A's Reach from 500 to 1000.
12. Roadsmith shows A at rank 1 with score 400 and B at rank 2 with score 300,
    without a page reload.

## Acceptance Criteria

- [ ] **US-003-AC1** — Given an item with all four inputs within range, when
  Maya stores the last missing input, then the item shows score
  (Reach × Impact × Confidence ÷ 100) ÷ Effort.
- [ ] **US-003-AC2** — Given A scores 200 and B scores 300, when Maya opens
  the ranked view, then B holds rank 1 and A holds rank 2.
- [ ] **US-003-AC3** — Given B ranks above A, when Maya changes A's Reach from
  500 to 1000, then A shows score 400 at rank 1 without a page reload.
- [ ] **US-003-AC4** — Given an item with a stored value for an input, when
  Maya submits a value outside that input's allowed range, then Roadsmith
  rejects it and names the allowed range.
- [ ] **US-003-AC5** — Given Roadsmith rejected an out-of-range value, when
  Maya views the item, then the previously stored value, score, and rank are
  unchanged.
- [ ] **US-003-AC6** — Given item F is missing Confidence, when Maya opens the
  ranked view, then F appears after every scored item with no rank position.
- [ ] **US-003-AC7** — Given item F is missing Confidence, when Maya opens the
  ranked view, then F is labeled unscored and names Confidence as missing.
- [ ] **US-003-AC8** — Given two unscored items where G was created before H,
  when Maya opens the ranked view, then G is listed before H.
- [ ] **US-003-AC9** — Given two items with equal scores and different
  Effort, when Maya opens the ranked view, then the item with lower Effort
  ranks higher.
- [ ] **US-003-AC10** — Given two items with equal scores and equal Effort,
  when Maya opens the ranked view, then the item with higher Confidence ranks
  higher.
- [ ] **US-003-AC11** — Given two items with equal score, Effort, and
  Confidence, when Maya opens the ranked view, then the item created earlier
  ranks higher.
- [ ] **US-003-AC12** — Given item A holds rank 2 of 3, when Maya clears A's
  Impact, then A moves to the unscored items and the item formerly at rank 3
  holds rank 2.
- [ ] **US-003-AC13** — Given a scored item, when Maya opens the ranked view,
  then the item's row shows its rank, score, Reach, Impact, Confidence, and
  Effort.
- [ ] **US-003-AC14** — Given a score with more than one decimal place, when
  Maya opens the ranked view, then the score is shown rounded to one decimal
  place.
- [ ] **US-003-AC15** — Given the AI service is unavailable, when Maya stores
  an input, then the score and ranked order update as they do with the AI
  service available.
- [ ] **US-003-AC16** — Given a fixed set of stored inputs, when Maya opens
  the ranked view in two separate browser sessions, then both sessions show
  the same order.

## Edge Cases

- **Reach 0 or Confidence 0**: The item scores 0 and ranks below every
  positive score, above all unscored items.
- **Mathematically equal scores from different inputs**: They tie and follow
  the tie-break rules; floating-point drift must not split them.
- **Item deleted**: Remaining scored items renumber without gaps.
- **Score with long decimal expansion**: Item with Reach 200, Impact 1,
  Confidence 100%, Effort 3 shows 66.7. Ranking uses the unrounded value.
- **Non-numeric entry such as "high" for Impact**: Rejected under US-003-AC4
  with the allowed Impact values named.
- **Effort edited**: Score and rank update; FEAT-003 capacity totals also
  change, which is FEAT-003's behavior to verify.

## Test Scenarios

| Scenario | AC ID | Input / State | Action | Expected Result |
|----------|-------|---------------|--------|-----------------|
| Score computed | US-003-AC1 | A: Reach 500, Impact 2, Confidence 80%, Effort empty | Store Effort 4 | A score 200 |
| Score with fractional Effort | US-003-AC1 | E: Reach 100, Impact 3, Confidence 100%, Effort empty | Store Effort 1.5 | E score 200 |
| PRD sketch order | US-003-AC2 | A: 500, 2, 80%, 4 (200); B: 300, 3, 100%, 3 (300) | Open ranked view | B rank 1, A rank 2 |
| Live re-rank | US-003-AC3 | A 200 rank 2; B 300 rank 1 | Change A Reach to 1000 | A score 400 rank 1; B rank 2; no reload |
| Effort 0 rejected | US-003-AC4 | A Effort 4 | Submit Effort 0 | Rejected; range "greater than 0, at most 10,000" named |
| Effort three decimals rejected | US-003-AC4 | A Effort 4 | Submit Effort 2.125 | Rejected; two-decimal limit named |
| Impact off scale | US-003-AC4 | A Impact 2 | Submit Impact 1.5 | Rejected; 0.25, 0.5, 1, 2, 3 named |
| Confidence above 100 | US-003-AC4 | A Confidence 80% | Submit Confidence 120 | Rejected; 0–100 named |
| Negative Reach | US-003-AC4 | A Reach 500 | Submit Reach -5 | Rejected; 0–1,000,000,000 named |
| Fractional Reach | US-003-AC4 | A Reach 500 | Submit Reach 12.5 | Rejected; whole number required |
| Stored value kept | US-003-AC5 | A: 500, 2, 80%, 4; rank 2 | Submit Effort 0 | A Effort 4, score 200, rank 2 |
| Unscored placement | US-003-AC6 | A 200, B 300; F: 100, 1, empty, 2 | Open ranked view | Order B, A, then F with no rank |
| Missing input named | US-003-AC7 | F: Reach 100, Impact 1, Confidence empty, Effort 2 | Open ranked view | F labeled unscored; "Confidence" named missing |
| Unscored creation order | US-003-AC8 | G created, then H; both with no inputs | Open ranked view | G above H |
| Tie on Effort | US-003-AC9 | A: 500, 2, 80%, 4 (200); C: 200, 1, 100%, 1 (200) | Open ranked view | C above A |
| Tie on Confidence | US-003-AC10 | C: 200, 1, 100%, 1 (200); D: 400, 1, 50%, 1 (200) | Open ranked view | C above D |
| Tie on creation | US-003-AC11 | J then K, both 100, 2, 50%, 1 (100) | Open ranked view | J above K |
| Equal scores from different inputs | US-003-AC9 | L: 300, 1, 100%, 3 (100); M: 100, 1, 100%, 1 (100) | Open ranked view | M (Effort 1) above L (Effort 3) |
| Clear an input | US-003-AC12 | B 300 rank 1; A 200 rank 2; Q: 150, 1, 100%, 1 (150) rank 3 | Clear A Impact | B rank 1; Q rank 2; A unscored, Impact missing |
| Row shows inputs | US-003-AC13 | A: 500, 2, 80%, 4 | Open ranked view | A row shows rank, 200, 500, 2, 80%, 4 |
| Rounded display | US-003-AC14 | N: 200, 1, 100%, 3 | Open ranked view | N shows 66.7 |
| AI outage | US-003-AC15 | AI service stubbed to fail; A 200, B 300 | Change A Reach to 1000 | A score 400 rank 1 |
| Zero Reach | US-003-AC6 | P: 0, 2, 80%, 4 (0); F unscored; A 200 | Open ranked view | A rank 1, P rank 2, then F |
| Same order across sessions | US-003-AC16 | A: 500, 2, 80%, 4 (200); B: 300, 3, 100%, 3 (300); C: 200, 1, 100%, 1 (200) | Open ranked view in two sessions | Both show B, C, A |

## Dependencies

- **Stories**: Backlog Intake story for creating items (FR-1 or FR-2) must
  exist so items are present; scoring can also be exercised on manually
  created items.
- **Feature Spec**: FEAT-002 — Prioritization.
- **Feature Requirements**: SCORE-01 to SCORE-09, RANK-01 to RANK-06.
- **PRD Requirements**: FR-4.
- **External**: None. No LLM call is involved; US-003-AC15 needs the AI
  service stubbed to fail.

## Out of Scope

- Evidence notes, unsupported flags, and sensitivity checks (US-004).
- AI-proposed RICE inputs (FR-12).
- Placing ranked items into periods and capacity totals (FEAT-003).
- Manually dragging an item to a different rank.
- ICE or custom scoring formulas (PRD P2).
