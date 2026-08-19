---
name: blng-manage-workspace-membership
description: Create a BLNG workspace, invite and manage members, set roles and SSO, and stay off the seventeen deprecated operations in the User API.
api: BLNG User API
base_url: https://users.blng.ai
spec: openapi/blng-user-api-openapi.yml
generated: '2026-08-13'
method: generated
source: openapi/blng-user-api-openapi.yml
operations:
  - POST /users/{userId}/workspaces
  - GET /users/{userId}/workspaces
  - GET /users/{userId}/workspaces/{workspaceId}
  - PUT /users/{userId}/workspaces/{workspaceId}
  - GET /users/{userId}/workspaces/{workspaceId}/members
  - PUT /users/{userId}/workspaces/{workspaceId}/members/{memberUserId}
  - DELETE /users/{userId}/workspaces/{workspaceId}/members/{memberUserId}
  - POST /users/{userId}/workspaces/{workspaceId}/invitations
  - GET /users/{userId}/workspaces/{workspaceId}/invitations
  - POST /users/{userId}/workspaces/{workspaceId}/invitations/{invitationId}/resend
  - DELETE /users/{userId}/workspaces/{workspaceId}/invitations/{invitationId}
  - GET /users/{userId}/workspace-invitations/inbox
  - POST /users/{userId}/workspace-invitations/{invitationId}/accept
  - PUT /users/{userId}/active-workspace
  - PUT /users/{userId}/workspaces/{workspaceId}/sso-config
  - GET /users/{userId}/workspaces/{workspaceId}/integrity
---

# Manage BLNG workspaces and membership

Grounded in BLNG's own definition at `https://users.blng.ai/swagger.yaml`.

**This API declares no operationIds.** Every one of its 47 operations is identified by method + path only,
so generated clients will produce synthesized names. Address operations by path, as above.

Auth is an AWS Cognito bearer JWT, same as the Journey API.

## Use the workspace surface, not the legacy one

17 of the 47 operations carry `deprecated: true`. Three whole surfaces are dead:

- **`/invitations/*`** — all 8 operations. Superseded by `/users/{userId}/workspaces/{workspaceId}/invitations`.
- **`/organizations/*`** — all 4 operations. Superseded by workspaces.
- **`/composite/*`** — both operations (`createOrgAndSubscriptionWithUser`, `createUserAndSubscription`).

Also deprecated: `POST /users/{userId}/subscriptions`,
`PUT /users/{userId}/subscriptions/{subscriptionId}`, `POST /subscriptions`.

BLNG publishes no deprecation policy, no `Sunset` header and no removal date, so there is no warning you
will get at runtime — the flag in the spec is the only notice. Build against the workspace model.

## Steps

### 1. Create a workspace — `POST /users/{userId}/workspaces`

Body is `CreateWorkspaceRequest`; `parentWorkspaceId` nests this workspace under an enterprise parent.

**403** here means one of: multi-tenancy disabled on the account, a deleted account, insufficient access to
the parent enterprise, or the workspace-creation limit reached. The response does not tell you which — do
not retry on 403.

### 2. Invite a member — `POST /users/{userId}/workspaces/{workspaceId}/invitations`

Body is `CreateWorkspaceInvitationRequest`. Returns `WorkspaceInvitationCreated`.

- **400** — invalid body, or you tried to invite your own email address.
- **403** — multi-tenancy disabled, insufficient role, or the workspace type does not support invitations
  (personal Starter/Professional workspaces do not).
- **409** — a pending invite already exists, or the user is already a member.

List pending invitations with `GET /users/{userId}/workspaces/{workspaceId}/invitations`
(`WorkspacePendingInvitationAdmin`), revoke with
`DELETE .../invitations/{invitationId}`.

### 3. Resend — `POST .../invitations/{invitationId}/resend`

Rate limited. **429** means the cooldown between successful invitation emails has not elapsed. The
duration is not published and no `Retry-After` is returned, so back off conservatively.

### 4. Accept, from the invitee's side

- `GET /users/{userId}/workspace-invitations/inbox` — the invitee's pending invitations.
- `GET /users/{userId}/workspace-invitations/{invitationId}/email-match` — check the invite matches this
  account's email before accepting.
- `POST /users/{userId}/workspace-invitations/{invitationId}/accept` — accept.
  **409** if not pending, expired, already a member, an invalid role, or accepted concurrently.
  **503** on a DynamoDB throughput limit — **the spec explicitly says to retry this one.**

`GET /workspace-invitations/{invitationId}` returns the public projection
(`WorkspaceInvitationPublic`) for an unauthenticated landing page.

### 5. Roles and removal

- `PUT /users/{userId}/workspaces/{workspaceId}/members/{memberUserId}` — change a member's role
  (`UpdateMembershipRoleRequest`).
- `DELETE /users/{userId}/workspaces/{workspaceId}/members/{memberUserId}` — remove a member.
- `GET /users/{userId}/workspaces/{workspaceId}/members` — list members (`WorkspaceMembersList`).
- `GET /users/{userId}/memberships` — every membership for one user.

### 6. Active workspace

`PUT /users/{userId}/active-workspace` with `{"activeWorkspaceId": "..."}`. The Journey API scopes reads
and permissions by the caller's workspace, so set this before driving a design flow, or you will see
**403** on delete/restore paths there.

### 7. SSO

`PUT /users/{userId}/workspaces/{workspaceId}/sso-config` configures the workspace's federated IdP.
**400** if the body is invalid, no provider exists for the workspace, or the metadata is rejected by
Cognito. SSO is an Enterprise-tier feature per BLNG's published pricing.

### 8. Health check

`GET /users/{userId}/workspaces/{workspaceId}/integrity` returns a `WorkspaceIntegrityReport`. Useful
after bulk membership changes.

## Rules

- **No pagination anywhere in this API.** `GET .../members`, `GET /invitations` and `GET /organizations`
  return unbounded collections. Budget for large responses rather than assuming a page.
- **No idempotency key.** A retried invitation create surfaces as **409**, not as a no-op.
- Every operation declares **500 Internal Server Error**; only the accept path declares 503. Treat 500 as
  retryable with backoff and escalate to support@blng.ai.
- Marketing consent is a first-class resource — `GET`/`PUT /users/{userId}/marketing-consent` returns a
  `MarketingConsentReceipt`. Never set it on a user's behalf without their explicit action.
