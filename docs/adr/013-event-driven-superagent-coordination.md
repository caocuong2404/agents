# ADR-013 — Separate responsive coordination from concurrent task execution

Date: 2026-11-02  
Status: Accepted architecture; proactive autonomy introduced after validation  
Adds: Event-driven Router/Worker coordination to the v0.3 baseline

## Context

Manually managing unrelated agent sessions shifts coordination to the user. One synchronous session can mix tasks and block conversation behind long-running tools. The Super-agent design provides a useful architectural reference for continuity and concurrency, but its prototype and synthetic experiments are not proof of this project's production quality.

## Decision

Use a logical Router for human interaction, task correlation, high-level priorities and dispatch. Give each execution task an isolated worker context. Workers report structured progress, questions, evidence and results; the Router presents channel-appropriate communication. Worker diagnostics remain inspectable through authorized tooling without flooding external conversations.

The Router is not the detailed planner for every task and does not get general execution tools by default. An existing harness may perform complex decomposition inside a worker. Shared context is scoped to a permission domain; there is no requirement for one universal Router or one all-knowing model.

### Event intake and routing

Persist normalized source events with immutable IDs and deduplication keys. Apply deterministic source/identity/rule checks first. Use model judgment only where semantic assessment is required. External content remains evidence and cannot change application permissions.

Unowned events go to task correlation and assessment. Once a task subscribes to a thread, deterministic routing delivers relevant follow-ups to its owner. The worker can return an unrelated event for reassessment. Subscription membership conveys context only: scope expansion, changed requirements and external effects still pass authorization and revision checks. Many-to-many subscriptions are allowed when justified; overlapping access does not establish exclusive write ownership.

### Responsiveness and concurrency

Durably enqueue accepted work and return an ACK. Long operations run as background assignments, with later events carrying results. New messages are accepted while tools run; model handling may wait for the current inference step. This is not a guarantee of arbitrary mid-token preemption.

Batch eligible queued events within configured limits, retain their individual IDs and report routing decisions separately. Use backpressure, task/concurrency limits and priority for authenticated steering/cancellation. A stop command must reach the trusted task service without relying on an LLM to notice it.

### Proactivity

Start with explicit requests and permitted read-only observations. Speculative work needs an explicit rule, bounded budget and defined evidence/notification conditions. Observing public discussion does not authorize a public PR or an external mutation. Keep inference separate from action authority.

Use ADR-009's independent monitoring for both runtime health and task progress. Do not rely on the Router to repair itself while its own queue is stuck.

## Alternatives and consequences

A single blocking session and an unrestricted worker-to-worker mesh are rejected initially. Prompt-only encouragement to delegate is insufficient to enforce a responsive coordinator. The design increases coordination records and can increase token use; measure the benefit in accepted outcomes and reduced human attention.

## Acceptance and reconsideration

Test simultaneous independent tasks, same-thread updates, cross-channel correlation, out-of-scope follow-ups, burst intake, stop priority and restart. Reconsider Router sharding or peer communication only after measured coordination pressure, while preserving permissions and authoritative state.

Related: [ADR-001](001-platform-scope-and-reuse.md), [ADR-004](004-executor-contracts-and-portability.md), [ADR-005](005-state-ownership-and-durable-execution.md), [ADR-006](006-authorization-and-external-effects.md), [ADR-009](009-observability-evaluation-and-recovery.md), [ADR-010](010-mvp-scope-and-release-gates.md), [ADR-012](012-shared-memory-and-context-service.md), [ADR-014](014-identity-and-authorization.md), [ADR-015](015-operational-baseline.md).
Sources: S01, S02, S05.
