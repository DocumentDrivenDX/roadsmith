---
ddx:
  id: competitive-analysis
  authoring:
    home: repo
  depends_on:
    - product-vision
---

# Competitive Analysis

Genesis prior-art survey for Roadsmith, time-boxed to about 20 searches and
fetches, accessed 2026-11-10. Pricing comes mostly from third-party
aggregators that often conflict, so treat it as reported and unverified
against vendor pages. **[F#]** cites a source listed at the end. **[I]** marks
an inference.

## Market Landscape

| Attribute | Assessment |
|-----------|------------|
| Market Maturity | Mature for roadmap tooling. AI features are an emerging layer that incumbents are adding as metered credits [F1][F8] |
| Growth Rate | Not researched; open question for the business case |
| Key Trends | Credit-metered AI add-ons in the suites [F1][F6][F8]; AI aimed at feedback synthesis and PRD drafting rather than plan feasibility [F4][F10][F12]; MCP servers and agents arriving in airfocus and Productboard [F4][F10] |
| Entry Barriers | Medium [I]. A basic roadmap UI is easy to build. Integrations, trust, and distribution are hard |
| Buyer Power | High [I]. Spreadsheets, Miro or Notion boards, and free tiers (Jira Product Discovery up to 3 creators [F11]) are good enough for many small teams [F14] |

## Competitive Forces

| Force | Pressure | Evidence / Confidence | Implication |
|-------|----------|-----------------------|-------------|
| Direct Rivalry | High | Eight or more funded incumbents with roadmap and prioritization features [F4–F15]; high confidence | Do not compete on breadth. Compete on plan feasibility and low setup cost |
| Substitutes | High | Small teams report a spreadsheet or three-column board is enough, and see roadmap tools as busywork [F14]; medium confidence (secondhand Reddit mirror) | Time to first roadmap must beat a spreadsheet: import a backlog and get a usable plan in minutes |
| New Entrants | Medium | AI document tools (ChatPRD) and generic AI roadmap generators produce roadmaps as documents [F16][F17][F18]; medium confidence | Drafting alone is commoditized. Critique grounded in a structured model is the moat |
| Buyer Power | High | Per-seat pricing seen as excessive for small teams [F5][F7]; capacity planning gated to top tiers or add-ons [F10][F15] | Pricing and packaging must fit small teams; capacity is core, not an add-on [I] |

## Competitor Profiles

| Competitor | Type | Positioning | Target Segment | Strengths | Weaknesses | Source / Confidence |
|------------|------|-------------|----------------|-----------|------------|---------------------|
| Productboard | Direct | Feedback-driven product management suite | Mid–large product orgs | Feedback pipeline; Spark agent drafts specs and analyses | Costly per seat; heavy setup; no AI roadmap generation or prioritization described | [F1][F4][F5]; medium |
| Aha! Roadmaps | Direct | Strategy-to-roadmap suite | Mid–enterprise | Value scorecards; Elle AI scores and ranks features with reasoning | Steep learning curve; scorecards need setup before scores are trusted | [F6][F7]; medium |
| airfocus (Lucid) | Direct | Modular prioritization and roadmapping | Mid-market, enterprise | Strongest prioritization (formulas, Priority Poker); AI agents for triage and PRDs | Opaque pricing; capacity planning in the top tier (reported) | [F10]; medium |
| ProductPlan | Direct | Roadmap communication | Mid-market | Visual roadmaps; AI Spark credits | Manual item linking; limits for occasional contributors | [F8]; medium |
| Jira Product Discovery | Direct | Discovery inside Atlassian | Atlassian shops | Formula fields; free tier | AI limited to text editing; ecosystem lock-in | [F11][F12]; high |
| Linear | Indirect | Engineering planning and issue tracking | Engineering-led teams | Project dependencies, critical paths, velocity forecasting [F13] | No real prioritization model; capacity not described | [F13]; high for features |
| Craft.io | Direct | Portfolio product management | Mid-market | Dependency management | Capacity planning is a paid add-on | [F15]; low–medium |
| ChatPRD | New entrant | AI PM copilot | Individual PMs | Drafts Now/Next/Later tables and phased roadmaps fast | No dependencies, capacity, or structured roadmap model; output is documents | [F17][F18]; medium |

**Indirect Competitors**: spreadsheets and Miro or Notion boards (high threat:
free, flexible, but a picture of a plan rather than a model [F14][F16]);
generic AI roadmap generators in ClickUp, Taskade, and Miro (medium threat:
they produce visuals with no feasibility checks [F16]); Roadmunk/Tempo (low
threat: no confirmed AI, being absorbed into a suite [F9]).

## Feature Comparison

| Feature | Roadsmith (intended) | Productboard | Aha! | airfocus | Linear | ChatPRD |
|---------|----|--------|--------|--------|--------|--------|
| Prioritization scoring (RICE/custom) | Planned | Full | Full | Full | None | Partial (labels) |
| Dependencies between items | Planned | None | Partial | Partial | Full | None |
| Capacity-aware sequencing | Planned | None | Partial | Partial (top tier) | Partial (forecasting) | None |
| AI drafting of a roadmap | Planned | None | Partial | Partial | None | Full |
| AI critique of plan feasibility | Planned | None | None | None | None | None |
| AI changes as reviewable diffs | Planned | None | None | None | None | None |

**Legend**: Full | Partial | Planned | None. Cells for competitors reflect what
the cited pages describe. "None" means not found in the survey, not proven
absent.

## Differentiation Strategy

| Differentiator | Why It Matters | Defensibility |
|----------------|----------------|---------------|
| AI critique grounded in a structured roadmap model (dependency violations, capacity overflow, unsupported scores) | Addresses the top-cited failure: overcommitted roadmaps that ignore capacity and hidden dependencies [F22][F23] | M. Needs both the model and the AI layer; incumbents only summarize and draft |
| Scoring with resistance to gaming built in: evidence flags and rank sensitivity | Addresses RICE inputs being inflated or "bent" [F19][F20][F21] | M |
| Dependency and capacity sequencing at small-team weight, with no Jira required | Capacity planning is sold as an enterprise tier or add-on elsewhere [F10][F15] | L–M. Packaging advantage, easy to copy |
| Minutes from backlog import to first roadmap | Addresses tool-overhead and staleness complaints [F14] | L. Execution advantage |

**Positioning**: For product managers on small and mid-size teams who need a
roadmap they can defend, Roadsmith is a roadmap workbench that drafts,
sequences, and stress-tests the plan against real dependencies and capacity.
Unlike Productboard or Aha!, whose AI drafts documents and synthesizes
feedback, Roadsmith's AI audits whether the plan is feasible.

## Strategic Implications

- **Attack**: plan feasibility, meaning dependency- and capacity-aware
  sequencing plus AI critique delivered as reviewable diffs. Also fast setup
  from an imported backlog.
- **Defend**: trust in the AI, by keeping scoring, sequencing, and capacity
  math deterministic and making the LLM propose changes that validators check.
- **Avoid**: issue tracking and sprints (Linear, Jira own it; integrate
  instead); feedback portals and voting (Productboard, airfocus, Canny-type
  tools); OKR or strategy suites; stakeholder portals (out of v1 scope); a
  generic AI document generator with no model behind it; full resource
  management (named people, allocations, timesheets).

**Open research questions**: official vendor pricing; market growth rate;
Linear's 2026 agentic features; Craft.io and Notion AI capabilities; traction
of AI-native roadmap startups (none found). Open-source references for Design:
Eververse (MIT, Next.js, archived 2026-03-12) [F25]; Plane [F24].

### Sources (accessed 2026-11-10)

- F1 https://pricingsaas.com/news/article/productboard-turns-spark-from-a-15-beta-add-on-into-a-4-tier-credit-platform
- F4 https://www.productboard.com/resources/productboard-ai
- F5 https://www.trustradius.com/products/productboard/reviews ; https://www.featurebase.app/blog/productboard-pricing
- F6 https://www.aha.io/ai-library/skill-feature-scoring ; https://www.aha.io/ai-library/skill-feature-prioritization
- F7 https://www.featurebase.app/blog/aha-pricing ; https://thedigitalprojectmanager.com/tools/aha-review/
- F8 https://www.capterra.com/p/145894/ProductPlan/
- F9 https://userjot.com/blog/roadmunk-pricing
- F10 https://airfocus.com/pricing ; https://www.g2.com/products/airfocus-by-lucid/pricing
- F11 https://www.featurebase.app/blog/jira-product-discovery-pricing
- F12 https://www.eesel.ai/blog/jira-product-discovery-ai-features
- F13 https://linear.app/plan
- F14 https://reddit.sentinel-team.org/posts/1r7ewbs/snapshots/2026-02-20T06%3A14%3A23.978309Z (secondhand Reddit mirror)
- F15 https://frontdeskreview.com/software/roadmap-tools/craft-io/
- F16 https://miro.com/ai/product-development/ai-product-roadmap/ ; https://clickup.com/features/ai/product-roadmap-generator
- F17 https://www.banani.co/blog/chatprd-ai-review
- F18 https://www.chatprd.ai/product/features/brainstorm-roadmap
- F19 https://productsos.substack.com/p/why-rice-should-be-avoided-for-prioritization
- F20 https://www.productcoalition.com/p/100-frameworks-and-youre-still-building
- F21 https://www.capicua.com/blog/rice-scoring-product-prioritization
- F22 https://adlerhsieh.com/p/stop-negotiating-capacity
- F23 https://us.fitgap.com/stack-guides/quantify-execution-capacity-so-roadmaps-stop-slipping
- F24 https://openalternative.co/tags/product-roadmap
- F25 https://github.com/haydenbleasel/eververse
