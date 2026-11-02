# ADR-004 — Make executors replaceable at application boundaries

Date: 2026-11-02  
Status: Accepted  
Supersedes: ADR-004 in baseline v0.3  
Amends: ADR-004 in v1.0  
Change note: SUBSTANTIVE — Executor adapters use server-issued fence tokens and gate-scoped contracts; native harness claims start at Gate 2 and external replacement is qualified at Gate 3 [D5, D13].

## Context

Execution may use a provider CLI, an existing agent platform, an isolated container, an on-demand local machine or a future device gateway. These systems have different native sessions and tool semantics. Reconstructing every internal runtime detail would be costly and brittle.

## Decision

Define an application-level task/assignment/report contract. A compatible executor receives a scoped objective, acceptance criteria, relevant context, allowed capabilities, current task revision, execution generation, deadline and budget. It returns progress, evidence and a proposed result.

Use native HTTP/RPC/SDK integration before introducing a custom adapter. Add a small adapter only for actual semantic differences. Discover capabilities explicitly: execution type, permitted data, tool availability, checkpoint/report support and online status. An executor unable to satisfy required capabilities must decline the assignment.

An external runner can pull assignments through an authenticated endpoint. Use a durable dispatch record and atomic lease claim. The MVP need not add Cloudflare Queues just to transport an assignment. If Queues are added, the task record still owns assignment authority; delivery acknowledgment and a business execution lease remain separate concepts.

A sandbox retains temporary files and process state while running. Canonical task state, accepted checkpoints and retained artifacts live in the shared service. No always-on local runner is assumed. Disconnection can delay work requiring that environment without making the cloud coordinator unavailable.

Replacement provides semantic recovery: a new worker receives objective, evidence, files and checkpoints. Native token caches, hidden reasoning, in-memory processes and proprietary CLI sessions are not universally portable. Report the recovery point honestly rather than claiming instruction-level continuation.

Export application records as versioned JSON/JSONL and artifacts with manifests and digests. Keep native harness exports as optional attachments. Import into an inactive environment, preserve operation IDs and receipts, and reconcile unresolved effects before resuming work. Import never replays historical operations automatically.

### v1.1 executor amendment

Use the fence protocol and call registry in [CONTRACTS](contracts.md). executor.claim returns an ExecutionFence. executor.heartbeat, executor.checkpoint, executor.report and operation.propose require that token plus their idempotency key and current revision guards. A caller cannot manufacture authority from an epoch/generation tuple. Every other mutating interface also has an explicit server-issued fence. Late evidence may be retained only by the current trusted service without accepting an obsolete result. ADR-014 defines enrollment/revocation; ADR-015 defines recovery prerequisites.

## Alternatives and consequences

Keep-everything-in-a-sandbox is rejected because it makes a replaceable runner the only copy of important state. A universal container image and a universal tool protocol are not required. Some integrations will remain product-specific.

## Acceptance and reconsideration

Replace executor A with B after an accepted checkpoint. Reject A's later stale report. Validate export/import, artifact integrity and unsupported-capability handling. Add additional adapters only when a second real executor requires them. A fake IoT executor may test contract compatibility without committing to hardware deployment.

Related: [ADR-001](001-platform-scope-and-reuse.md), [ADR-005](005-state-ownership-and-durable-execution.md), [ADR-006](006-authorization-and-external-effects.md), [ADR-007](007-tools-skills-and-configuration.md), [ADR-008](008-cloud-edge-and-hardware-extension.md), [ADR-010](010-mvp-scope-and-release-gates.md), [ADR-012](012-shared-memory-and-context-service.md), [ADR-013](013-event-driven-superagent-coordination.md), [ADR-014](014-identity-and-authorization.md), [ADR-015](015-operational-baseline.md).
Sources: S01, S02, S12.
