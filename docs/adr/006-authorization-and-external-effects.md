# ADR-006 — Enforce authority in trusted capabilities and isolate execution

Date: 2026-11-02  
Status: Accepted; implementation must pass the security gate  
Supersedes: ADR-006 in baseline v0.3  
Amends: ADR-006 in v1.0  
Change note: SUBSTANTIVE — Close data/trust vocabularies, require distinct broker/assignment fences and idempotent proposals, and block first Tier-1 enablement on owner fixture selection [D4, D5, D6, D12].

## Context

Agents consume untrusted messages, source code, tool output and retrieved memory. Existing CLIs and templates can run with broad OS access. A prompt, subscription or OAuth consent screen does not establish all application permissions or sandbox isolation.

## Decision

Authenticate humans, clients, webhooks and executors. Derive allowed workspaces and capabilities from verified identities and server-side policy. A caller-supplied `scope`, Telegram username, task role or message mention cannot grant access. Verify signatures or supported secret mechanisms for inbound integrations and deduplicate source events.

Represent external actions as typed operation proposals. A trusted broker performs policy checks and invokes an allowlisted capability using credentials unavailable to untrusted execution. Check subject, target, method, resource, parameters, data classification, task revision, generation, preconditions and approval. Do not expose a generic privileged URL fetch or unrestricted shell as the broker API.

An execution sandbox has no ambient production kubeconfig, SSH agent, privileged host mount, organization master token or administrative cloud credentials. Apply OS/container isolation and scoped egress appropriate to its runtime. A denylist, command wrapper, prompt instruction or per-task directory is not an isolation boundary. Approved inference credentials require a separate, tested adapter boundary; an unqualified CLI does not gain privileged integrations.

### Operation lifecycle

Persist the immutable operation descriptor and dispatch intent before calling a remote service. Use a stable idempotency key for the logical operation, independent of retries. Approvals bind to its exact arguments, resource and task revision. Execution requires a current generation; reassignment requires explicit revalidation and, where needed, a new proposal.

Use provider idempotency when available. When completion is ambiguous, set `UNKNOWN`, inspect the external service and reconcile before retry. A transactional outbox prevents lost intent; it does not create exactly-once external effects across arbitrary APIs. Never retry an unknown non-idempotent operation blindly.

### Initial risk policy

| Tier | Permission                                                                                                                                                             |
| ---- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 0    | Automatic read-only work and application-owned scratch/memory writes within an already authorized scope                                                                |
| 1    | Scoped, permitted external mutations require one-time human approval; initial enablement is limited to an allowed test integration                                     |
| 2    | Sensitive/destructive actions, production changes, financial commitments, identity/secret changes, infrastructure privilege and physical actuation are disabled in MVP |

Employer/client confidential information, production secrets and sensitive personal/third-party records remain excluded until separately authorized. Permission to perform one task never authorizes unrestricted data collection or disclosure.

### v1.1 exact intent and test boundary amendment

The trusted Task owner mints operation_id exactly once when operation.propose first commits an idempotency_key and immutable descriptor. The same key with changed logical arguments is IDEMPOTENCY_CONFLICT. Executor replacement reuses the durable intent key/operation_id; a new key cannot bypass unresolved earlier intent, approval or dispatch guards. Proposal uses the current ExecutionFence. Dispatch requires the broker's own service RecordFence plus the current Assignment execution_fence; the executor token never authenticates the broker or grants a worker access to its routes. Receipt/reconciliation writes use a current broker RecordFence and cannot authorize another remote write. See CONTRACTS for namespaces and canonical payload rules.

Use Classification and TrustClass from CONTRACTS. Labels preserve the existing excluded-data boundary; a trusted source label never grants access and a public label never crosses a workspace grant. Identity, grant ownership, revocation and kill switch are specified in ADR-014; secret and ingress prerequisites are in ADR-015.

**OPEN: owner decision — OD-08:** the first Tier-1 allowed test integration is not selected. Options: (A) an application-owned canary receipt in a dedicated Task-store test namespace, recommended; (B) a fixture-file create/update inside an on-demand isolated scratch directory with no network or third-party mount. Both have a named owner-scoped destination, reversible cleanup and no third-party effects. Treat either as Tier 1 to exercise exact-intent human approval. Do not enable one until the owner chooses the fixture and scope. A fixture can simulate a lost receipt and reconciliation, but cannot establish a real external provider's idempotency guarantee. Any real integration requires separate owner selection and its own effect tests. Tier 2 remains disabled.

## Alternatives and consequences

Giving every worker all credentials is rejected. Prompt-only permission checks are rejected. Brokered actions add integration work and may limit CLIs that assume ambient access. The value is bounded authority, traceable decisions and safer replacement.

## Acceptance and reconsideration

Test scope forgery, prompt injection, unauthorized routes, secret access, stale proposals, approval replay, callback duplication and ambiguous completion. Maintain a kill switch and credential revocation. Enabling any Tier-2 capability requires a separate decision, threat model and domain-specific validation.

Related: [ADR-003](003-model-providers-and-cost-control.md), [ADR-004](004-executor-contracts-and-portability.md), [ADR-005](005-state-ownership-and-durable-execution.md), [ADR-007](007-tools-skills-and-configuration.md), [ADR-008](008-cloud-edge-and-hardware-extension.md), [ADR-009](009-observability-evaluation-and-recovery.md), [ADR-010](010-mvp-scope-and-release-gates.md), [ADR-011](011-telegram-and-human-approval.md), [ADR-012](012-shared-memory-and-context-service.md), [ADR-013](013-event-driven-superagent-coordination.md), [ADR-014](014-identity-and-authorization.md), [ADR-015](015-operational-baseline.md).
Sources: S03, S12, S14.
