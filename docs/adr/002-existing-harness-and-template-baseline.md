# ADR-002 — Reuse an existing harness and qualify templates before adoption

Date: 2026-11-02  
Status: Accepted principle and initial scaffold; runtime qualification pending  
Supersedes: ADR-002 and the Pi-first trial order in baseline v0.3

## Context

Durable turns, model streaming, history, steering and recovery are substantial runtime responsibilities. Existing Cloudflare examples address much of this. Repository popularity and an official maintainer do not prove that a template meets this project's authorization or recovery requirements.

## Decision

Start the memory-only service from the official authenticated MCP pattern. When cloud-hosted coordination is added, use Cloudflare Think durable submissions as the default scaffold. It demonstrates durable acceptance, an idempotency key, status inspection and cancellation. Add persistent context through the supported Think context facilities. Keep PiHarness as an alternative if qualification shows lower integration effort or better required behavior.

| Building block               | Initial reference                                     | Qualification boundary                                         |
| ---------------------------- | ----------------------------------------------------- | -------------------------------------------------------------- |
| Authenticated client ingress | `cloudflare/agents/examples/mcp-worker-authenticated` | Real identity binding, resource authorization and scope checks |
| Coordinator turns            | `cloudflare/agents/examples/think-submissions`        | Durable intake, steering, cancellation, recovery and cost      |
| Context/memory               | Think context and session documentation               | Persistence, extraction quality and cross-client access        |
| Multi-step waits             | `cloudflare/agents/examples/think-workflows`          | Retry ownership, durable approval and effect reconciliation    |
| Optional interactive UI      | `cloudflare/agents-starter`                           | Authentication and removal of demo behavior                    |

Treat these as source examples. Remove demonstration tools and scheduled prompts that are not needed. Replace wildcard dependencies with a tested lockfile and exact release choices before deployment. Record the adopted commit, dependency versions, compatibility date and configuration in the implementation change. Do not infer validated version pins from this ADR's date.

Think and Pi Durable include experimental surfaces. Isolate application records from native transcript formats and run contract/recovery tests before upgrades. Do not implement a generic `HarnessPort` merely to hide every difference between them.

Community memory services remain optional alternatives. doobidoo's service has a Python runtime with Cloudflare storage support; Supermemory's self-hosted server is not fully represented by its public OSS components; Graphiti adds a graph database. None is a mandatory dependency of the small Cloudflare-native release.

## Alternatives and consequences

A custom harness is deferred. A fully managed memory vendor could reduce setup but adds vendor/data-handling constraints. Native examples reduce initial code while leaving application authorization, external-worker ownership and operations to this project.

## Acceptance and reconsideration

Run equivalent representative tasks with the candidate runtime. Evaluate durable admission, repeated submissions, restart mid-turn, steering, tool replay, integration effort and cost. Ship the simplest successful option. Record failures and a replacement ADR if the default changes.

Related: [ADR-001](001-platform-scope-and-reuse.md), [ADR-003](003-model-providers-and-cost-control.md), [ADR-005](005-state-ownership-and-durable-execution.md), [ADR-007](007-tools-skills-and-configuration.md), [ADR-009](009-observability-evaluation-and-recovery.md), [ADR-010](010-mvp-scope-and-release-gates.md), [ADR-012](012-shared-memory-and-context-service.md).
Sources: S02–S09.
