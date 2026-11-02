# ADR-001 — Build a shared platform by adapting existing products

Date: 2026-11-02  
Status: Accepted  
Supersedes: ADR-001 in baseline v0.3

## Context

Existing agent products already provide teams, tool loops, model integrations and execution environments. The project needs continuity across those products, durable work, controlled external actions and future hardware integration. Recreating their capabilities would increase maintenance and slow adoption of better models and runtimes.

## Decision

Use a shared application core for identity, scoped memory, task state, policy, approvals, evidence and observability. Prefer maintained products and official templates for the implementation. Write application logic and narrow adapters where an actual integration gap exists.

Cloudflare is the preferred host for authenticated ingress, durable coordination and shared application services. This is not a requirement to run all model inference, POSIX commands or hardware software in Workers. Execution location follows workload and trust requirements.

Keep three logical layers:

1. Shared core: consistent application records and coordination.
2. Integration adapters: translation to an existing product or device interface.
3. Domain execution: the harness, sandbox, service or local controller that performs the work.

Separate shared interfaces from shared access. Two clients using the same service do not automatically share data, credentials or authority. Preserve independent permission domains for personal work, each project and any separately authorized organizational data.

Use manual chat and existing CLI tools as early reasoning clients. Introduce an autonomous cloud Router only after shared state and execution contracts work. Hardware support is an extension boundary, not an initial implementation commitment.

## Alternatives and consequences

### Alternatives considered

- A new universal agent OS/kernel: rejected for the initial release; high maintenance and duplication.
- One existing agent product owns all data and execution: simple initially, but does not satisfy shared access and executor replacement goals.
- A fully decentralized swarm: deferred; no demonstrated need offsets the coordination and diagnostic cost.
- Other cloud providers: remain viable executor or future hosting options; not implemented in parallel by default.

### Consequences

Some application-specific work remains, especially authorization, entity IDs and task/effect records. Product APIs may change. Central cloud dependencies still create availability boundaries; this decision provides replaceability and recovery, not independent survival of a Cloudflare outage.

## Acceptance and reconsideration

Accept the first integration when two authorized clients can access the same permitted task context, an executor can be replaced, and no unsupported access crosses scopes. Reconsider hosting or a product only when workload evidence, access requirements, cost or maintenance demonstrates a concrete limitation.

Related: [ADR-002](002-existing-harness-and-template-baseline.md), [ADR-004](004-executor-contracts-and-portability.md), [ADR-008](008-cloud-edge-and-hardware-extension.md), [ADR-010](010-mvp-scope-and-release-gates.md), [ADR-012](012-shared-memory-and-context-service.md), [ADR-013](013-event-driven-superagent-coordination.md), [ADR-014](014-identity-and-authorization.md), [ADR-015](015-operational-baseline.md).
Sources: S01, S02, S03.
