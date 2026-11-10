---
ddx:
  id: US-009
  authoring:
    home: repo
  depends_on:
    - FEAT-004
---

# US-009: Get AI-Drafted RICE Inputs for Unscored Items

**Feature**: FEAT-004 — AI Assistance
**Feature Requirements**: DRF-01, DRF-02, DRF-03, DRF-04, DRF-05, DRF-06, DRF-07, VAL-01, VAL-04, VAL-05, VAL-07, REV-09, REQ-03, REQ-05
**PRD Requirements**: FR-12
**Priority**: P0

## Story

**As a** Maya, sole PM at a 12-person B2B SaaS company
**I want** to request AI-drafted RICE inputs for my unscored backlog items and accept, edit, or reject each draft
**So that** every item has a score I have reviewed before the leadership review,
without typing 240 inputs by hand for a 60-item backlog.

## Context

Maya imports 60–120 items, most with no RICE (Reach, Impact, Confidence,
Effort) inputs. Scoring each by hand takes most of a planning day, but a draft
the ranking silently trusts would bend priorities toward the model's guess.
This story exercises FEAT-004 score drafting: drafts stay outside the ranking
until accepted (DRF-04), never overwrite inputs Maya entered (DRF-02), and stay
flagged unsupported until Maya adds evidence (DRF-07). RICE ranges and the
score formula belong to FEAT-002.

## Walkthrough

1. Maya opens roadmap "Q1 2027". It has goals "Reduce churn" and "Win
   enterprise deals", two scored items, and five unscored items.
2. Roadsmith shows the ranking: "Usage dashboard" (300) first, "Billing
   retries" (100) second. The five unscored items are listed as unscored.
3. Maya chooses to draft scores for all unscored items.
4. Roadsmith states that the request sends the five items' text and the
   roadmap's goals, and Maya starts the request.
5. Roadsmith shows the request in progress with a cancel action.
6. Roadsmith shows four score drafts, each input marked AI-drafted with a
   rationale. The "Dark mode" draft is shown as not applicable: Impact 7 is
   outside the valid range.
7. Maya checks the ranking. It still lists only "Usage dashboard" and "Billing
   retries".
8. Maya edits the "SAML SSO" draft's Confidence from 50% to 80% and accepts it.
   Roadsmith ranks "SAML SSO" second with score 192.
9. Maya accepts all remaining valid drafts in one action.
10. Roadsmith ranks the six scored items and flags every accepted drafted input
    as unsupported, with its AI-drafted marker. "Dark mode" stays unscored.

## Acceptance Criteria

- [ ] **US-009-AC1** — Given "Q1 2027" has five unscored items, when Maya
  requests drafts for all unscored items and the AI returns a draft for each,
  then Roadsmith shows every valid draft's inputs with an AI-drafted marker and
  a rationale per input.
- [ ] **US-009-AC2** — Given four score drafts are pending, when Maya views the
  ranking, then the ranking and every score match their values before the
  request.
- [ ] **US-009-AC3** — Given the AI returns a draft with Impact 7, when the
  drafts are shown, then that draft is marked not applicable with the
  out-of-range reason and offers no accept action.
- [ ] **US-009-AC4** — Given Maya entered Effort 3 for "Audit log", when the AI
  returns a "Audit log" draft that includes Effort 8, then the whole draft is
  not applicable and Effort stays 3.
- [ ] **US-009-AC5** — Given a pending "Bulk CSV export" draft, when Maya
  accepts it, then its inputs are stored and the ranking places it by its new
  score.
- [ ] **US-009-AC6** — Given a pending "SAML SSO" draft with Confidence 50%,
  when Maya changes Confidence to 80% and accepts, then the stored Confidence
  is 80%.
- [ ] **US-009-AC7** — Given a pending "Slack alerts" draft, when Maya rejects
  it, then "Slack alerts" stays unscored and the ranking is unchanged.
- [ ] **US-009-AC8** — Given three valid drafts and one not-applicable draft
  are pending, when Maya accepts all, then the three valid drafts are stored
  and the not-applicable draft remains unaccepted.
- [ ] **US-009-AC9** — Given Maya accepted a drafted Reach without adding
  evidence, when Maya views that item's score, then the Reach input shows the
  AI-drafted marker and the unsupported flag.
- [ ] **US-009-AC10** — Given an accepted AI-drafted Reach of 400, when Maya
  changes it to 350, then the AI-drafted marker is removed from that input.
- [ ] **US-009-AC11** — Given a pending "SAML SSO" draft, when Maya enters
  Reach 250 for "SAML SSO" by hand, then the draft is marked stale and offers
  no accept action.
- [ ] **US-009-AC12** — Given every item in the roadmap is fully scored, when
  Maya opens the draft-scores control, then Roadsmith says no items are
  unscored and sends no request.
- [ ] **US-009-AC13** — Given Maya is about to start a draft request, when the
  request control is shown, then it states which kinds of roadmap data the
  request sends.
- [ ] **US-009-AC14** — Given a draft request is in flight, when Maya cancels
  it, then no drafts appear and the roadmap is unchanged.

## Edge Cases

- **Draft for an item not requested**: The AI returns a draft for a fully
  scored item; it fails validation as overwriting PM-entered inputs and is not
  applicable.
- **Out-of-range input**: Not applicable with its reason; the item stays
  unscored (VAL-04).
- **Item deleted while a draft is pending**: The draft becomes stale.
- **Accepted draft lacks evidence**: The input stays unsupported until Maya
  adds an evidence note; the AI rationale is never copied into the evidence
  note (DRF-07).
- **AI service fails mid-request**: Covered by US-012; no drafts appear.

## Test Scenarios

All scenarios use a stubbed AI response. Fixture roadmap "Q1 2027": goals
"Reduce churn", "Win enterprise deals"; scored items "Usage dashboard" (R=600,
I=2, C=100%, E=4, score 300) and "Billing retries" (R=200, I=2, C=50%, E=2,
score 100); unscored items "SAML SSO", "Audit log" (Effort 3 entered by Maya),
"Bulk CSV export", "Slack alerts", "Dark mode". Score = R × I × C ÷ E (FEAT-002).

| Scenario | AC ID | Input / State | Action | Expected Result |
|----------|-------|---------------|--------|-----------------|
| Drafts shown | US-009-AC1 | Stub: SAML SSO R=400 I=3 C=50% E=5; Audit log R=300 I=1 C=80%; Bulk CSV export R=500 I=1 C=80% E=2; Slack alerts R=250 I=0.5 C=50% E=1; Dark mode R=800 I=7 C=100% E=2 | Request drafts for all unscored | 4 valid drafts shown, each input marked AI-drafted with a rationale; Dark mode shown as not applicable |
| Ranking untouched | US-009-AC2 | 4 pending drafts | View ranking | Usage dashboard 300 rank 1; Billing retries 100 rank 2; 5 items unscored |
| Out of range | US-009-AC3 | Stub Dark mode I=7 | View drafts | Dark mode draft not applicable, reason names Impact 7 out of range; no accept action |
| No overwrite | US-009-AC4 | Stub Audit log R=300 I=1 C=80% E=8 | View drafts | Audit log draft not applicable; stored Effort 3 |
| Accept one | US-009-AC5 | Pending Bulk CSV export draft | Accept | Score 200; rank 2 below Usage dashboard |
| Edit then accept | US-009-AC6 | Pending SAML SSO draft C=50% | Set C=80%, accept | Stored C=80%; score 192 |
| Reject | US-009-AC7 | Pending Slack alerts draft | Reject | Slack alerts unscored; ranking unchanged |
| Accept all | US-009-AC8 | SAML SSO accepted at 192; pending: Audit log, Bulk CSV export, Slack alerts valid; Dark mode not applicable | Accept all | Order: Usage dashboard 300, Bulk CSV export 200, SAML SSO 192, Billing retries 100, Audit log 80, Slack alerts 62.5; Dark mode unscored |
| Unsupported flag | US-009-AC9 | SAML SSO accepted, no evidence notes | View SAML SSO score | Reach 400 shows AI-drafted marker and unsupported flag |
| Marker cleared | US-009-AC10 | SAML SSO Reach 400, AI-drafted | Change Reach to 350 | Marker removed from Reach; Impact, Confidence, Effort keep it |
| Stale draft | US-009-AC11 | Pending SAML SSO draft | Enter Reach 250 by hand | Draft stale; no accept action; Reach 250 stored |
| Nothing to draft | US-009-AC12 | All 7 items fully scored | Open draft-scores control | Message: no unscored items; stub receives no call |
| Data disclosure | US-009-AC13 | 5 unscored items | Open draft-scores control | Control states it sends item text and roadmap goals |
| Cancel | US-009-AC14 | Stub delays 10 s | Start request, cancel after 2 s | No drafts shown; ranking unchanged |

## Dependencies

- **Stories**: None within FEAT-004. Requires item management (FEAT-001) and
  scoring and ranking (FEAT-002) stories.
- **Feature Spec**: FEAT-004 — AI Assistance.
- **Feature Requirements**: DRF-01–DRF-07, VAL-01, VAL-04, VAL-05, VAL-07,
  REV-09, REQ-03, REQ-05.
- **PRD Requirements**: FR-12.
- **External**: AI service through the model gateway (stubbed in tests); the
  AI request and response Contract from Design.

## Out of Scope

- Drafting evidence notes for RICE inputs.
- Re-drafting inputs Maya already entered.
- Changing the score formula or RICE ranges (FEAT-002).
- Auto-sequencing items after drafts are accepted (FEAT-003).
