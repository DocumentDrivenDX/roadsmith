---
ddx:
  id: FEAT-005
  authoring:
    home: repo
  depends_on:
    - prd
  links:
    - id: concerns
      kind: informed_by
    - id: principles
      kind: informed_by
---

# Feature Specification: FEAT-005 — Accounts and Workspaces

**Feature ID**: FEAT-005
**Priority**: P0 (FR-18 is P0; the Membership and Invitations area, FR-19, is P1)
**Owner**: Product and Engineering
**Covered PRD Subsystem(s)**: Accounts and Workspaces
**Covered PRD Requirements**: FR-18, FR-19, FR-20
**Cross-Subsystem Rationale**: None — single subsystem.

## Overview

Accounts and Workspaces implements PRD P0-7 (FR-18), P1-5 (FR-19), and P1-6 (FR-20). People
sign up, sign in, and sign out. Every roadmap belongs to one workspace, and only
that workspace's members can list, read, or change it. A workspace owner can
invite teammates by email with edit access and can remove them. The PRD
treats roadmap content as customer-confidential, so isolation is a P0
release condition (PRD Success Criteria).

## Ideal Future State

Maya signs up and lands in a personal workspace where Maya's
roadmaps are private to Maya. No other Roadsmith user can find, open, or change
them, whatever address they type. Priya invites the three PMs on the team to one
shared workspace by email. Each PM joins with edit access in one step, whether or not
they already have an account. When a PM leaves, Priya removes them, and their
access ends on their next action, not at their next sign-in. The roadmaps they
built stay with the workspace.

## Problem Statement

- **Current situation**: Roadsmith has no accounts. The PRD requires
  signed-in users and workspace-private roadmaps (FR-18), and shared
  workspaces for teams (FR-19).
- **Pain points**: Without accounts, a roadmap cannot be private. Maya cannot
  trust Roadsmith with confidential plans. Priya cannot review the PMs'
  roadmaps in one place without copying them.
- **Desired outcome**: A user who is not a member of a workspace cannot list,
  read, or change any of its roadmaps (PRD acceptance sketch P0-7). A new user
  reaches an empty personal workspace from sign-up with no setup step.

## Functional Areas

| Area | User question or job | Feature responsibility |
|------|----------------------|------------------------|
| Account and session | "How do I get in, stay in, and get out?" | Sign-up, sign-in, sign-out, session expiry, sign-in throttling, password reset |
| Workspace isolation | "Who can see and change this roadmap?" | Roadmap-to-workspace ownership, member-only access enforced server-side, choosing among several workspaces |
| Membership and invitations (P1) | "How do I bring my team in, and take someone out?" | Owner and editor roles, email invitations, invitation expiry and revocation, member removal |

**Domain terms.** These terms are separate and must not be conflated:

- **Account**: one person's sign-in identity, keyed by email address. An
  account holds no roadmaps directly.
- **Workspace**: the container that owns roadmaps and has members. One
  account can be a member of several workspaces.
- **Member**: an account's membership in one workspace, with one role
  (owner or editor).
- **Invitation**: a pending, expiring offer of editor membership addressed to
  an email address. It grants nothing until accepted.

## Task Model

| Task | Goal | Users | Needs | Rules | Realized by | Key |
|------|------|-------|-------|-------|-------------|-----|
| Sign up and start planning | Signed in to a personal workspace with no roadmaps | Sole or lead PM, team of 5–50 | "Get my backlog in without a day of setup" | — | US-013 | sign-up |
| Keep a roadmap private | Non-members cannot list, read, or change the roadmap | Sole or lead PM, team of 5–50 | None listed; PRD Constraint: roadmap content is customer-confidential | — | US-013 | keep-private |
| Bring PMs into a shared workspace | Each invited PM can edit the workspace's roadmaps | Head of Product over 2–5 PMs | Consistent checks across teams | — | US-014 | invite-teammates |
| Remove a departed PM | The removed PM can no longer reach the workspace's roadmaps | Head of Product over 2–5 PMs | Consistent checks across teams | — | US-014 | remove-member |

## Requirements

### Functional Requirements by Area

#### Account and session (FR-18 P0; ACC-11 to ACC-13 are FR-20, P1)

ACC-01. A visitor must be able to sign up with a name, an email address, and a
password.
ACC-02. Completing sign-up must create the account, a personal workspace with
that account as its owner, and a signed-in session, in one flow.
ACC-03. Sign-up must refuse a password shorter than 12 characters and state the
minimum length.
ACC-04. Sign-up must refuse an email address that already has an account,
compared case-insensitively, and offer sign-in instead.
ACC-05. A person with an account must be able to sign in with their email
address and password.
ACC-06. A failed sign-in must show the same message whether the email address
has no account or the password is wrong.
ACC-07. After 5 consecutive failed sign-in attempts for one email address
within 15 minutes, the system must refuse every sign-in attempt for that
address for the next 15 minutes and state when to retry.
ACC-08. A signed-in person must be able to sign out. After sign-out, that
session must not be able to list, read, or change any roadmap.
ACC-09. A session must end after 14 days without activity or 30 days after
sign-in, whichever comes first. After a session ends, the next request must
direct the person to sign in.
ACC-10. The system must never store, display, or record a password in readable
form, including in logs and telemetry.
ACC-11. (P1) A person who has forgotten their password must be able to request
a reset link sent to their email address. The link must be single-use and
expire 60 minutes after sending.
ACC-12. (P1) The response to a reset request must be the same whether or not
the email address has an account.
ACC-13. (P1) Setting a new password through a reset link must end every other
session of that account.

#### Workspace isolation (FR-18, P0)

ISO-01. Every roadmap must belong to exactly one workspace: the workspace in
which it was created.
ISO-02. Only members of a roadmap's workspace may list, read, or change that
roadmap. The server must enforce this on every request, keyed on the signed-in
account, regardless of what the interface shows or hides.
ISO-03. A request by a non-member for a roadmap must receive the same response
as a request for a roadmap that does not exist.
ISO-04. A request without a valid session for any roadmap or workspace content
must be refused and directed to sign-in.
ISO-05. A person who is a member of more than one workspace must be able to
choose the workspace they are working in.
ISO-06. The roadmap list must show only roadmaps of the workspace the person is
working in.
ISO-07. Access checks must use membership as it stands at the time of each
request. A membership change takes effect on the member's next request, with
no sign-out required.

#### Membership and invitations (FR-19, P1)

MEM-01. A workspace has exactly one owner: the account whose sign-up created
it. Every other member is an editor.
MEM-02. An editor must be able to create, read, and change every roadmap in the
workspace.
MEM-03. The server must refuse an editor's attempt to invite, revoke an
invitation, or remove a member.
MEM-04. The owner must be able to invite a person by email address to join the
workspace as an editor. The system sends the invitation through the external
email service.
MEM-05. An invitation must expire 7 days after it is sent and must be usable
once.
MEM-06. Accepting an invitation must require a signed-in account whose email
address matches the invited address, compared case-insensitively.
MEM-07. A person with no account who opens an invitation must be able to sign
up from it with the invited email address. Completing sign-up must add them to
the inviting workspace as an editor, in addition to ACC-02.
MEM-08. The owner must be able to see the workspace's members with their roles
and its pending invitations with their expiry dates.
MEM-09. The owner must be able to revoke a pending invitation. A revoked
invitation must not be acceptable.
MEM-10. Inviting an address that already has a pending invitation must replace
it. The earlier invitation must stop being acceptable.
MEM-11. Inviting an address whose account is already a member must be refused
with a message saying so.
MEM-12. When the external email service does not accept an invitation email,
the owner must be told the invitation was not sent and must be able to resend
it.
MEM-13. The owner must be able to remove an editor. From the removed member's
next request, ISO-02 applies to them as to any non-member.
MEM-14. Roadmaps a removed member created must stay in the workspace,
unchanged.
MEM-15. The owner must not be able to remove themselves or be removed.

### Non-Functional Requirements

- **Performance**: Sign-in completes in under 1 second at the 95th percentile,
  measured server-side including password verification *(assumption: no PRD
  target)*. The workspace membership check adds under 20 ms at the 95th
  percentile to any roadmap request *(assumption)*.
- **Security**:
  - Passwords are stored only as salted, work-factored hashes
    (`auth-local-sessions` concern).
  - Session credentials are unreadable by page scripts. In production they
    travel only over HTTPS (Hypertext Transfer Protocol Secure).
  - Sessions are validated server-side on every protected request.
  - Invitation and reset links each carry at least 128 bits of randomness
    *(assumption)*.
  - Throttle and expiry values follow ACC-07, ACC-09, ACC-11, and MEM-05
    *(all assumptions: no PRD target)*.
- **Scalability**: A workspace supports 50 members, 20 pending invitations,
  and 100 roadmaps with the performance targets above still met. One account
  can belong to 10 workspaces *(assumptions derived from the PRD's 5–50
  person team segment)*.
- **Reliability**: When the external email service is unavailable, sign-up,
  sign-in, sign-out, and all roadmap access keep working. Only sending
  invitations and reset links fails, with the message in MEM-12.
- **Usability**: Sign-up, sign-in, and member management meet WCAG (Web
  Content Accessibility Guidelines) 2.x level AA and are fully operable by
  keyboard (`a11y-wcag-aa` concern).

## User Stories

- [US-013 — Sign up and keep roadmaps private](../user-stories/US-013-sign-up-and-keep-roadmaps-private.md)
- [US-014 — Invite teammates](../user-stories/US-014-invite-teammates.md)

- [US-015 — Reset a forgotten password](../user-stories/US-015-reset-forgotten-password.md) (ACC-11 to ACC-13, PRD FR-20, P1)

## Edge Cases and Error Handling

- **Session ends mid-edit**: The next change is refused and the person is
  directed to sign in. Changes saved before the session ended stay saved.
- **Member removed while viewing a roadmap**: The removed member's next
  request for that roadmap gets the non-member response (ISO-03). Their
  session stays valid for their other workspaces.
- **Invitation opened by a different signed-in account**: Acceptance is
  refused. The person is told the invitation is for another email address
  and is offered to sign out and continue.
- **Expired, revoked, or replaced invitation opened**: The person is told the
  invitation is no longer valid and to ask the owner for a new one. No
  membership is created.
- **Removed member re-invited**: A new invitation works as for anyone else.
  The person rejoins as an editor.
- **Owner attempts to leave or be removed**: Refused with a message that the
  workspace needs its owner (MEM-15).
- **Duplicate sign-up submission**: Two submissions of the same sign-up
  create one account and one personal workspace.
- **Email address case differs**: `Maya@Northwind.example` and
  `maya@northwind.example` are the same address for sign-up, sign-in, and
  invitation matching.
- **Simultaneous edits by two members**: Both members can edit (PRD
  non-goal: no live co-editing). How a conflicting save is resolved is
  decided in Design.

## Success Metrics

- **Sign-up completion**: share of started sign-ups that reach a personal
  workspace. Learning goal, no target. Observed through product analytics.
- **Invitation acceptance**: share of sent invitations accepted before expiry.
  Learning goal: a low share would question the 7-day expiry or email
  delivery. Observed through product analytics.

## Constraints and Assumptions

- **Constraint**: All sign-up, sign-in, sign-out, and session checks go through
  one authentication interface. An OIDC (OpenID Connect, the OpenID Foundation
  identity layer over OAuth 2.0) provider can replace local passwords without
  changes to roadmap features (`auth-local-sessions` concern).
- **Constraint**: Isolation keys off the authenticated account resolved on
  the server for each request, never off client-supplied workspace identity
  alone.
- **Assumption**: Every sign-up creates a personal workspace owned by the new
  account, including sign-ups made from an invitation.
- **Assumption**: v1 roles are owner and editor only. Each workspace has
  exactly one owner, its creator. The PRD grants invitees "edit access" and
  names no other role.
- **Assumption**: Password reset is P1 and ships with FR-19, which brings the
  external email service dependency. P0 (FR-18) needs no email delivery. The
  PRD now records password reset as FR-20 (P1); the operator may re-prioritize.
- **Assumption**: Sign-up does not verify email ownership in v1. Accepting an
  invitation proves control of the invited address, because the link is sent
  only there.
- **Assumption**: Sign-up reveals that an email address already has an account
  (ACC-04). Sign-in and reset do not (ACC-06, ACC-12).
- **Assumption**: The numeric values in ACC-03, ACC-07, ACC-09, ACC-11, MEM-05,
  and the NFRs have no PRD source and are open to operator change.

## Dependencies

- **Other features**: FEAT-005 is upstream of every roadmap feature. Each
  roadmap-owning feature relies on ISO-01 and ISO-02 for its data.
- **External services**: An external email service delivers invitation and
  reset emails (P1 only; selected in Design). The authentication interface is
  defined by a Contract in Design.
- **PRD requirements**: FR-18 (P0-7), FR-19 (P1-5).

## Out of Scope

- **SSO and OIDC sign-in**: v1 uses local email-and-password accounts. Only
  the swap path is required (Constraints).
- **Viewer, commenter, or admin roles; ownership transfer**: v1 has owner and
  editor only.
- **Per-roadmap permissions**: Membership grants access to every roadmap in the
  workspace.
- **Public, read-only, or share links**: Stakeholder portals are a PRD
  non-goal.
- **Moving a roadmap between workspaces**: A roadmap stays in the workspace
  where it was created.
- **Editors leaving a workspace on their own**: Only the owner removes members.
- **Workspace rename or deletion, account deletion, email address change**:
  Not required by FR-18 or FR-19.
- **Multi-factor authentication and email verification at sign-up**: Not
  required by FR-18; revisit with the auth-provider swap.
- **Audit history of membership changes**: Not required by FR-19.
- **Billing, seats, or plan limits**: No pricing decision exists (PRD
  Constraints).
