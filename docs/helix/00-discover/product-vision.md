---
ddx:
  id: product-vision
  authoring:
    home: repo
---

# Product Vision

## Mission Statement

Roadsmith helps product managers on small and mid-size teams turn a messy
backlog into a roadmap they can defend. It prioritizes the work, sequences it
against real dependencies and capacity, and uses AI to draft and stress-test
the plan.

## Positioning

For product managers on teams of roughly 5–50 people who must commit to a
roadmap without a dedicated planning function,
Roadsmith is a roadmap workbench that produces a prioritized, dependency- and
capacity-aware plan in minutes.
Unlike Productboard or Aha! Roadmaps, whose AI drafts documents and summarizes
feedback, Roadsmith's AI checks whether the plan can actually be delivered and
proposes fixes the PM can accept or reject.

## Vision

A PM walks into planning with a roadmap that holds up under scrutiny. Every
item on it has a visible reason for its rank. No item starts before its
blockers finish, and no period promises more than the team can deliver.
Leadership asks "why is this before that?" and gets an answer in seconds, not
a reworked slide deck. Roadmaps stay current because revising one means
editing a model and re-running checks, not rebuilding a document. AI acts as a
skeptical planning partner that raises the problems a PM would otherwise find
mid-quarter.

**North Star**: The share of committed roadmap items that ship in their
planned period without being re-sequenced because of a dependency or capacity
problem.

## User Experience

Maya, the only PM on a 12-person team, has two days to build next quarter's
roadmap. She pastes 60 backlog items from a CSV export and writes three goals
for the quarter. Roadsmith drafts RICE scores for each item and marks the
scores whose inputs lack evidence. She adjusts four confidence values and sees
two items drop out of the top ten. She sets the team's capacity at 40
points per month. Roadsmith sequences the ranked items into Now, Next, and
Later. She asks the AI to critique the plan. It reports that the
billing-migration item sits in "Now" while its blocker, a data model
change, is in "Next". It also reports that April is planned at 130% of
capacity and that one goal has no items serving it. Each finding comes with a
proposed change shown as a diff. Maya accepts two changes, rejects one with a
note, and re-runs the checks until none remain. She leaves with a roadmap she
can explain line by line.

## Target Market

| Attribute | Description |
|-----------|-------------|
| Who | Product and project managers on software teams of about 5–50 people, often the sole or one of few PMs, planning quarterly without a PMO or portfolio tool |
| Pain | Roadmaps overcommit because capacity and hidden dependencies are not modeled; prioritization scores are easy to bend; roadmap upkeep feels like busywork |
| Current Solution | Spreadsheets, Miro or Notion boards, Jira Product Discovery's free tier, or an underused Productboard/Aha! seat; sometimes ChatPRD for drafting |
| Why They Switch | A missed quarter traced to an unmodeled dependency or overload; suite pricing and setup that cost more than the team can justify; a need to defend priorities to leadership with reasons |

## Key Value Propositions

| Value Proposition | Customer Benefit |
|-------------------|------------------|
| AI critique of the plan against dependencies, capacity, and goals | The PM finds overcommitment and ordering mistakes before the quarter starts, not halfway through it |
| Scoring that flags weak evidence and shows rank sensitivity | The PM can defend priorities to leadership and catch their own bias |
| Capacity- and dependency-aware sequencing without a suite | A credible Now/Next/Later plan without adopting Jira or an enterprise tier |
| AI drafting from a backlog and goals | A usable first roadmap in minutes instead of a day of spreadsheet work |
| AI changes proposed as reviewable diffs | The PM stays the decision-maker and every change is explainable |

## Success Definition

These strategic goals guide learning and priorities; they imply no delivery
gate. Record explicit human business decisions separately.

| Metric | Target |
|--------|--------|
| Primary KPI: on-plan delivery rate (the North Star), measured by comparing each period's committed items against items marked shipped in that period | Hypothesis: users' rate rises over their first two planning cycles; baseline to be measured |
| Time to first defensible roadmap, measured from backlog import to the first critique run with no blocking findings | Hypothesis: under 60 minutes for a 50–100 item backlog |
| AI critique acceptance rate, measured as the share of proposed diffs accepted (by finding type) | Learning goal: find which critique types PMs trust and act on |
| Planning-cycle retention, measured as the share of teams that build their next period's roadmap in Roadsmith | Learning goal: shows Roadsmith beats the spreadsheet substitute |

## Why Now

LLMs now reliably return structured output that conforms to a schema. That
lets AI propose concrete edits to a roadmap model that deterministic
validators can check. Before, AI could only write prose about a plan.
Incumbents have shipped AI over the past year, but it is aimed at feedback
synthesis and document drafting: Productboard Spark, Aha! Elle, and the
editor-level AI in Jira Product Discovery. All of it is sold as metered
credits on top of costly seats. Capacity planning is still gated to enterprise
tiers or add-ons in airfocus and Craft.io. AI-native tools like ChatPRD
produce roadmaps as documents with no dependency or capacity model. Nobody yet
offers feasibility critique grounded in a structured plan. Evidence and
sources: [competitive analysis](competitive-analysis.md).
