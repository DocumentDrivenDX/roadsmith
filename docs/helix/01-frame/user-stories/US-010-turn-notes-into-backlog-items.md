---
ddx:
  id: US-010
  authoring:
    home: repo
  depends_on:
    - FEAT-004
---

# US-010: Turn Meeting Notes into Backlog Items

**Feature**: FEAT-004 — AI Assistance
**Feature Requirements**: NTE-01, NTE-02, NTE-03, NTE-04, NTE-05, NTE-06, NTE-07, VAL-01, VAL-03, VAL-06, VAL-07, REV-09, REQ-03
**PRD Requirements**: FR-13
**Priority**: P1

## Story

**As a** Maya, sole PM at a 12-person B2B SaaS company
**I want** to paste my customer-call notes and accept or discard the backlog items Roadsmith proposes from them
**So that** requests buried in notes reach the backlog in minutes instead of
being retyped or lost before planning.

## Context

Maya's backlog lives in a spreadsheet and a Linear export, but customer calls
and leadership meetings produce requests only in free-text notes. This story
exercises FEAT-004 note extraction: candidate items stay out of the backlog
until Maya accepts each one (NTE-03, NTE-04), and candidates that reference
goals the roadmap lacks are withheld (VAL-03). Accepted candidates are created
through FEAT-001's item creation as unscored, unplaced items, ready for US-009
drafting.

## Walkthrough

1. Maya opens roadmap "Q1 2027". It has goals "Reduce churn", "Win enterprise
   deals", and "Improve onboarding", and an existing item "SAML SSO".
2. Maya chooses to extract items from notes and pastes 178 characters of notes
   from a call with Acme.
3. Roadsmith states that the request sends the pasted notes, the roadmap's
   goals, and its item titles, and Maya starts the request.
4. Roadsmith shows four candidate items marked AI-proposed:
   "Invoice CSV export" linked to "Reduce churn"; "SAML SSO" marked as a
   possible duplicate; "Rewrite onboarding emails" linked to "Improve
   onboarding"; and "EMEA data residency" marked not applicable because it
   links to an unknown goal "Expand to EMEA".
5. Maya accepts "Invoice CSV export".
6. Maya discards the "SAML SSO" candidate.
7. Maya changes "Rewrite onboarding emails" to "Rewrite admin onboarding
   emails" and accepts it.
8. Roadsmith shows the backlog with two new items, both unscored and unplaced,
   each linked to its goal. The pasted notes are not saved to the roadmap.

## Acceptance Criteria

- [ ] **US-010-AC1** — Given Maya pasted 178 characters of notes, when the AI
  returns candidate items, then each valid candidate shows a title and its
  proposed description and goal links, marked AI-proposed.
- [ ] **US-010-AC2** — Given three valid candidates are pending, when Maya
  views the backlog, then the backlog item count matches its count before the
  request.
- [ ] **US-010-AC3** — Given a pending "Invoice CSV export" candidate linked to
  "Reduce churn", when Maya accepts it, then the backlog contains an unscored,
  unplaced item "Invoice CSV export" linked to "Reduce churn".
- [ ] **US-010-AC4** — Given a pending "Rewrite onboarding emails" candidate,
  when Maya changes its title to "Rewrite admin onboarding emails" and accepts,
  then the backlog item's title is "Rewrite admin onboarding emails".
- [ ] **US-010-AC5** — Given a pending candidate, when Maya discards it, then
  no backlog item is created from it.
- [ ] **US-010-AC6** — Given the roadmap has an item "SAML SSO", when the AI
  returns a candidate titled " saml sso", then the candidate is marked as a
  possible duplicate of "SAML SSO".
- [ ] **US-010-AC7** — Given the roadmap has no goal "Expand to EMEA", when the
  AI returns a candidate linked to "Expand to EMEA", then that candidate is not
  applicable, names the unknown goal, and offers no accept action.
- [ ] **US-010-AC8** — Given the AI returns a candidate with an empty title,
  when candidates are shown, then that candidate is not applicable.
- [ ] **US-010-AC9** — Given Maya pasted 20,001 characters, when Maya tries to
  start the request, then Roadsmith says the notes exceed 20,000 characters and
  sends no request.
- [ ] **US-010-AC10** — Given the AI returns zero candidates, when the request
  completes, then Roadsmith says no backlog items were found and the backlog is
  unchanged.
- [ ] **US-010-AC11** — Given Maya has accepted or discarded every candidate,
  when Maya reopens the roadmap, then no pasted note text appears in it outside
  the accepted items.

## Edge Cases

- **Goal deleted while a candidate is pending**: A candidate linked to that goal
  becomes stale and offers no accept action (REV-09).
- **Notes contain instructions aimed at the AI**: The notes are data; any
  resulting candidate still needs validation and Maya's accept.
- **Two candidates with the same title in one response**: Both are shown.
  Once Maya accepts one, the other is marked a possible duplicate (NTE-05).
- **Maya leaves before deciding**: Pending candidates stay pending; the backlog
  is unchanged.
- **AI service fails**: Covered by US-012; no candidates appear.

## Test Scenarios

All scenarios use a stubbed AI response. Fixture roadmap "Q1 2027": goals
"Reduce churn", "Win enterprise deals", "Improve onboarding"; 12 items
including "SAML SSO". Notes (178 characters): "Call with Acme, 3 Nov. Finance
team needs invoice CSV export. Okta SSO blocks their security review. New
admins find onboarding emails confusing. Asked about EMEA data residency."

| Scenario | AC ID | Input / State | Action | Expected Result |
|----------|-------|---------------|--------|-----------------|
| Candidates shown | US-010-AC1 | Stub: "Invoice CSV export" → Reduce churn; " saml sso"; "Rewrite onboarding emails" → Improve onboarding; "EMEA data residency" → Expand to EMEA | Request extraction | 3 valid candidates marked AI-proposed; 1 not applicable |
| Backlog untouched | US-010-AC2 | 3 valid candidates pending | View backlog | 12 items |
| Accept | US-010-AC3 | Pending "Invoice CSV export" | Accept | 13 items; new item unscored, unplaced, linked to Reduce churn |
| Edit then accept | US-010-AC4 | Pending "Rewrite onboarding emails" | Retitle, accept | Item "Rewrite admin onboarding emails" linked to Improve onboarding |
| Discard | US-010-AC5 | Pending " saml sso" | Discard | Backlog count unchanged |
| Duplicate | US-010-AC6 | Existing "SAML SSO"; stub " saml sso" | View candidates | Possible-duplicate marker names "SAML SSO" |
| Unknown goal | US-010-AC7 | Stub links "EMEA data residency" to "Expand to EMEA" | View candidates | Not applicable; reason names "Expand to EMEA" |
| Empty title | US-010-AC8 | Stub candidate title "" with description "Fix export" | View candidates | Not applicable; reason: missing title |
| Notes too long | US-010-AC9 | 20,001 characters pasted | Start request | Length message; stub receives no call |
| Nothing found | US-010-AC10 | Notes "Thanks all, see you Tuesday."; stub returns 0 candidates | Request extraction | "No backlog items found"; 12 items |
| Notes not kept | US-010-AC11 | Accepted 2, discarded 1 | Reopen roadmap, search "Okta" | No match |

## Dependencies

- **Stories**: None within FEAT-004. Requires FEAT-001 item creation and goal
  linking.
- **Feature Spec**: FEAT-004 — AI Assistance.
- **Feature Requirements**: NTE-01–NTE-07, VAL-01, VAL-03, VAL-06, VAL-07,
  REV-09, REQ-03.
- **PRD Requirements**: FR-13.
- **External**: AI service through the model gateway (stubbed in tests); the
  AI request and response Contract from Design.

## Out of Scope

- Importing notes files or connecting to meeting or chat tools.
- Summarizing or clustering customer feedback (PRD non-goal).
- Drafting RICE inputs for candidates (US-009, after acceptance).
- Merging a candidate into an existing item.
