---
ddx:
  id: US-004
  authoring:
    home: repo
  depends_on:
    - FEAT-002
---

# US-004: Flag Unsupported Scores and Test Rank Sensitivity

**Feature**: FEAT-002 — Prioritization
**Feature Requirements**: EVID-01, EVID-02, EVID-03, EVID-04, EVID-05, SENS-01, SENS-02, SENS-03, SENS-04, SENS-05, SENS-06
**PRD Requirements**: FR-5, FR-6
**Priority**: P1

## Story

**As a** Maya, sole PM at a 12-person B2B SaaS company
**I want** to see which RICE inputs on my ranked items lack evidence, and to
check the rank a top item would hold at a lower Confidence
**So that** before the leadership review I know which ranks rest on guesses,
and I write evidence for, or stop defending, every rank that falls when its
Confidence drops.

## Context

Confidence and Effort are the inputs PMs most often bend toward a preferred
answer [competitive analysis F19–F21], and Maya cannot show leadership which
ranks are well evidenced. This story exercises the Evidence and Sensitivity
areas of FEAT-002. It bundles FR-5 and FR-6 deliberately: both serve the PRD
need "Show me which scores I'm guessing at", both read stored inputs without
changing a score, and Maya uses them in one review pass. One check finds a
guessed input; the other measures what that guess is worth in rank.

## Walkthrough

1. Maya opens the ranked view of "Q1 plan". A "Usage-based billing" holds
   rank 1 (Reach 1000, Impact 2, Confidence 80%, Effort 4; score 400). B
   "SAML single sign-on" holds rank 2 (Reach 300, Impact 3, Confidence 100%,
   Effort 3; score 300).
2. A's Reach and Effort have evidence notes; its Impact and Confidence do
   not. Every B input has a note.
3. Roadsmith shows A's row flagged with Impact and Confidence as unsupported.
   B's row shows no flag.
4. Maya opens a sensitivity check on A and chooses Confidence 50.
5. Roadsmith shows A's current rank 1 and score 400 beside a hypothetical
   rank 2 and score 250. The ranked view still shows A at rank 1 with
   Confidence 80%.
6. Maya closes the sensitivity check. A's stored Confidence is still 80%.
7. Maya opens A's item detail view and adds a Confidence evidence note:
   "8 of 10 churned accounts cited billing in exit interviews".
8. Roadsmith shows the note next to A's Confidence value, removes the
   Confidence flag, keeps the Impact flag, and keeps A at rank 1 with score
   400.

## Acceptance Criteria

- [ ] **US-004-AC1** — Given A's Confidence is 80% with no evidence note,
  when Maya opens the ranked view, then A's row flags Confidence as
  unsupported.
- [ ] **US-004-AC2** — Given A's Reach has an evidence note, when Maya opens
  the ranked view, then A's row does not flag Reach.
- [ ] **US-004-AC3** — Given A's Confidence evidence note contains only
  spaces, when Maya opens the ranked view, then A's row flags Confidence as
  unsupported.
- [ ] **US-004-AC4** — Given A's Confidence is flagged unsupported, when Maya
  opens A's item detail view, then the detail view flags Confidence as
  unsupported.
- [ ] **US-004-AC5** — Given A's Confidence is flagged unsupported, when Maya
  saves an evidence note on A's Confidence, then the Confidence flag is
  removed.
- [ ] **US-004-AC6** — Given A scores 400 at rank 1, when Maya saves an
  evidence note on A's Confidence, then A still scores 400 at rank 1.
- [ ] **US-004-AC7** — Given A's Confidence has an evidence note, when Maya
  opens A's item detail view, then the note appears next to the Confidence
  value.
- [ ] **US-004-AC8** — Given Maya enters a 2,001-character evidence note,
  when Maya saves it, then Roadsmith rejects the note and states the
  2,000-character limit.
- [ ] **US-004-AC9** — Given A (score 400, rank 1) and B (score 300, rank 2),
  when Maya runs a sensitivity check on A at Confidence 50, then Roadsmith
  shows hypothetical score 250 and hypothetical rank 2.
- [ ] **US-004-AC10** — Given a sensitivity check on A at Confidence 50, when
  the result appears, then A's current rank 1 and score 400 are shown beside
  it.
- [ ] **US-004-AC11** — Given a sensitivity check on A at Confidence 50, when
  the result appears, then the ranked view still shows A at rank 1 with
  Confidence 80%.
- [ ] **US-004-AC12** — Given Maya ran a sensitivity check on A at Confidence
  50, when Maya closes the check, then A's stored Confidence is 80%.
- [ ] **US-004-AC13** — Given A's hypothetical score equals B's score, when
  the sensitivity result appears, then A's hypothetical rank follows the
  tie-break rules in FEAT-002 RANK-02.
- [ ] **US-004-AC14** — Given A's stored Confidence is 80%, when Maya chooses
  Confidence 80 or higher for a sensitivity check, then Roadsmith rejects
  the value and states the allowed range 0–79.
- [ ] **US-004-AC15** — Given item F is missing Confidence, when Maya selects
  F, then no sensitivity check is offered and Roadsmith names Confidence as
  missing.
- [ ] **US-004-AC16** — Given A's Confidence is flagged unsupported, when Maya
  runs a sensitivity check on A, then the check flags Confidence as
  unsupported.
- [ ] **US-004-AC17** — Given A's Confidence is flagged unsupported, when Maya
  reaches the flag by keyboard with a screen reader, then the flag is
  announced as text naming Confidence as unsupported.

## Edge Cases

- **Missing input**: An empty input is reported as missing (US-003), not as
  unsupported.
- **Score 0 input with no note**: Reach 0 with no evidence note is flagged
  unsupported; 0 is a value.
- **Evidence note removed**: Deleting the note restores the unsupported flag
  without changing score or rank.
- **AI-proposed input accepted**: The accepted input carries no evidence note
  and is flagged unsupported until Maya writes one (FEAT-002 EVID-06).
- **Stored Confidence lowered while a check is open**: When the chosen value
  is no longer below the stored value, Roadsmith discards it and asks for a
  new value.
- **Chosen Confidence 0**: Allowed; the hypothetical score is 0.
- **Another item edited while a check is open**: The hypothetical rank
  recomputes against the current stored inputs of the other items.

## Test Scenarios

Fixture for every row unless stated: A "Usage-based billing" with Reach 1000,
Impact 2, Confidence 80%, Effort 4 (score 400, rank 1), evidence notes on
Reach and Effort only. B "SAML single sign-on" with Reach 300, Impact 3,
Confidence 100%, Effort 3 (score 300, rank 2), evidence notes on all inputs.

| Scenario | AC ID | Input / State | Action | Expected Result |
|----------|-------|---------------|--------|-----------------|
| Unsupported flagged | US-004-AC1 | Fixture | Open ranked view | A row flags Impact and Confidence; B row has no flag |
| Supported input not flagged | US-004-AC2 | Fixture | Open ranked view | A row does not flag Reach or Effort |
| Whitespace note | US-004-AC3 | A Confidence note "   " | Open ranked view | A row flags Confidence |
| Flag in detail view | US-004-AC4 | Fixture | Open A detail view | Impact and Confidence flagged |
| Evidence clears flag | US-004-AC5 | Fixture | Save Confidence note "8 of 10 churned accounts cited billing in exit interviews" | Confidence flag removed; Impact flag remains |
| Rank unchanged by evidence | US-004-AC6 | Fixture | Save the same note | A score 400, rank 1 |
| Note shown with value | US-004-AC7 | A Confidence note saved | Open A detail view | Note shown next to "80%" |
| Note too long | US-004-AC8 | Fixture | Save a 2,001-character Confidence note | Rejected; 2,000-character limit stated; A Confidence still flagged |
| Note at limit | US-004-AC8 | Fixture | Save a 2,000-character Confidence note | Saved; Confidence flag removed |
| Rank falls | US-004-AC9 | Fixture | Sensitivity on A at 50 | Hypothetical score 250, rank 2 |
| Rank holds | US-004-AC9 | Fixture | Sensitivity on A at 79 | Hypothetical score 395, rank 1 |
| Zero Confidence | US-004-AC9 | Fixture | Sensitivity on A at 0 | Hypothetical score 0, rank 2 |
| Current shown beside hypothetical | US-004-AC10 | Fixture | Sensitivity on A at 50 | Current rank 1, score 400 shown beside rank 2, score 250 |
| Ranked view untouched | US-004-AC11 | Fixture | Sensitivity on A at 50 | Ranked view: A rank 1, Confidence 80%; B rank 2 |
| Nothing saved | US-004-AC12 | Fixture | Sensitivity on A at 50, then close | A stored Confidence 80%, score 400, rank 1 |
| Tie in hypothetical | US-004-AC13 | Fixture | Sensitivity on A at 60 | Hypothetical score 300 ties B; A rank 2 because B Effort 3 is lower than A Effort 4 |
| Just above tie | US-004-AC13 | Fixture | Sensitivity on A at 61 | Hypothetical score 305, rank 1 |
| Value at stored rejected | US-004-AC14 | Fixture | Sensitivity on A at 80 | Rejected; range 0–79 stated |
| Value above stored rejected | US-004-AC14 | Fixture | Sensitivity on A at 95 | Rejected; range 0–79 stated |
| Negative value rejected | US-004-AC14 | Fixture | Sensitivity on A at -1 | Rejected; range 0–79 stated |
| Unscored item | US-004-AC15 | F: Reach 100, Impact 1, Confidence empty, Effort 2 | Select F | No sensitivity check offered; Confidence named missing |
| Flag in sensitivity check | US-004-AC16 | Fixture | Sensitivity on A at 50 | Check flags Impact and Confidence as unsupported |
| Accessible flag | US-004-AC17 | Fixture | Tab to A's Confidence flag with a screen reader | Announced as text naming Confidence as unsupported; not color alone |

## Dependencies

- **Stories**: US-003 (scored items and a ranked order must exist).
- **Feature Spec**: FEAT-002 — Prioritization.
- **Feature Requirements**: EVID-01 to EVID-05, SENS-01 to SENS-06.
- **PRD Requirements**: FR-5, FR-6.
- **External**: None. No LLM call is involved.

## Out of Scope

- Showing the unsupported flag on the sequencing board or in AI critique;
  those surfaces belong to FEAT-003 and AI Assistance (FR-14).
- Sensitivity on Reach, Impact, or Effort, or on several items at once.
- Saving a sensitivity result as a scenario.
- Attaching files or links to feedback tools as evidence.
- Requiring evidence before an input can be stored; unsupported inputs still
  score and rank.
