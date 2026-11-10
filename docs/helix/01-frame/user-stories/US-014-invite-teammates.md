---
ddx:
  id: US-014
  authoring:
    home: repo
  depends_on:
    - FEAT-005
---

# US-014: Invite Teammates

**Feature**: FEAT-005 — Accounts and Workspaces
**Feature Requirements**: ISO-05, ISO-06, ISO-07, MEM-01, MEM-02, MEM-03, MEM-04, MEM-05, MEM-06, MEM-07, MEM-08, MEM-09, MEM-10, MEM-11, MEM-12, MEM-13, MEM-14, MEM-15
**PRD Requirements**: FR-19
**Priority**: P1

## Story

**As a** Priya, Head of Product at a 40-person company
**I want** to invite each PM I lead to my workspace by email with edit access,
and remove a PM who leaves
**So that** all three teams' roadmaps sit in one workspace I review before the
quarter, and a departed PM loses access on their next action.

## Context

Priya leads three PMs, each planning for their own team, and reviews every
team's roadmap before the quarter (PRD secondary persona). Today each PM's
roadmap would sit in that PM's personal workspace, out of Priya's reach. This
story exercises the FR-19 membership and invitation area of FEAT-005,
including an invitee with no account and removal with an open session. It
depends on US-013 for sign-up, sign-in, and workspace isolation.

## Walkthrough

Testers need three browsers (or profiles) and an inbox for each address
below. `arun@fabrikam.example` has no account.
`lena@fabrikam.example` has an account with its own `Lena's workspace`.

1. Priya signs in to `Priya's workspace`, where Priya is owner, and opens the
   members view.
2. Priya invites `arun@fabrikam.example`. Roadsmith lists a pending invitation
   for that address with an expiry date 7 days out.
3. Arun opens the invitation email and follows its link. Roadsmith shows that
   Priya invited Arun to `Priya's workspace` and offers sign-up with
   `arun@fabrikam.example` filled in.
4. Arun signs up with name `Arun` and a 14-character password. Roadsmith opens
   `Priya's workspace`.
5. Arun creates roadmap `Payments Q1` and sets item `Card vaulting` Effort to 5.
6. Priya reloads the members view. Arun is listed as editor, and the pending
   invitation is gone. The roadmap list shows `Payments Q1`.
7. Priya invites `lena@fabrikam.example`. Lena signs in, follows the link, and
   accepts.
8. Lena opens the workspace choice. Roadsmith offers `Lena's workspace` and
   `Priya's workspace`. Lena chooses `Priya's workspace` and sees
   `Payments Q1`.
9. With `Payments Q1` open in Arun's browser, Priya removes Arun from the
   workspace.
10. Arun changes `Card vaulting` Effort to 8. Roadsmith refuses the change and
    shows the not-found response for the roadmap.
11. Priya opens `Payments Q1`. It is still in `Priya's workspace`, and
    `Card vaulting` Effort is 5.

## Acceptance Criteria

- [ ] **US-014-AC1** — Given Priya owns `Priya's workspace`, when Priya invites
  `arun@fabrikam.example`, then the members view lists a pending invitation
  for that address expiring 7 days after sending.
- [ ] **US-014-AC2** — Given a pending invitation for `arun@fabrikam.example`
  and no account for that address, when Arun signs up from the invitation,
  then Arun is listed as an editor of `Priya's workspace`.
- [ ] **US-014-AC3** — Given Lena has an account and a pending invitation,
  when Lena signs in and accepts it, then `Priya's workspace` appears among
  Lena's workspace choices.
- [ ] **US-014-AC4** — Given Lena is a member of `Lena's workspace` and
  `Priya's workspace`, when Lena chooses `Priya's workspace`, then the roadmap
  list shows only that workspace's roadmaps.
- [ ] **US-014-AC5** — Given Arun is an editor, when Arun changes
  item `Checkout redesign` Effort from 5 to 6 in `Platform Q1`, a roadmap
  Priya created, then the new value is saved.
- [ ] **US-014-AC6** — Given Arun is an editor, when Arun's session sends an
  invitation request for `kim@fabrikam.example` directly to the server, then
  no invitation for that address exists.
- [ ] **US-014-AC7** — Given an invitation sent 8 days ago, when the invitee
  opens its link, then Roadsmith states the invitation is no longer valid.
- [ ] **US-014-AC8** — Given Priya revoked the invitation for
  `kim@fabrikam.example`, when Kim opens its link, then Roadsmith states the
  invitation is no longer valid.
- [ ] **US-014-AC9** — Given Priya invited `kim@fabrikam.example` twice, when
  Kim opens the link from the first email, then Roadsmith states the
  invitation is no longer valid.
- [ ] **US-014-AC10** — Given an invitation for `kim@fabrikam.example`, when
  Lena, signed in as `lena@fabrikam.example`, opens its link, then acceptance
  is refused with an offer to sign out and continue.
- [ ] **US-014-AC11** — Given Arun is a member of `Priya's workspace`, when
  Priya invites `Arun@Fabrikam.example`, then the invitation is refused with a
  message that Arun is already a member.
- [ ] **US-014-AC12** — Given the external email service rejects the
  invitation email, when Priya invites `kim@fabrikam.example`, then the members
  view marks the invitation as not sent with a resend option.
- [ ] **US-014-AC13** — Given Arun has `Payments Q1` open in a valid session,
  when Priya removes Arun, then Arun's next request for `Payments Q1` gets the
  not-found response.
- [ ] **US-014-AC14** — Given Arun created `Payments Q1`, when Priya removes
  Arun, then `Payments Q1` stays in `Priya's workspace` unchanged.
- [ ] **US-014-AC15** — Given Arun has been removed from `Priya's workspace`,
  when Arun opens Roadsmith, then `Arun's workspace` and its roadmaps remain
  available to Arun.
- [ ] **US-014-AC16** — Given Priya is the owner of `Priya's workspace`, when
  Priya tries to remove Priya's own membership, then the removal is refused
  with a message that the workspace needs its owner.

## Edge Cases

- **Invitee signs up with a different address**: If Arun signs up from the
  invitation but changes the address to `arun.k@fabrikam.example`, the account
  is created but does not join `Priya's workspace`. The invitation stays
  pending.
- **Removed member re-invited**: A new invitation to Arun works as for anyone
  else, and Arun rejoins as an editor.
- **Invitation accepted twice**: Following the same link after acceptance
  shows that the invitation is no longer valid and opens nothing.
- **Email service down**: Sign-in and roadmap editing keep working for every
  member while invitations fail with the not-sent state (FEAT-005
  Reliability NFR).

## Test Scenarios

| Scenario | AC ID | Input / State | Action | Expected Result |
|----------|-------|---------------|--------|-----------------|
| Invite created | US-014-AC1 | Priya owner; sent 2026-11-10 14:00 | Invite `arun@fabrikam.example` | Pending invitation for that address, expires 2026-11-17 14:00 |
| New-account invitee joins | US-014-AC2 | Pending invite for Arun; no account | Arun signs up from link with password `teal-harbor-77!` | Members: Priya (owner), Arun (editor); Arun also owns `Arun's workspace` |
| Existing-account invitee joins | US-014-AC3 | Lena has account and `Lena's workspace` | Lena signs in, accepts | Workspace choices: `Lena's workspace`, `Priya's workspace` |
| Workspace switch | US-014-AC4 | `Lena's workspace` holds `Lena Q1`; `Priya's workspace` holds `Payments Q1` | Lena chooses `Priya's workspace` | List shows `Payments Q1` only |
| Editor edits | US-014-AC5 | Priya created `Platform Q1`; `Checkout redesign` Effort = 5 | Arun sets Effort = 6 | Priya sees Effort = 6 after reload |
| Editor cannot invite | US-014-AC6 | Arun is editor | Arun's session sends an invitation for `kim@fabrikam.example` to the server | Refused; Priya's members view shows no invitation for Kim |
| Expired invitation | US-014-AC7 | Invite sent 2026-11-01 09:00 | Open link 2026-11-09 09:00 | "No longer valid"; Kim not a member |
| Revoked invitation | US-014-AC8 | Invite for Kim revoked | Kim opens link | "No longer valid"; Kim not a member |
| Replaced invitation | US-014-AC9 | Kim invited at 10:00 and again at 11:00 | Kim opens the 10:00 link | "No longer valid"; the 11:00 link still works |
| Wrong account | US-014-AC10 | Invite for Kim; Lena signed in | Lena opens Kim's link | Refused; sign-out offered; Lena's memberships unchanged |
| Already a member | US-014-AC11 | Arun is editor | Priya invites `Arun@Fabrikam.example` | Refused: already a member; no pending invitation |
| Email not accepted | US-014-AC12 | Email service stubbed to reject | Priya invites Kim | Invitation marked not sent; resend offered; Kim not a member |
| Removal with open session | US-014-AC13 | Arun viewing `Payments Q1` | Priya removes Arun; Arun sets Effort = 8 | Not-found response; Effort stays 5 |
| Work stays | US-014-AC14 | Arun created `Payments Q1` | Priya removes Arun | `Payments Q1` listed in `Priya's workspace`, contents unchanged |
| Removed member keeps own workspace | US-014-AC15 | Arun removed | Arun opens Roadsmith | `Arun's workspace` available; `Priya's workspace` absent from choices |
| Owner cannot leave | US-014-AC16 | Priya sole owner | Priya removes Priya's own membership | Refused; Priya still owner |

## Dependencies

- **Stories**: US-013 (sign-up, sign-in, and workspace isolation must work
  first).
- **Feature Spec**: FEAT-005 — Accounts and Workspaces
- **Feature Requirements**: ISO-05–ISO-07, MEM-01–MEM-15
- **PRD Requirements**: FR-19
- **External**: An external email service that delivers invitation emails,
  selected in Design. Tests stub it to accept or reject. The authentication
  interface Contract is defined in Design.

## Out of Scope

- Viewer, commenter, or admin roles, and transferring ownership.
- Per-roadmap access within a workspace.
- Public or read-only share links (PRD non-goal: stakeholder portals).
- Editors leaving a workspace on their own.
- Moving a roadmap from a personal workspace into `Priya's workspace`.
- Live co-editing between Priya's PMs (PRD non-goal).
