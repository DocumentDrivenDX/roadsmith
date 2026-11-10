---
ddx:
  id: prd
  authoring:
    home: repo
  depends_on:
    - product-vision
  links:
    - id: competitive-analysis
      kind: informed_by
kind: product
---

# Product Requirements Document

## Summary

Roadsmith is a web app for product managers on software teams of about 5–50
people. It turns a backlog into a roadmap they can defend. Today these PMs
build roadmaps in spreadsheets, boards, or underused suite seats. Those tools
do not model dependencies or capacity, so plans overcommit and fail
mid-quarter. Prioritization scores are easy to bend toward opinion.

Roadsmith v1 keeps the roadmap as a structured model. The model holds items,
goals, RICE scores, "blocked by" dependencies, and planning periods with
capacity budgets. Deterministic checks continuously report plans that break
dependency order or exceed capacity. An LLM drafts scores and critiques the
plan. Every change the AI proposes appears as a reviewable diff that the PM
accepts or rejects, and the checks validate it first. The AI never edits the
plan silently, and the product stays fully usable without it.

The v1 scope covers five subsystems: Backlog Intake, Prioritization,
Sequencing and Capacity, AI Assistance, and Accounts and Workspaces.
Stakeholder presentation, issue tracking, and feedback collection are out of
scope. Success means a PM can go from a 50–100 item CSV to a sequenced
roadmap with no blocking violations in one sitting, and can explain every
placement.

## Problem and Goals

### Problem

A PM planning a quarter for a small team commits items without a model of
team capacity or of which items block which. Overcommitment becomes normal
and costs the PM credibility when the roadmap slips [competitive analysis
F22, F23]. The dependency or overload that causes the slip is usually
knowable at planning time. It goes unseen because the roadmap is a picture
in a spreadsheet or on a board, not a model anyone can check [F14, F16].

Prioritization frameworks like RICE are meant to add rigor. In practice their
Confidence and Effort inputs get inflated or "bent" toward the PM's existing
preference [F19, F20, F21]. Leadership cannot tell a well-evidenced rank from
a guessed one.

The tools that model more are suites like Productboard and Aha!. Small teams
report them as costly per seat and heavy to set up, and they gate capacity
planning to top tiers or add-ons [F5, F7, F10, F15]. Their AI drafts
documents and summarizes feedback. None of it checks whether a plan is
feasible [F4, F6, F12]. AI-native drafting tools like ChatPRD produce a
roadmap as text with no dependency or capacity model [F17, F18].

### Goals

1. A PM can see, before committing, every place the roadmap breaks
   dependency order or exceeds capacity.
2. A PM can explain why each item sits where it does: its score inputs, the
   evidence behind them, and the constraints that placed it.
3. A PM gets from a raw backlog to a first sequenced roadmap in one sitting,
   without adopting an issue tracker or suite.
4. AI suggestions stay reviewable and reversible: the PM decides every change.

### Success Metrics

| Metric | Target | Measurement Method |
|--------|--------|--------------------|
| On-plan delivery rate (vision North Star) | Hypothesis: rises over a team's first two planning cycles; baseline to be measured | Period records (FR-11): shipped committed items ÷ items committed at period start, read together with the preventable-slip share (slips with a dependency or capacity reason ÷ committed) |
| Time to first clean roadmap | Hypothesis: < 60 min for a 50–100 item backlog | Product analytics: time from first import to first plan with zero blocking violations |
| Critique diff acceptance rate by finding type | Learning goal, no target | Accept/reject events per finding category |
| Unsupported-score share at commit | Learning goal: expected to fall as PMs add evidence | Share of committed items with ≥1 unsupported RICE input at period start |

### Non-Goals

- **Issue tracking, sprints, or task boards.** Linear and Jira own delivery
  execution. Roadsmith plans at the item level and does not track subtasks,
  assignees, or status workflows beyond period close.
- **Feedback portals, voting, or insights repositories.** Roadsmith does not
  collect customer feedback. Evidence for scores is a note the PM writes.
- **OKR or strategy management.** Goals are lightweight labels that items
  link to. There are no key-result tracking, check-ins, or cascades.
- **Stakeholder portals and presentation views.** v1 has no public links,
  branded views, or slide output. Export is a P2 consideration only.
- **Named-person resource management.** Capacity is an effort budget per
  period for the team. Roadsmith does not track individual allocations,
  skills, or timesheets.
- **Day-level Gantt scheduling.** Sequencing places items into periods
  (Now/Next/Later, months, or quarters), not onto start and end dates.
- **Real-time multi-user co-editing.** Teammates can edit a shared workspace
  (P1), but simultaneous live editing is not supported.
- **Generic AI document generation.** Roadsmith does not write PRDs, specs,
  or release notes.

## Users and Scope

### Primary Persona: Maya, sole PM at a 12-person B2B SaaS company

Maya plans quarterly for one engineering team of six. Her backlog of 60–120
items lives in a spreadsheet and a Linear export. She builds the roadmap over
one or two days before a leadership review. Last quarter a billing
change slipped because a data-model item it depended on was scheduled
later, and she found out in week five. She wants to walk into the review
knowing the plan is feasible and able to answer "why this before that?"
without rebuilding slides.

### Secondary Persona: Priya, Head of Product at a 40-person company

Priya leads three PMs, each planning for their own team. She reviews each
team's roadmap before the quarter and wants the same checks applied to all
three: no overloaded periods, no inverted dependencies, and scores with
stated evidence. She shares a workspace with her PMs (P1).

### User Segments

| User | Context | Needs | Evidence | Confidence | Status |
|------|---------|-------|----------|------------|--------|
| Sole or lead PM, team of 5–50 | Desktop browser; plans quarterly; backlog in spreadsheet or tracker export | See feasibility problems before committing; defend priorities; fast setup | Competitive analysis F14, F22, F23 | medium | assumed |
| Head of Product over 2–5 PMs | Reviews roadmaps before planning; desktop | Consistent checks across teams | None beyond persona reasoning | low | assumed |

### User Needs

| Need | Importance | Evidence | Confidence | Status |
|------|------------|----------|------------|--------|
| "Tell me where my plan won't fit before I commit to it" | high | F22, F23 | medium | assumed |
| "Show me which scores I'm guessing at" | high | F19, F20, F21 | medium | assumed |
| "Get my backlog in without a day of setup" | high | F5, F14 | medium | assumed |
| "Let AI help, but don't let it change my plan behind my back" | medium | None; inferred from trust risk | low | assumed |

## Requirements

### Must Have (P0)

1. Backlog intake from CSV or pasted tabular text, plus manual item and goal
   management (FR-1, FR-2, FR-3).
2. RICE scoring with a deterministic, always-current ranked order (FR-4).
3. "Blocked by" dependencies between items, with cycles rejected (FR-8).
4. Planning periods with capacity budgets, and manual or automatic
   placement of items into periods (FR-7, FR-9).
5. Continuous deterministic plan checks for dependency-order and capacity
   violations (FR-10).
6. AI drafting of RICE inputs and AI critique of the plan. Every proposed
   change is a validated, reviewable diff, and all non-AI functions keep
   working when the AI is unavailable (FR-12, FR-14, FR-15, FR-16, FR-17).
7. Accounts with roadmaps private to the owner's workspace (FR-18).

### Should Have (P1)

1. Evidence notes on score inputs, with unsupported inputs flagged (FR-5).
2. Rank sensitivity to a lowered Confidence value (FR-6).
3. Period start and close: commitment snapshot, shipped, slipped, or dropped
   outcomes with slip reasons, on-plan delivery rate, and archiving (FR-11).
4. AI extraction of proposed backlog items from unstructured notes (FR-13).
5. Workspace invitations for teammates with edit access (FR-19).
6. Password reset by email (FR-20). It shares the outbound email dependency
   with FR-19.

### Nice to Have (P2)

Not yet decomposed into FRs. Frame them when promoted.

1. Export a roadmap to CSV and Markdown.
2. Alternative scoring models (ICE, custom weighted formula).
3. Direct import from Linear, Jira, or GitHub Issues.

## Functional Requirements

### Subsystem: Backlog Intake

- **FR-1** — A PM can import backlog items from a CSV file or pasted tabular
  text by mapping source columns to item title (required), description, and
  the optional RICE input fields (Reach, Impact, Confidence, Effort). Rows
  that cannot be mapped are reported individually and do not abort the import
  of the remaining rows.
- **FR-2** — A PM can create, edit, and delete backlog items manually. Each
  roadmap has its own backlog. Deleting an item also removes its goal links,
  its dependencies, and its period placement.
- **FR-3** — A PM can define goals for a roadmap and link each item to zero
  or more goals.

### Subsystem: Prioritization

- **FR-4** — Each item can carry RICE inputs (Reach, Impact, Confidence,
  Effort) within the allowed ranges defined in FEAT-002. The system computes
  each item's score and a ranked order deterministically, with a defined
  tie-break. Items with incomplete inputs rank after all scored items. The
  ranking updates whenever an input changes.
- **FR-5** — Each RICE input can carry an evidence note. The system flags
  inputs without evidence as unsupported and shows the flag wherever the
  score appears.
- **FR-6** — For a selected item, the PM can see the rank the item would
  hold if its Confidence were lowered to a chosen value, without changing the
  stored input.

### Subsystem: Sequencing and Capacity

- **FR-7** — A PM can define an ordered set of planning periods for a
  roadmap, either horizons (Now, Next, Later) or named calendar periods, each
  with a capacity budget in the same effort units as RICE Effort.
- **FR-8** — A PM can declare that one item is blocked by another. The
  system rejects a dependency that would create a cycle and names the items
  in the cycle.
- **FR-9** — A PM can place items into periods manually. The PM can also
  run auto-sequencing, which places unplaced items in rank order into the
  earliest period that has remaining capacity and comes no earlier than every
  blocker's period.
- **FR-10** — The system continuously reports plan violations. An item placed
  in an earlier period than one of its blockers is a dependency-order
  violation. A period whose placed effort exceeds its capacity is a capacity
  violation. Each violation names the items and periods involved. A dependent
  item placed in the same period as its blocker is reported as a warning, not
  a violation.
- **FR-11** — The PM starts a period, which records the items placed in it
  at that moment as the period's commitment. At period close, the PM marks
  each committed item as shipped, slipped, or dropped. Committed items moved
  out during the period count as slipped. Every slipped item needs a reason:
  dependency, capacity, or other. The system records two figures for the
  period. The on-plan delivery rate is shipped committed items ÷ committed
  items. The preventable-slip share is items slipped for a dependency or
  capacity reason ÷ committed items. Shipped and dropped items are archived
  out of the backlog and ranking, and stay visible in the period's history.
  Slipped items not already placed in a later period return to the backlog
  unplaced.

### Subsystem: AI Assistance

- **FR-12** — A PM can request AI-drafted RICE inputs for unscored items,
  based on item text and roadmap goals. Drafted inputs are shown as proposals
  marked AI-drafted, with a rationale, and are not used in ranking until the
  PM accepts them.
- **FR-13** — A PM can paste unstructured notes and receive proposed backlog
  items. The PM accepts or discards each one before it enters the backlog.
- **FR-14** — A PM can request an AI critique of the roadmap. The critique
  returns findings in these categories: dependency order, capacity overflow,
  goal coverage gap, unsupported score, and item without a stated outcome.
  Each finding cites the items, periods, or goals involved. The
  unsupported-score category depends on evidence notes (FR-5), so it ships
  with FR-5 at P1. The other categories are P0.
- **FR-15** — Each critique finding that has a remedy comes with a proposed
  change, shown as a before-and-after diff of the roadmap. The PM accepts or
  rejects each proposal, optionally with a note. An accepted change applies
  to the roadmap and the plan checks re-run.
- **FR-16** — An AI-proposed change that fails deterministic validation is
  never offered as an applicable diff. Failures include creating a cycle,
  referencing an unknown item, and producing an out-of-range RICE input.
- **FR-17** — When the AI service is unavailable or errors, every non-AI
  function remains usable. The AI request shows a failure message, and the
  roadmap is left unchanged.

### Subsystem: Accounts and Workspaces

- **FR-18** — A person can sign up, sign in, and sign out. Each roadmap
  belongs to a workspace, and only that workspace's members can read or
  change it.
- **FR-19** — A workspace owner can invite teammates by email to join the
  workspace with edit access, and can remove them.
- **FR-20** — A person who has forgotten their password can reset it through
  a time-limited link sent to their account email. The reset flow does not
  reveal whether an email has an account.

## Acceptance Test Sketches

| Requirement | Scenario | Input | Expected Output |
|-------------|----------|-------|-----------------|
| P0-1 (FR-1) | Import a CSV with one bad row | 61-row CSV; row 17 has an empty title; title→"Name", description→"Notes" | 60 items created; one row-level error naming row 17 and "title missing" |
| P0-1 (FR-3) | Link items to a goal | Goal "Reduce churn"; link items A and C | Goal shows 2 linked items; item B shows no goals |
| P0-2 (FR-4) | Rank updates on edit | A: R=500, I=2, C=80%, E=4 (score 200); B: R=300, I=3, C=100%, E=3 (score 300) | B ranks above A; raise A's Reach to 1000 (score 400) and A ranks first without a reload |
| P0-3 (FR-8) | Reject a cycle | A blocked by B, B blocked by C; add "C blocked by A" | Rejected with a message naming A → B → C → A; no dependency saved |
| P0-4 (FR-7, FR-9) | Auto-sequence respects capacity and blockers | Periods Now (cap 10), Next (cap 10); ranked A (E=6), B (E=6, blocked by C), C (E=3) | A and C in Now (9/10); B in Next |
| P0-5 (FR-10) | Report both violation types | Manually place B in Now while its blocker C is in Next; Now holds 12 effort against cap 10 | Dependency-order violation naming B, C, Now, Next; capacity violation naming Now, 12/10 |
| P0-6 (FR-12) | AI-drafted scores stay out of ranking until accepted | 5 unscored items; request draft | 5 proposals marked AI-drafted with rationale; ranking unchanged until accepted |
| P0-6 (FR-14, FR-15) | Critique proposes a diff | Plan with B in Now and blocker C in Next | Dependency-order finding citing B and C, with a proposed diff moving B to Next; accepting it clears the violation |
| P0-6 (FR-16) | Invalid AI proposal is withheld | Stubbed AI response proposes "A blocked by C" where it would form a cycle | No applicable diff offered for that proposal; the finding is still shown, with its remedy marked not applicable |
| P0-6 (FR-17) | AI outage | AI service returns errors | Critique request shows a failure message; items, scores, and placements are unchanged and editable |
| P0-7 (FR-18) | Workspace isolation | User X owns roadmap R; user Y is in another workspace | Y cannot list, read, or change R |

## Technical Context

These are recorded stack selections from
[concerns.md](concerns.md) (shipped defaults the operator accepted at
genesis). None is backed by an ADR yet, and exact versions get pinned in
Design.

- **Language/Runtime**: TypeScript in strict mode on Bun (`typescript-bun`)
- **Key Libraries**: React with Next.js App Router (`react-nextjs`), Tailwind
  CSS 4 tokens, Playwright for browser e2e (`e2e-playwright`)
- **Auth**: local sessions with a documented swap path to OIDC
  (`auth-local-sessions`)
- **AI**: Anthropic Claude (operator choice, 2026-11-10). LLM calls go
  through named model aliases (`model-gateway`), so the provider can change
  without product changes. The alias set and model versions are decided in
  Design. Proposals use structured
  output validated against the roadmap model.
- **Data/Storage**: PostgreSQL (operator choice, 2026-11-10; version pinned
  in Design), modeled under `relational-data-modeling`
- **Observability**: OpenTelemetry for service, frontend, and GenAI-operation
  signals (`o11y-otel`)
- **Platform Targets**: desktop browsers, current Chrome, Firefox, Safari,
  and Edge. Mobile layouts are not a v1 target.

## Constraints, Assumptions, Dependencies

### Constraints

- **Technical**: Scoring, ranking, sequencing, and plan checks must work
  without the LLM (FR-17). The AI layer only proposes changes.
- **Technical**: Meet WCAG 2.x AA, including a keyboard alternative to every
  drag interaction (`a11y-wcag-aa`).
- **Business**: No pricing or packaging decision constrains v1 (operator,
  2026-11-10). No budget or launch date is set. Pricing is revisited in a
  later business case.
- **Legal/Compliance**: Roadmap content is customer-confidential. It is sent
  to the LLM provider only on an explicit AI request.

### Assumptions

- PMs on small teams will enter effort estimates and period capacity in a
  shared unit (for example points or person-weeks). If they won't, capacity
  checks have no input.
- A plan model at item and period granularity, without dates, is enough to
  catch the dependency and capacity problems that cause slips.
- Current LLMs can draft plausible RICE inputs and useful critique findings
  from item text and goals. Testing in Design checks this with a bounded
  evaluation set.
- PMs will accept or reject diffs rather than wanting the AI to apply
  changes automatically.

### Dependencies

- Anthropic's API, reached through the model gateway.
- A PostgreSQL database.
- An outbound email service for invitations and password reset (FR-19,
  FR-20; selected in Design).
- Governing artifacts: [product-vision](../00-discover/product-vision.md),
  [competitive-analysis](../00-discover/competitive-analysis.md),
  [concerns](concerns.md), [principles](principles.md).

## Risks

| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| PMs skip effort and capacity entry, so checks find nothing | Med | High | Import maps estimate columns. Unestimated items in a period are flagged as a check finding. Measure the estimated-item share in the first usability test |
| AI critique produces noisy or wrong findings and loses trust | Med | High | Every AI proposal passes deterministic validation (FR-16). Build a bounded critique evaluation set in Test and track acceptance rate by category |
| LLM cost per critique is too high for small-team pricing | Med | Med | Send only the roadmap model, not the full history. Record token usage per request through GenAI telemetry, and set a per-request token budget in Design |
| Roadmap data sent to the LLM raises confidentiality objections | Med | Med | Send data only on an explicit AI request (constraint above). Document what is sent. Confirm in Design that Anthropic's commercial API terms exclude training on customer data, and record the evidence |
| Scope drifts toward an issue tracker or feedback suite | Med | Med | Non-goals above. `scope-discipline` concern applies to every work item |

## Open Questions

- [ ] How should two editors' conflicting saves to the same roadmap be
  resolved (FR-19 makes this possible)? Blocks the persistence design.
  Decided in Design.
- [ ] Should the stack selections in Technical Context be ratified as ADRs?
  Blocks nothing in Frame. Decided in Design.

Resolved by the operator on 2026-11-10:

- [x] Datastore: PostgreSQL.
- [x] LLM provider: Anthropic Claude, through model-gateway aliases. The
  alias set is decided in Design.
- [x] A dependent item in the same period as its blocker is a warning
  (FR-10).
- [x] A period's commitment is recorded at an explicit period start, and
  every slip records a reason: dependency, capacity, or other (FR-11).
- [x] Shipped and dropped items are archived at close, and slipped items
  return to the backlog (FR-11).
- [x] The numeric limits and targets in the feature specs are accepted as
  working values. Change them through `evolve`.
- [x] No pricing or packaging decision constrains v1.

## Success Criteria

v1 is complete when all of the following can be demonstrated against the
running app with fixtures:

- A PM imports a 100-row CSV fixture, scores items, declares dependencies,
  defines three periods with capacity, and runs auto-sequencing. The result
  has zero dependency-order violations and any capacity overflow is reported
  (FR-1, FR-4, FR-7–FR-10).
- Every P0 acceptance sketch above passes as an automated test, and the
  core flow (import, score, sequence, critique, accept a diff) runs green
  end to end in Playwright.
- AI critique, run against a seeded roadmap with known violations, reports
  each one with a validated diff. A stubbed invalid proposal is withheld
  (FR-14–FR-16).
- With the AI service stubbed to fail, the non-AI core flow still passes
  (FR-17).
- One user cannot read or change another workspace's roadmap (FR-18).
- Automated WCAG AA checks pass on the core flow screens, and every drag
  interaction has a keyboard alternative.
