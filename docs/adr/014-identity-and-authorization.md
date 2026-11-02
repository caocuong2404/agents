# ADR-014 — Bind verified principals to explicit workspace authority

Date: 2026-11-02  
Status: Accepted; owner identity and credential configuration remain open  
Adds: Identity and authorization baseline absent from v1.0  
Amends: ADR-001, ADR-005, ADR-006, ADR-011, ADR-012  
Change note: SUBSTANTIVE — require verified principal mappings, server-owned grants, revocable executor credentials and an independently reachable stop path [D1, D5].

## Context

An authenticated connection identifies a caller; it does not authorize a workspace or an object. Existing v1.0 records require scope checks but omit the policy owner, principal lifecycle and executor enrollment. Nothing is deployed. Acceptance here establishes an implementation baseline, with owner configuration still open.

## Decision

### Principals and grants

Use the closed PrincipalKind vocabulary and gate annotations in [CONTRACTS](contracts.md). Owner, client/bot, executor and inbound webhook are separate principals. Internal services have separate deployment identities. Never reuse the owner's administrative credential in a client or runner.

| Principal        | Source of identity                                                               | Maximum authority                                                                                           |
| ---------------- | -------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| Owner            | Verified login bound to the enrolled owner principal                             | Review grants, revoke credentials, approve eligible operations and stop work through trusted control routes |
| Client/bot       | Individually registered application credential or verified OAuth subject         | Explicit workspace grants; no owner approval or policy administration                                       |
| Executor         | Operator-approved registration and an executor credential                        | Declared capabilities intersected with registration, workspace grant and current Assignment                 |
| Inbound webhook  | Registered source plus supported signature/secret verification and replay checks | Deliver events in the bound workspace; event content never becomes owner authority                          |
| Internal service | Deployment-bound service identity                                                | Named repository, scheduler, broker or recovery actions; no ambient universal authority                     |

A WorkspaceGrant binds one principal, one workspace, allowed action names, permitted resource selectors and a policy version. An absent grant denies access. Resource selectors are server-reviewed constraints, not arbitrary model-supplied code. Resolve workspace membership from the authoritative catalog before loading content. Check both the requested workspace and the object's stored workspace; guessing an ID cannot cross either boundary. Public classification also requires a grant.

MCP OAuth uses a verified issuer and subject, validated audience/client binding and token validity. The server maps that external identity to a registered principal, then looks up WorkspaceGrant records. OAuth scopes restrict the result; they cannot enlarge it. An unmapped subject, unapproved client, mismatched issuer/audience or caller-supplied workspace claim is denied. Do not infer tenancy from an email suffix. Use the authenticated MCP example only as integration guidance (S03); application grants remain this platform's responsibility.

### Policy ownership and enrollment

Version-reviewed policy configuration is deployed to the trusted application. Principal bindings, WorkspaceGrant records, credential verification metadata, executor registrations, policy version and kill-switch state live in an operator-controlled authority registry backed by the existing Durable Object SQLite boundary. Models may receive a permitted capability summary; they have no write route or secret access to this registry. Learned Memory cannot modify policy. Policy administration is an owner control-plane action, never an agent Tier-2 capability.

Bootstrap is an explicit offline/operator installation of the owner binding and deny-by-default policy; no first caller becomes administrator. After bootstrap, control mutations use an authenticated owner, a current ControlFence and an idempotency key as defined in CONTRACTS. Policy changes invalidate affected fence tokens and cached decisions. Revocations must be checked at the point of mutation/dispatch; a stale cache or unavailable registry fails closed. Do not claim a distributed transaction across policy, task stores and a remote API. Admission/dispatch rechecks and durable recovery records enforce the boundary; an effect already sent is reconciled.

The owner approves executor identity, workspace, permitted classification, capability set and environment restrictions before registration becomes usable. Capability declarations during claim can only narrow this registration. Enrollment returns verification metadata and a narrowly scoped executor credential through a trusted channel; the model/task/context never receives a control or provider secret. Claims return an ExecutionFence bound to that executor and Assignment. Registration alone is insufficient to claim arbitrary tasks.

### Credentials, revocation and restore

Issue separate credentials for each principal and integration. Store opaque credential verification material or verify signed tokens against a trusted issuer; never persist plaintext bearer secrets as Memory. Use Workers secret bindings for Worker-side provider/bot/webhook secrets (S25), outside prompts, retained artifacts and audit content. Client and executor credentials must be revocable server-side even if their nominal expiry is later. Issuance records identify subject, audience, workspace restrictions, validity and credential version. Never log the credential itself.

Rotation is an operator sequence: issue a replacement, deliver it through the trusted enrollment channel, confirm the intended consumer, revoke the predecessor and invalidate affected leases/callback references. Any overlap duration, token lifetime and routine rotation cadence are **OPEN: owner decision — OD-03**. Suspected compromise triggers revocation without waiting for routine rotation. Deleting a Worker secret is not proof that a credential has been revoked at its issuer.

Restore begins with ingress/dispatch disabled. An operator establishes a fresh authority epoch from the current recovery control configuration outside the restored snapshot, invalidates credentials/fences and pending approvals affected by the rollback, and verifies current owner/grants before reopening. A rolled-back registry cannot resurrect a revoked credential. Retain a separately controlled revocation/recovery record or re-enroll all affected principals; the recovery choice is OD-05. No snapshot counter alone proves freshness.

### Kill switch

Provide authenticated owner stop controls for workspace and platform scope, reachable without a Router/model turn and through the web control route when Telegram is unavailable. Setting the switch advances policy_version and invalidates affected execution authority, denies new claims and mutating work, fences active assignments, revokes pending approvals and stops new broker dispatch. Retain a recovery-only service permission for status reads, evidence preservation and reconciliation; it cannot initiate a new external write. Re-enabling requires an explicit owner action, fresh authority and renewed task/operation checks. It never replays a historical action automatically. A disconnected process may still run locally, and an already-issued external action may finish; record and reconcile both. Device emergency stop remains local under ADR-008.

### Configuration still requiring the owner

**OPEN: owner decision — OD-01:** choose the owner login and external-subject mapping: (A) reuse an existing authenticated Cloudflare/approved OAuth identity, recommended if already available; (B) use a separately enrolled existing web identity provider. No new identity product is selected. Actual issuer, subject, audience and recovery identity are unset.

**OPEN: owner decision — OD-02:** choose the initial principal/workspace grant inventory: (A) one owner workspace and individually enrolled clients, recommended for the first memory gate; (B) separate personal/project workspaces and explicit per-client grants. Neither option permits confidential data by default. Actual principals and grants remain unset. Credential timing is OD-03; secret procedure and ingress limits are OD-06 and OD-07.

## Alternatives and consequences

Shared owner credentials and token-embedded workspace claims without authoritative lookup are rejected. A standalone new identity product is not required. Central grant/revocation checks add a trusted dependency and must fail closed. Models remain useful clients without becoming policy administrators.

## Acceptance and reconsideration

Gate 0 reviews bootstrap, owner recovery, registry write access, credential handling and the stop path. Gate 1 tests forged scope/object IDs, unmapped OAuth subjects, revoked clients and an attempted policy write through Memory. Gate 3 tests unregistered executors and stale fences per call. Gate 4 tests owner pairing and callbacks separately from bot identity. These are required future tests, not reported runtime results. See [VALIDATION](validation.md).

Related: [ADR-001](001-platform-scope-and-reuse.md), [ADR-004](004-executor-contracts-and-portability.md), [ADR-005](005-state-ownership-and-durable-execution.md), [ADR-006](006-authorization-and-external-effects.md), [ADR-007](007-tools-skills-and-configuration.md), [ADR-008](008-cloud-edge-and-hardware-extension.md), [ADR-010](010-mvp-scope-and-release-gates.md), [ADR-011](011-telegram-and-human-approval.md), [ADR-012](012-shared-memory-and-context-service.md), [ADR-013](013-event-driven-superagent-coordination.md), [ADR-015](015-operational-baseline.md).
Sources: S03, S13, S18, S25.
