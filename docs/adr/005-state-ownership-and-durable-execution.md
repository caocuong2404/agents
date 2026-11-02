# ADR-005 — Keep authoritative work state outside replaceable execution

Date: 2026-11-02  
Status: Accepted  
Supersedes: ADR-005 and state-ownership proposal in baseline v0.3  
Amends: ADR-005 in v1.0  
Change note: SUBSTANTIVE — Adopt the guarded Task/Assignment transition tables and consistent fence-token protocol, including lease expiry and restore [D3, D5].

## Context

An agent process can stop, an event can be delivered twice, a user can change requirements during execution, and a remote action can succeed before its receipt is recorded. Persisted chat history alone does not resolve these cases.

## Decision

Give every task an authoritative record containing objective, acceptance criteria, scope, revision, lifecycle state, assignment generation, approval/operation references and evidence references. Give every native conversation a separate transcript identity. A task can span conversations and channels; a conversation can contain several tasks.

Use task-specific Durable Object SQLite persistence for task records and authorization-relevant operation records. Use scoped memory repositories for shared knowledge. Existing harness persistence owns native transcripts. R2 owns retained large artifacts when needed. D1 or semantic indexes may provide projections; they are not competing writers of task or permission state.

All claims and transitions check the expected revision and generation and commit atomically in the authoritative owner. Persist dispatch intent before delivery. Reconcile pending delivery after restart. A single-threaded Durable Object does not make arbitrary operations across `await` boundaries transactional; keep transactions short and never hold one around model or network calls.

### Revision and assignment rules

- `task_revision` changes when the accepted objective, scope or acceptance criteria change. Delivery of a new instruction alone does not silently alter the record; validate it first.
- `generation` increases whenever execution ownership is transferred or invalidated. Claims, heartbeats, reports and effects bind to it.
- A lease is renewable within policy. Expiry permits a new assignment but cannot physically kill a disconnected process.
- A stale generation cannot update current state, consume an approval or execute an effect. Store a rejected report as diagnostic evidence if useful.
- An in-scope result for an old revision may be retained as an artifact but is not accepted or published as the current result.

Restoring/importing authoritative state creates a new authority epoch and invalidates old execution grants and pending approvals. Generation comparisons are scoped to that epoch. Reconcile externally visible operations since the restore point before allowing new writes; a restored counter or old receipt snapshot cannot establish that no later action happened.

### Recovery and long waits

Persist acceptance before returning a durable ACK. Use one owner for each timer/retry: native harness recovery for a turn, or Workflows for a discrete multi-step pipeline. The task record owns business state; the selected engine owns execution checkpoints. A failed execution attempt can be replaced without pretending that completed external actions were rolled back.

Store pending human decisions and release execution resources while waiting. Cancellation moves through `CANCEL_REQUESTED` until workers and operations have been reconciled; it is not evidence that an already-started external action was undone.

### v1.1 state and fencing amendment

The Task transition matrix in CONTRACTS is authoritative for allowed From/To changes, actors, epoch/generation/revision guards and side effects. Lease expiry changes RUNNING to QUEUED and increases generation; restore rotates authority_epoch and invalidates old grants. Review has explicit acceptance, rework and changed-requirements outcomes. Cancellation remains pending until execution is stopped/fenced and effects are accounted for. The AssignmentStatus values explain lease expiry, supersession and revocation without inventing a replay state.

executor.claim returns an ExecutionFence bound to the registered executor, Assignment, current Task revision/epoch/generation and live lease. Heartbeat, checkpoint, report and operation.propose require it; stale calls return the structured STALE_FENCE error without current-state mutation. Internal recovery uses a current service/control fence to preserve late receipts, never an old executor grant. Gate 3 tests each stale mutating executor/operation entry point. ADR-014 governs identity; ADR-015 governs backup/restore.

## Alternatives and consequences

One global LLM context and multiple writable truth stores are rejected. No active-active cross-cloud state is promised. Durable stores and indexes need export, restore and access tests, not merely backups configured in a console.

## Acceptance and reconsideration

Inject failures before/after claim, model call, checkpoint, approval and external action. Verify deduplication, current revision, fencing, cancellation and restore. Choose different object granularity only after measured contention or access needs; preserve single authority per logical record.

Related: [ADR-002](002-existing-harness-and-template-baseline.md), [ADR-004](004-executor-contracts-and-portability.md), [ADR-006](006-authorization-and-external-effects.md), [ADR-008](008-cloud-edge-and-hardware-extension.md), [ADR-009](009-observability-evaluation-and-recovery.md), [ADR-010](010-mvp-scope-and-release-gates.md), [ADR-011](011-telegram-and-human-approval.md), [ADR-012](012-shared-memory-and-context-service.md), [ADR-013](013-event-driven-superagent-coordination.md), [ADR-014](014-identity-and-authorization.md), [ADR-015](015-operational-baseline.md).
Sources: S02, S05, S12, S13.
