# ADR-010 — Ship through measured gates with a small initial scope

Date: 2026-11-02  
Status: Accepted; cost defaults require calibration  
Supersedes: ADR-010 and implementation order in baseline v0.3  
Amends: ADR-010 in v1.0  
Change note: SUBSTANTIVE — Separate the memory-only Gate 1 from Task/Assignment machinery at Gate 2 and approval/effect machinery at Gate 4 [D13].

## Context

The platform has a broad future use case, but the immediate goal is useful shared memory and integration with existing agents at low implementation effort. Earlier review suggested fixed schedules and performance targets without workload evidence. Hardware capability must not become a deadline-driven obligation.

## Decision

Release through evidence gates in VALIDATION.md, not a calendar promise. The initial order is:

1. Authenticated scoped shared memory, usable from existing clients (Gate 1).
2. Task records and task-bound context with one existing reasoning harness and permitted read-only tools, if coordination is needed (Gate 2).
3. One on-demand isolated software executor and verified replacement/recovery.
4. Telegram pairing and notifications, then scope-bound approvals for one allowed test mutation.
5. Bounded scheduling/proactivity and additional integrations based on observed demand.

The first useful workloads remain public technical research and source-backed summaries, ADR/design critique, read-only permitted repository triage, isolated inspection/testing and proposed code changes. Status digests may use Telegram after the bot is configured and the channel is authorized.

No always-on local machine is assumed. Do not introduce a container, extra database, semantic search, queue or workflow service before its specific requirement is present. A mock executor can validate extension contracts without provisioning a real device.

Retain v0.3's provisional targets: USD 15/month infrastructure, USD 20/month metered inference, USD 3/day inference, USD 0.50 estimated task warning and USD 1.00 estimated task scheduling stop. These are engineering defaults, not permission to activate paid resources or a billing guarantee. A paid dependency requires explicit account/spend authorization during deployment setup.

Retain the data boundary: public or explicitly authorized classified project material only. Production changes, physical actuation, secret rotation, financial commitments and other Tier-2 actions remain disabled in MVP. Do not make confidential employer/client data part of a demonstration.

Each release must have the relevant failure tests, pinned dependency evidence, resource inventory, cost observations and a restore path. Quantitative service thresholds are calibrated from representative runs before production reliance. Do not label template deployment as production qualification.

### v1.1 release-boundary amendment

Gate 0 establishes identity and applicable operational configuration under ADR-014 and ADR-015. Gate 1 activates only scoped Memory, authorization, idempotency and memory recovery; clients use recall/read with provenance. Task-bound context, Task lifecycle and Assignment/executor machinery start at Gate 2, with the existing harness as the native executor. Gate 3 qualifies an isolated replaceable external executor. Operation and Approval start at Gate 4, conditional on OD-08. Router Event intake is Gate 5. Gate labels in CONTRACTS are earliest activation stages, not passed evidence.

**OPEN: owner decision — OD-09:** the retained 15-minute approval expiry needs calibration for overnight phone approvals. Options: keep it and request while the owner is available; choose an owner-specified longer expiry after review; or notify overnight and issue a fresh short-lived Approval when review opens, recommended. No extension or new duration is selected. Budgets remain provisional under OD-10.

## Alternatives and consequences

A full multi-agent/IoT platform in the first release is rejected. A calendar-based expansion plan is rejected. A memory-only release provides value earlier, but full autonomous work and integrations arrive later.

## Acceptance and reconsideration

The memory release must pass cross-client authorization, provenance and restore tests. Any execution release must pass revision/generation replacement tests. Any external mutation release must pass approval and effect tests. Hardware remains a separate workstream triggered by a concrete device use case and its own authorization/safety requirements.

Related: [ADR-001](001-platform-scope-and-reuse.md), [ADR-002](002-existing-harness-and-template-baseline.md), [ADR-003](003-model-providers-and-cost-control.md), [ADR-004](004-executor-contracts-and-portability.md), [ADR-005](005-state-ownership-and-durable-execution.md), [ADR-006](006-authorization-and-external-effects.md), [ADR-007](007-tools-skills-and-configuration.md), [ADR-008](008-cloud-edge-and-hardware-extension.md), [ADR-009](009-observability-evaluation-and-recovery.md), [ADR-011](011-telegram-and-human-approval.md), [ADR-012](012-shared-memory-and-context-service.md), [ADR-013](013-event-driven-superagent-coordination.md), [ADR-014](014-identity-and-authorization.md), [ADR-015](015-operational-baseline.md).
Sources: project baseline v0.3; S02, S03, S05.
