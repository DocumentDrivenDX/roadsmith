---
ddx:
  id: US-012
  authoring:
    home: repo
  depends_on:
    - FEAT-004
---

# US-012: Keep Planning When the AI Service Fails

**Feature**: FEAT-004 — AI Assistance
**Feature Requirements**: DEG-01, DEG-02, DEG-03, DEG-04, DEG-05, REQ-05, REQ-06
**PRD Requirements**: FR-17
**Priority**: P0

## Story

**As a** Maya, sole PM at a 12-person B2B SaaS company
**I want** to keep importing, scoring, sequencing, and checking my roadmap when an AI request fails
**So that** I still finish a feasible roadmap in one sitting before the
leadership review, with no AI failure changing or blocking my plan.

## Context

Maya has one or two days to build the quarter's roadmap, and the AI service is
an external dependency that can be slow, down, or not configured. PRD FR-17 and
principle 8 require the deterministic core to work without the LLM. This story
exercises FEAT-004 degraded mode: failures are contained to the AI request
(DEG-01, DEG-02), every non-AI function keeps working (DEG-03), and proposals
from earlier successful requests stay usable (DEG-04). Tests stub the AI
service to fail; no live outage is needed.

## Walkthrough

1. Maya opens roadmap "Q1 2027 Platform". Two score drafts from a request
   earlier that day are pending. Later holds 10 effort against capacity 8.
2. Maya requests a critique. The AI service returns an error.
3. Roadsmith shows a failure message naming the critique request, with a retry
   action. No findings appear.
4. Maya checks the roadmap. Items, scores, and placements match their values
   before the request.
5. Maya accepts the pending "Bulk CSV export" score draft. Roadsmith stores the
   inputs and ranks the item.
6. Maya adds a dependency, "Billing migration" blocked by "Data model change",
   and Roadsmith reports the resulting dependency-order violation.
7. Maya runs auto-sequencing for unplaced items. Roadsmith places "Bulk CSV
   export" in Next without any AI call.
8. The AI service recovers. Maya retries the critique, and Roadsmith shows its
   findings.

## Acceptance Criteria

- [ ] **US-012-AC1** — Given the AI service returns an error, when Maya
  requests a critique, then Roadsmith shows a failure message naming the
  critique request with a retry action.
- [ ] **US-012-AC2** — Given the AI service returns an error, when the critique
  request fails, then every item, score, dependency, placement, and goal matches
  its value before the request.
- [ ] **US-012-AC3** — Given the AI service returns an error, when the critique
  request fails, then no findings or proposals appear.
- [ ] **US-012-AC4** — Given the AI service does not respond, when 60 seconds
  pass after Maya starts a draft request, then Roadsmith shows a failure message
  for the draft request.
- [ ] **US-012-AC5** — Given the AI service returns an unreadable response,
  when Maya requests item extraction from notes, then Roadsmith shows a failure
  message and the backlog is unchanged.
- [ ] **US-012-AC6** — Given the AI service is failing, when Maya accepts a
  score draft pending from an earlier successful request, then Roadsmith stores
  its inputs and updates the ranking.
- [ ] **US-012-AC7** — Given the AI service is failing, when Maya changes an
  item's Reach, then the ranking updates.
- [ ] **US-012-AC8** — Given the AI service is failing, when Maya adds a "blocked
  by" dependency that inverts placement order, then Roadsmith reports the
  dependency-order violation.
- [ ] **US-012-AC9** — Given the AI service is failing, when Maya runs
  auto-sequencing, then Roadsmith places unplaced items into periods.
- [ ] **US-012-AC10** — Given the AI service is failing, when Maya imports a
  CSV file, then Roadsmith creates the mapped items.
- [ ] **US-012-AC11** — Given no AI service is configured, when Maya opens a
  roadmap, then each AI request control says AI assistance is unavailable.
- [ ] **US-012-AC12** — Given a critique request is in flight, when Maya edits
  an item's title, then the edit is saved.
- [ ] **US-012-AC13** — Given a draft request is in flight, when Maya cancels
  it, then no drafts appear.
- [ ] **US-012-AC14** — Given the AI response is cut off after two complete
  findings, when the critique completes, then Roadsmith shows those two
  findings and marks the critique incomplete with a re-run action.
- [ ] **US-012-AC15** — Given a critique request failed, when Maya retries after
  the AI service recovers, then Roadsmith shows the critique's findings.
- [ ] **US-012-AC16** — Given the AI service returns an error, when the failure
  message appears, then it is announced to screen readers.

## Edge Cases

- **Failure after some proposals arrive**: A request that fails outright adds
  no proposals; a cut-off response follows DEG-05 (US-012-AC14).
- **Repeated failures**: Each retry is a new explicit request; Roadsmith does
  not retry in the background.
- **Failure while proposals from another request are pending**: Those proposals
  stay reviewable (DEG-04).
- **Maya navigates away during a request**: The request's outcome still follows
  DEG-01 and DEG-02; the roadmap is unchanged unless Maya later accepts a
  proposal.

## Test Scenarios

All scenarios stub the AI service. Fixture roadmap "Q1 2027 Platform" as in
US-011, with two differences: "Billing migration" has no "blocked by"
dependency, and "Usage-based billing" has R=500, I=2, C=80%, E=6 (score 133.3).
Two unscored, unplaced items have score drafts pending from an earlier stubbed
success: "Bulk CSV export" (R=500, I=1, C=80%, E=2) and "Dark mode" (R=800,
I=0.5, C=50%, E=2).

| Scenario | AC ID | Input / State | Action | Expected Result |
|----------|-------|---------------|--------|-----------------|
| Error message | US-012-AC1 | Stub returns a server error | Request critique | Failure message names critique; retry action |
| Roadmap unchanged | US-012-AC2 | Stub returns a server error | Request critique; compare roadmap snapshot | Snapshot identical |
| No proposals | US-012-AC3 | Stub returns a server error | Request critique | 0 findings; 0 proposals |
| Timeout | US-012-AC4 | Stub never responds | Request drafts; wait 60 s | Failure message for draft request at 60 s |
| Unreadable | US-012-AC5 | Stub returns non-parseable text | Request extraction | Failure message; backlog count unchanged |
| Earlier draft | US-012-AC6 | Stub failing; Bulk CSV export draft pending | Accept draft | Score 200; ranked above Usage-based billing |
| Rank update | US-012-AC7 | Stub failing; Usage-based billing R=500 | Set R=1000 | Score 266.7; ranking updates |
| Plan check | US-012-AC8 | Stub failing; Billing migration in Now, Data model change in Next, no dependency | Add Billing migration blocked by Data model change | Dependency-order violation naming both items, Now, Next |
| Auto-sequence | US-012-AC9 | Stub failing; Bulk CSV export accepted (E=2), unplaced; Now 10/10, Next 8/12 | Run auto-sequencing | Bulk CSV export placed in Next; stub receives no call |
| Import | US-012-AC10 | Stub failing; 10-row CSV | Import with title mapped | 10 items created |
| Not configured | US-012-AC11 | No AI service configured | Open roadmap | Draft, extract, and critique controls say unavailable |
| Edit in flight | US-012-AC12 | Stub delays 10 s | Rename Audit log to "Audit trail" during critique delay | Title "Audit trail" saved |
| Cancel | US-012-AC13 | Stub delays 10 s | Start draft request; cancel at 2 s | No drafts; roadmap unchanged |
| Cut-off response | US-012-AC14 | Stub returns 2 complete findings then truncates | Request critique | 2 findings; "incomplete" marker; re-run action |
| Recovery | US-012-AC15 | First call errors; second returns base stub from US-011 | Request critique; retry | 5 findings shown |
| Announced | US-012-AC16 | Stub returns a server error | Request critique with screen reader running | Failure message read aloud |

## Dependencies

- **Stories**: US-009 (pending score drafts), US-011 (critique). Requires
  FEAT-001 import, FEAT-002 ranking, and FEAT-003 sequencing and plan-check
  stories.
- **Feature Spec**: FEAT-004 — AI Assistance.
- **Feature Requirements**: DEG-01–DEG-05, REQ-05, REQ-06.
- **PRD Requirements**: FR-17.
- **External**: Stubbed AI service with error, timeout, unreadable, and
  truncated modes; the AI request and response Contract from Design.

## Out of Scope

- Falling back to a second AI provider or model alias.
- Caching earlier AI results to answer new requests.
- Service availability targets for the AI provider.
