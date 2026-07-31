---
name: openwork-manage-org-members
description: Invite, role, and remove members of an OpenWork Den organization.
api: openwork:den-api
base_url: https://api.openworklabs.com
auth: "Authorization: Bearer <session-token>  (or x-api-key: <den-api-key> for org-key routes)"
operations:
  - postV1Invitations
  - getV1OrgsInvitationsPreview
  - postV1OrgsInvitationsAccept
  - postV1InvitationsByInvitationIdCancel
  - postV1MembersByMemberIdRole
  - postV1MembersByMemberIdTransferOwnership
  - deleteV1MembersByMemberId
---

# Manage OpenWork Den organization members

Administer who belongs to an OpenWork Den organization and what they can do.

## Steps

1. **Invite a member** — `POST /v1/invitations` (`postV1Invitations`) with the
   invitee email and target role. If the org enforces an allowed email domain,
   a mismatch returns `409 invite_email_domain_not_allowed`; if seats are
   exhausted you get `402 payment_required` (`InvitePaymentRequiredError`).
2. **Let the invitee preview and accept** — the invitee calls
   `GET /v1/orgs/invitations/preview` (`getV1OrgsInvitationsPreview`) then
   `POST /v1/orgs/invitations/accept` (`postV1OrgsInvitationsAccept`).
3. **Cancel a pending invitation** — `POST /v1/invitations/{invitationId}/cancel`
   (`postV1InvitationsByInvitationIdCancel`).
4. **Change a member's role** — `POST /v1/members/{memberId}/role`
   (`postV1MembersByMemberIdRole`).
5. **Transfer ownership** (owner only) — `POST /v1/members/{memberId}/transfer-ownership`
   (`postV1MembersByMemberIdTransferOwnership`).
6. **Remove a member** — `DELETE /v1/members/{memberId}` (`deleteV1MembersByMemberId`).

## Rules

- Errors use `{ "error": "<code>", "message": "..." }` (not RFC 9457). Handle
  `403 forbidden`/`reauth`, `402 payment_required`, and `409` domain/seat conflicts.
- No idempotency key is supported; retry only on network failure, and re-check
  state with a list call before re-issuing a create.
- See `../conventions/openwork-conventions.yml` and `../errors/openwork-problem-types.yml`.
