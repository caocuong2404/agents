# Cloudflare architecture reference guide

Research checked: 2026-11-02/03 with Brave Search, live official pages, official releases, and published npm metadata. This is a reading guide and compatibility snapshot, not an additional accepted ADR or a runtime qualification result.

## Reading order and ADR links

| Responsibility           | Primary references                                                                                                                                                                                                                                                                                                                      | Related ADRs and application boundary                                                                                                                                                                          |
| ------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Durable state            | [Agent state facade](https://developers.cloudflare.com/agents/agent-class/state/), [State capability](https://developers.cloudflare.com/agents/runtime/capabilities/state/), [runtime status](https://developers.cloudflare.com/agents/runtime/)                                                                                        | [ADR-005](005-state-ownership-and-durable-execution.md): small synchronized state and dedicated authoritative record tables.                                                                                   |
| Conversation and context | [Conversation state and memory](https://developers.cloudflare.com/agents/concepts/conversation-state-and-memory/), [Sessions](https://developers.cloudflare.com/agents/runtime/capabilities/sessions/), [Think configuration](https://developers.cloudflare.com/agents/harnesses/think/configuration/)                                  | [ADR-002](002-existing-harness-and-template-baseline.md), [ADR-012](012-shared-memory-and-context-service.md): native histories stay separate; a shared context provider resolves permitted project knowledge. |
| Authenticated ingress    | [MCP handler](https://developers.cloudflare.com/agents/model-context-protocol/apis/handler-api/), [authorization](https://developers.cloudflare.com/agents/model-context-protocol/protocol/authorization/), [security checks](https://developers.cloudflare.com/agents/model-context-protocol/guides/securing-mcp-server/)              | [ADR-006](006-authorization-and-external-effects.md), [ADR-014](014-identity-and-authorization.md): OAuth identity and explicit application grants are both checked.                                           |
| Durable reasoning        | [Think submissions](https://developers.cloudflare.com/agents/harnesses/think/programmatic-submissions/), [turn API selection](https://developers.cloudflare.com/agents/harnesses/think/#choose-a-turn-api)                                                                                                                              | [ADR-002](002-existing-harness-and-template-baseline.md), [ADR-005](005-state-ownership-and-durable-execution.md): reuse durable turn admission without replacing Task authority.                              |
| Child delegation         | [Retained agent tools](https://developers.cloudflare.com/agents/agent-class/agent-tools/), [sub-agent RPC](https://developers.cloudflare.com/agents/harnesses/think/sub-agents/)                                                                                                                                                        | [ADR-004](004-executor-contracts-and-portability.md), [ADR-013](013-event-driven-superagent-coordination.md): native children and qualified external-executor adapters have distinct boundaries.               |
| Long-term memory         | [Agent Memory](https://developers.cloudflare.com/agent-memory/), [profiles](https://developers.cloudflare.com/agent-memory/concepts/namespaces-profiles/), [ingestion](https://developers.cloudflare.com/agent-memory/concepts/how-agent-memory-works/), [Workers API](https://developers.cloudflare.com/agent-memory/api/workers-api/) | [ADR-012](012-shared-memory-and-context-service.md), [ADR-015](015-operational-baseline.md): scope and capture inspiration; managed-backend adoption and operations remain unqualified.                        |

The original source catalog keeps [S01–S26](references.md) and their provenance. This guide adds current reading order; it does not renumber or silently replace those historical references.

## Responsibilities to preserve

- State synchronization reaches connections to an Agent instance. Expose only data those connections may read; keep project access checks and protected records in trusted services.
- Conversation history, writable working context, shared Memory, and durable Task state are distinct. A custom context provider can reach the shared service while each client retains its native session.
- MCP handlers can be stateless while application data remains durable. Current handler guidance uses OAuthResourceServer for token validation; the security guide says handlers must enforce operation scopes.
- Think submissions provide durable admission and retry/status/cancellation behavior. Application Task revisions, grants, acceptance criteria, budgets, and external-effect reconciliation retain their ADR ownership.
- Retained native child runs do not establish compatibility with an arbitrary external CLI or web session. Qualify each adapter and client surface.
- Agent Memory ingestion retains raw conversations and performs extraction. Choosing it would require the applicable data/retention policy; no automatic-capture choice has been recorded.
- Managed Agent Memory is documented as private beta. Its documentation does not establish every export/retention/restore guarantee required by the platform.

## Published compatibility snapshot

| Item                                                                           | Verified snapshot                                                    | Meaning                                                                           |
| ------------------------------------------------------------------------------ | -------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| Repository baseline                                                            | Agents 0.17.4, AI SDK 6.0.233, no Think dependency.                  | Existing starter; not a qualified host for every current example.                 |
| [Agents 0.17.4 manifest](https://registry.npmjs.org/agents/0.17.4)             | Includes the older experimental memory/session API.                  | An older import generation, not an alias for every newer API.                     |
| [Agents 0.32.0 manifest](https://registry.npmjs.org/agents/0.32.0)             | Published state, sessions, agent-tools, and MCP-server exports.      | A verified candidate, not a selected upgrade.                                     |
| [Think 0.23.0 manifest](https://registry.npmjs.org/@cloudflare%2Fthink/0.23.0) | Requires Agents >=0.28.0 <1, AI SDK 7, and compatible peer packages. | Qualify a coherent package set; the current starter is outside those peer ranges. |

Official release evidence: [Agents 0.32.0](https://github.com/cloudflare/agents/releases/tag/agents%400.32.0), [Think 0.23.0](https://github.com/cloudflare/agents/releases/tag/%40cloudflare/think%400.23.0).

Cloudflare lists State as stable and Sessions as experimental. The concept page, runtime reference, and Think examples contain different Session import generations. Resolve the published package exports and exact adopted example commit during Gate 0; copying mixed examples is not compatibility evidence.

## Client qualification references

- [Codex MCP](https://learn.chatgpt.com/docs/extend/mcp) documents a native connection path.
- [Devin CLI MCP](https://docs.devin.ai/cli/extensibility/mcp/configuration), [Desktop customization](https://docs.devin.ai/desktop/devin-local/customizations), and [cloud MCP](https://docs.devin.ai/work-with-devin/mcp) require surface-specific configuration and authentication.
- [ChatGPT custom MCP](https://developers.openai.com/api/docs/guides/custom-mcp-server) documents a product path; the owner's actual Plus entitlement and enabled UI remain to be checked.
- No verified native connection was established for Meta AI's consumer app or the owner's third-party Grok bot. Keep these explicit qualification items in the [project register](project-decisions.md).

## Qualification order

Follow [validation gates](validation.md): pin a reproducible supported scaffold; prove authenticated shared memory first; then qualify existing reasoning and external replacement; enable approval/effects and bounded autonomy only at their applicable gates. Keep [contracts](contracts.md), current authority, and [owner choices](project-decisions.md) as the application requirements.
