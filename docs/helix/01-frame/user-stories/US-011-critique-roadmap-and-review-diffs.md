---
ddx:
  id: US-011
  authoring:
    home: repo
  depends_on:
    - FEAT-004
---

# US-011: Critique the Roadmap and Review Proposed Diffs

**Feature**: FEAT-004 — AI Assistance
**Feature Requirements**: CRT-01, CRT-02, CRT-03, CRT-04, CRT-05, CRT-06, CRT-07, CRT-08, CRT-09, CRT-10, REV-01, REV-02, REV-03, REV-04, REV-05, REV-06, REV-07, REV-08, REV-09, REV-10, VAL-01, VAL-02, VAL-03, VAL-04, VAL-05, VAL-07, VAL-08
**PRD Requirements**: FR-14, FR-15, FR-16
**Priority**: P0

## Story

**As a** Maya, sole PM at a 12-person B2B SaaS company
**I want** to request a critique of my roadmap and accept or reject each proposed fix as a diff
**So that** I walk into the leadership review with zero dependency-order and
capacity violations and a recorded reason for every change I made or declined.

## Context

Last quarter a billing change slipped because its data-model blocker was
scheduled later, and Maya found out in week five. This story is the product's
differentiator: critique grounded in the roadmap model, with every fix a
validated, reviewable diff. It bundles FR-14, FR-15, and FR-16 on purpose:
a proposed diff (FR-15) exists only inside a critique finding (FR-14), and
validation (FR-16) is observable only on a proposed diff, so the three form one
vertical journey. Plan-check violations come from FEAT-003; RICE ranges come
from FEAT-002.

## Walkthrough

1. Maya opens roadmap "Q1 2027 Platform". Roadsmith reports two plan-check
   violations: "Billing migration" in Now is blocked by "Data model change" in
   Next, and Later holds 10 effort against capacity 8.
2. Maya requests a critique. Roadsmith states that the request sends the
   roadmap's items, scores, evidence notes, dependencies, periods, capacities,
   placements, and goals. Maya starts it and Roadsmith shows it in progress.
3. Roadsmith shows five findings grouped by category, each with its source,
   rationale, and cited items, periods, or goals:
   dependency order (1), capacity overflow (1), goal coverage gap (1),
   unsupported score (1), item without a stated outcome (1).
4. Maya opens the dependency-order finding. Its diff shows "Billing migration:
   Now → Next" and predicts that it clears the dependency-order violation and
   introduces none.
5. Maya accepts it. Roadsmith moves "Billing migration" to Next, re-runs the
   plan checks, and the dependency-order violation disappears.
6. Maya opens the capacity-overflow finding. Its diff shows "Slack alerts:
   Later → unplaced", predicting Later at 4 of 8. Maya accepts it, and the
   capacity violation disappears.
7. Maya opens the unsupported-score finding for "SAML SSO" Confidence 100%. Its
   diff proposes Confidence 50%. Maya rejects it with the note "Signed LOIs from
   two customers".
8. Maya opens the goal-coverage-gap finding for "Improve onboarding". It has no
   remedy, so Roadsmith offers no accept action.
9. Maya accepts the missing-outcome remedy, which edits the "Slack alerts"
   description.
10. Roadsmith shows zero plan-check violations. Each finding shows its decision,
    and the rejected one shows Maya's note.

## Acceptance Criteria

- [ ] **US-011-AC1** — Given "Q1 2027 Platform" with the fixture data, when
  Maya requests a critique and the AI returns the base stub, then Roadsmith
  shows findings grouped by the five categories with a count per category.
- [ ] **US-011-AC2** — Given a completed critique, when Maya views any finding,
  then it names its category, its source, a rationale, and at least one cited
  item, period, or goal.
- [ ] **US-011-AC3** — Given the dependency-order finding citing "Billing
  migration" and "Data model change", when Maya opens it, then its diff shows
  "Billing migration" placed in Now before and Next after.
- [ ] **US-011-AC4** — Given the "Billing migration: Now → Next" remedy, when
  Maya opens it, then the diff predicts the dependency-order violation clears
  and no violation is introduced.
- [ ] **US-011-AC5** — Given the "Billing migration: Now → Next" remedy is
  pending, when Maya accepts it, then "Billing migration" is in Next and the
  plan checks report no dependency-order violation.
- [ ] **US-011-AC6** — Given the "SAML SSO" Confidence remedy is pending, when
  Maya rejects it with a note, then "SAML SSO" Confidence stays 100%.
- [ ] **US-011-AC7** — Given Maya rejected a remedy with the note "Signed LOIs
  from two customers", when Maya views that finding, then it shows Rejected and
  the note.
- [ ] **US-011-AC8** — Given the AI returns two dependency-order findings citing
  the same items and periods, when the critique completes, then Roadsmith shows
  one dependency-order finding.
- [ ] **US-011-AC9** — Given Later exceeds capacity and the AI returns no
  capacity-overflow finding, when the critique completes, then Roadsmith shows
  a capacity-overflow finding for Later sourced from the plan checks.
- [ ] **US-011-AC10** — Given Now holds 10 of 10 capacity, when the AI returns a
  capacity-overflow finding for Now, then Roadsmith withholds that finding.
- [ ] **US-011-AC11** — Given "Billing migration" is blocked by "Data model
  change", when the AI proposes a remedy that adds "Data model change blocked
  by Billing migration", then the remedy is not applicable with a reason naming
  the cycle.
- [ ] **US-011-AC12** — Given a remedy is not applicable, when Maya views its
  finding, then the finding is shown and offers no accept action.
- [ ] **US-011-AC13** — Given the roadmap has no item "Partner API", when the AI
  returns a finding citing "Partner API", then that finding is not applicable
  with a reason naming the unknown item.
- [ ] **US-011-AC14** — Given the AI proposes "SAML SSO" Confidence 150%, when
  the critique completes, then that remedy is not applicable with an
  out-of-range reason.
- [ ] **US-011-AC15** — Given the AI proposes raising Later's capacity to 12,
  when the critique completes, then that remedy is not applicable as a
  disallowed change.
- [ ] **US-011-AC16** — Given a remedy moves "Slack alerts" to Later and links
  it to an unknown goal, when the critique completes, then the whole remedy is
  not applicable.
- [ ] **US-011-AC17** — Given the goal-coverage-gap finding for "Improve
  onboarding" has no remedy, when Maya opens it, then Roadsmith offers no
  accept action.
- [ ] **US-011-AC18** — Given a roadmap with no plan-check violations, when the
  AI returns zero findings, then Roadsmith shows a completed critique with no
  findings and lists the five categories checked.
- [ ] **US-011-AC19** — Given a critique is in flight, when Maya changes "SAML
  SSO" Confidence from 100% to 90%, then the returned "SAML SSO" Confidence
  remedy is stale.
- [ ] **US-011-AC20** — Given the "Billing migration: Now → Next" remedy is
  pending, when Maya moves "Billing migration" to Next by hand, then the remedy
  is stale and offers no accept action.
- [ ] **US-011-AC21** — Given a teammate moved "Slack alerts" to Next after
  Maya's view last refreshed, when Maya accepts the "Slack alerts: Later →
  unplaced" remedy, then Roadsmith applies nothing and marks the remedy stale.
- [ ] **US-011-AC22** — Given a remedy moves "Billing migration" to Later, when
  Maya opens it, then the diff predicts a new capacity violation for Later at
  14 of 8.
- [ ] **US-011-AC23** — Given five remedies are pending, when Maya leaves the
  critique without deciding, then the roadmap is unchanged.
- [ ] **US-011-AC24** — Given a previous critique left two remedies pending,
  when a new critique completes, then only the new critique's findings are
  shown as unresolved.

## Edge Cases

- **Two remedies move the same item**: After Maya accepts one, the other is
  stale.
- **Remedy with several changes**: Accept applies all changes or none (REV-05).
- **Finding in an unknown category**: Withheld.
- **Finding cites an item deleted after the request**: Stale, with no accept
  action.
- **Partial response**: Valid complete findings are shown and the critique is
  marked incomplete; see US-012.
- **Roadmap with no items**: No request is sent; Roadsmith says there is
  nothing to critique.
- **Critique over the token budget**: Nothing is sent; Roadsmith names the data
  that exceeds the limit (REQ-04).

## Test Scenarios

All scenarios use a stubbed AI response. Fixture roadmap "Q1 2027 Platform":
periods Now (capacity 10), Next (12), Later (8). Items: "Usage-based billing"
E=6 in Now; "Billing migration" E=4 in Now, blocked by "Data model change";
"Data model change" E=3 in Next; "SAML SSO" E=5, Confidence 100% without
evidence, in Next; "Audit log" E=4 in Later; "Slack alerts" E=6 in Later,
description "Send alerts to Slack." Goals: "Reduce churn" (Usage-based billing,
Billing migration), "Win enterprise deals" (SAML SSO, Audit log), "Improve
onboarding" (none). Base stub: dependency order citing Billing migration and
Data model change with remedy "move Billing migration to Next"; capacity
overflow citing Later with remedy "unplace Slack alerts"; goal coverage gap
citing Improve onboarding, no remedy; unsupported score citing SAML SSO
Confidence with remedy "Confidence 50%"; missing outcome citing Slack alerts
with remedy "description: Send alerts to Slack so admins see failed syncs
within 5 minutes."

| Scenario | AC ID | Input / State | Action | Expected Result |
|----------|-------|---------------|--------|-----------------|
| Grouped findings | US-011-AC1 | Fixture; base stub | Request critique | 5 findings; 1 per category; counts shown |
| Finding detail | US-011-AC2 | Completed base critique | Open each finding | Category, source AI, rationale, cited names |
| Diff shown | US-011-AC3 | Base critique | Open dependency-order finding | Billing migration: Now → Next |
| Predicted effect | US-011-AC4 | Base critique | Open same remedy | Clears dependency-order violation; introduces none; Next 12/12 |
| Accept | US-011-AC5 | Pending remedy | Accept | Billing migration in Next; 0 dependency-order violations |
| Reject keeps value | US-011-AC6 | Pending SAML SSO remedy | Reject with note | Confidence 100% |
| Reject note shown | US-011-AC7 | Rejected with "Signed LOIs from two customers" | View finding | Rejected; note shown |
| Duplicate merge | US-011-AC8 | Base stub plus a second identical dependency-order finding | Request critique | 1 dependency-order finding |
| Missed violation | US-011-AC9 | Base stub minus capacity finding | Request critique | Capacity-overflow finding for Later 10/8, source plan check, no remedy |
| False violation | US-011-AC10 | Base stub plus capacity overflow citing Now | Request critique | No Now capacity finding shown |
| Cycle withheld | US-011-AC11 | Dependency remedy replaced by "add Data model change blocked by Billing migration" | Request critique | Remedy not applicable; reason names Billing migration → Data model change → Billing migration |
| Finding kept | US-011-AC12 | Same as AC11 | View dependency-order finding | Finding shown; no accept action |
| Unknown item | US-011-AC13 | Base stub plus missing-outcome finding citing "Partner API" | Request critique | Not applicable; reason names Partner API |
| Out of range | US-011-AC14 | SAML SSO remedy Confidence 150% | Request critique | Not applicable; out-of-range reason |
| Disallowed change | US-011-AC15 | Capacity remedy "Later capacity 12" | Request critique | Not applicable; disallowed-change reason |
| All or nothing | US-011-AC16 | Remedy: Slack alerts to Later plus link to "Expand to EMEA" | Request critique | Whole remedy not applicable |
| No remedy | US-011-AC17 | Base critique | Open goal-coverage-gap finding | Finding cites Improve onboarding; no accept action |
| Zero findings | US-011-AC18 | Billing migration in Next, Slack alerts unplaced; stub returns 0 findings | Request critique | "No findings"; 5 categories listed |
| Edit in flight | US-011-AC19 | Stub delays 5 s | Change SAML SSO Confidence 100% → 90% during delay | SAML SSO remedy stale; other 4 remedies pending |
| Stale before accept | US-011-AC20 | Pending Billing migration remedy | Move Billing migration to Next by hand | Remedy stale; no accept action |
| Accept-time re-validation | US-011-AC21 | Second session moves Slack alerts to Next | Accept "unplace Slack alerts" in first session | Nothing applied; remedy stale; Slack alerts stays in Next |
| Introduced violation | US-011-AC22 | Remedy "Billing migration: Now → Later" | Open remedy | Predicts capacity violation Later 14/8 |
| No silent change | US-011-AC23 | 4 pending remedies | Navigate away, reopen roadmap | Placements, scores, descriptions match pre-critique values |
| Replaced findings | US-011-AC24 | First critique left 2 remedies pending | Run second critique with base stub | Only second critique's findings unresolved |

## Dependencies

- **Stories**: Requires FEAT-002 scoring and FEAT-003 dependency, placement,
  and plan-check stories.
- **Feature Spec**: FEAT-004 — AI Assistance.
- **Feature Requirements**: CRT-01–CRT-10, REV-01–REV-10, VAL-01–VAL-05,
  VAL-07, VAL-08.
- **PRD Requirements**: FR-14, FR-15, FR-16.
- **External**: AI service through the model gateway (stubbed in tests); the
  AI request and response Contract from Design; the critique evaluation set
  built in Test.

## Out of Scope

- Applying remedies in bulk or automatically.
- Critique across several roadmaps (Priya's cross-team review).
- Asking follow-up questions about a finding.
- Undoing an accepted remedy other than by editing the roadmap by hand.
