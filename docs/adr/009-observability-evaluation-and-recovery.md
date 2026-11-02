# ADR-009 — Measure accepted outcomes and monitor independently

Date: 2026-11-02  
Status: Accepted  
Supersedes: ADR-009 in baseline v0.3

## Context

A process may be alive while its agent repeats commands, loses the objective or consumes budget without progress. Additional workers may increase supervision cost. Provider demos and synthetic scheduling benchmarks do not establish this platform's result quality or production reliability.

## Decision

Use existing platform and harness telemetry before adding a new observability system. Correlate events with workspace, task, revision, assignment, generation, model request, approval and operation IDs. Record concise decisions, status changes, evidence references and redacted error categories. Do not require storage or publication of hidden model reasoning.

Measure accepted task outcomes and human minutes per accepted outcome, together with elapsed time, cost, intervention count, retries, stale-result rejection and failure recovery. Measure quality using representative task acceptance criteria. Do not adopt an unsupported universal success percentage or latency SLO.

Separate two monitoring functions:

- Deterministic checks: heartbeat/lease expiry, deadlines, retry counts, budget, queued work and unconfirmed operations. These may pause or recover work within bounded policy.
- Optional semantic assessment: inspect evidence for progress or goal drift. It may suggest a different plan or request intervention. It cannot expand permissions or rewrite production policy autonomously.

Run recovery monitoring outside the Router's turn and queue. Start with a separately scheduled, minimal monitor. It needs scoped visibility into task state, not unrestricted access to all user data. A Cloudflare-hosted monitor is independent of a Router failure but still shares Cloudflare's failure domain; stronger provider-outage monitoring is a later availability decision.

Use retry limits, backoff, jitter and a circuit breaker. A monitor cannot restart a failed task indefinitely or approve its own proposed external effect. Recovery actions and their outcomes are audited.

Keep raw prompt/tool traces disabled by default. Enable bounded diagnostic capture only for classified, authorized data. Retention is finite and configured before production use; deletion/export must cover memory, artifacts, projections and backups according to policy.

## Alternatives and consequences

Process health alone is insufficient. An unrestricted watchdog agent is rejected. A large custom telemetry stack is deferred. Less default trace content improves privacy but may require explicit diagnostic sessions for difficult failures.

## Acceptance and reconsideration

Inject a stuck worker, repeated tool failure, stale lease, provider outage, unavailable approval channel and cost exhaustion. Verify bounded intervention and operator visibility. Plant test secrets and confirm redaction. Record restore evidence and residual gaps. Revisit observability tooling after the existing tools demonstrably fail required diagnostics.

Related: [ADR-002](002-existing-harness-and-template-baseline.md), [ADR-003](003-model-providers-and-cost-control.md), [ADR-005](005-state-ownership-and-durable-execution.md), [ADR-006](006-authorization-and-external-effects.md), [ADR-008](008-cloud-edge-and-hardware-extension.md), [ADR-010](010-mvp-scope-and-release-gates.md), [ADR-011](011-telegram-and-human-approval.md), [ADR-013](013-event-driven-superagent-coordination.md), [ADR-015](015-operational-baseline.md).
Sources: S01, S02, S11.
