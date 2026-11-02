# ADR-003 — Separate reasoning providers from coordination and budget authority

Date: 2026-11-02  
Status: Accepted; numerical caps are retained provisional defaults  
Supersedes: ADR-003 and cost-control guidance in baseline v0.3

## Context

The owner has multiple model subscriptions and provider-specific CLIs. A cloud Router and a CLI worker have different inference and credential requirements. Model quality and provider behavior vary by task. Neither a subscription nor a gateway automatically establishes a unified spending or authorization boundary.

## Decision

Keep task identity, application memory, policy and receipts independent of a selected model. Use existing provider integrations offered by the adopted harness. Preserve provider-specific tool, reasoning and streaming capabilities; do not force all providers into the lowest common feature set.

Start with one qualified default model for coordination. Select an execution model through explicit task capability requirements. Automated fallback may change the provider only if its data classification, credentials, capabilities and remaining budget permit it. Unknown model/tool outcomes must not silently produce an external action.

Use AI Gateway where the chosen model integration is supported and useful for metering, routing or limits. It is optional infrastructure, not the agent's brain, and not the sole budget authority. CLI subscription traffic is not assumed to pass through it.

Consumer/CLI subscriptions and metered APIs are distinct integration paths. Verify each provider's supported authentication and unattended-use conditions when configuring the adapter. Store subscription credentials outside disposable workspaces where supported; do not copy personal login profiles into arbitrary runners. An adapter that cannot separate required credentials from untrusted execution is restricted to an approved lower-trust workload or replaced.

Retain these engineering targets from v0.3:

- Hosted infrastructure: USD 15/month.
- Metered inference: USD 20/month and USD 3/day.
- Estimated task spend: warning at USD 0.50; stop scheduling additional work at USD 1.00.

These figures do not authorize paid use. Prefer free-tier operation where feasible. Before expensive work, atomically reserve an estimate in application state and reconcile actual usage. Stop new work when policy limits are reached; keep already-incurred charges visible. Gateway spend counters and external billing can lag, so concurrency and uncertain charges prevent a strict mathematical combined cap. Infrastructure requires separate controls.

## Alternatives and consequences

One mandatory provider would simplify integration but reduce flexibility. A custom universal provider layer is deferred. Routing diversity increases evaluation and accounting work, so add providers for demonstrated value rather than coverage.

## Acceptance and reconsideration

Prove denied provider/data combinations, budget reservation under concurrent tasks, usage reconciliation, fallback failure behavior and credential redaction. Revisit model choice after representative quality/cost measurements or a supported API change.

Related: [ADR-002](002-existing-harness-and-template-baseline.md), [ADR-006](006-authorization-and-external-effects.md), [ADR-009](009-observability-evaluation-and-recovery.md), [ADR-010](010-mvp-scope-and-release-gates.md), [ADR-015](015-operational-baseline.md).
Sources: S02, S10, S11.
