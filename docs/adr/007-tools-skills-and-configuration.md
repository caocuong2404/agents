# ADR-007 — Use typed tools and version-controlled skills with provenance

Date: 2026-11-02  
Status: Accepted  
Supersedes: ADR-007 in baseline v0.3

## Context

The project will reuse provider CLIs, skills, MCP servers and template tools. These assets can change behavior without changing the model. Retrieved documents and memory may contain text that looks like instructions. Uncontrolled dynamic extensions would make execution difficult to reproduce and authorize.

## Decision

Expose application functions through typed, validated tool schemas. HTTP and MCP are transport options for the same application authorization rules. Keep core business logic independent of a particular chat UI or transport.

Version-control skills, system instructions, tool definitions, policy configuration and adapter configuration. Record their versions or digests on assignments and results. Pin executable dependencies and template commits in the implementation lockfile. Review changes as code. Revalidate changed tool semantics and supported provider capabilities before upgrade.

Use existing supported skill-loading and MCP facilities. Start with a fixed set of reviewed integrations. Do not build a dynamic plugin marketplace, auto-install tools from chat, or execute retrieved instructions as trusted configuration.

Separate configuration by authority:

| Material                                         | Who can change it?                                      | Treatment                                                    |
| ------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ |
| Authorization policy and capability mapping      | Authenticated operator through controlled configuration | Enforced by trusted application code                         |
| System identity and approved skills              | Versioned operator configuration                        | Read-only to the execution model unless explicitly delegated |
| Learned facts and working notes                  | Authorized agents within their scope                    | Evidence with provenance; never an access grant              |
| External messages, repositories and tool results | External sources                                        | Untrusted input, even when useful                            |

Tool capability changes require policy review; changing a prompt alone cannot unlock an administrative action. Worker-owned notes can suggest a configuration change but cannot apply it.

## Alternatives and consequences

Free-form privileged command execution and automatic community-plugin installation are rejected for the initial release. Typed tools require some per-integration code but make validation, audit and cross-harness reuse practical. Reusing an existing schema is preferred to inventing a new one if the semantics match.

## Acceptance and reconsideration

Validate tool inputs and outputs, unknown fields where security-relevant, invalid capability requests, changed skill provenance, dependency upgrades and injected instructions in memory/tool output. Add dynamic loading only when reviewed static integration becomes a measured bottleneck and a supply-chain policy exists.

Related: [ADR-002](002-existing-harness-and-template-baseline.md), [ADR-004](004-executor-contracts-and-portability.md), [ADR-006](006-authorization-and-external-effects.md), [ADR-010](010-mvp-scope-and-release-gates.md), [ADR-012](012-shared-memory-and-context-service.md), [ADR-014](014-identity-and-authorization.md).
Sources: S02, S03, S06.
