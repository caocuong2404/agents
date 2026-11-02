# ADR-012 — Share scoped application memory across existing harnesses

Date: 2026-11-02  
Status: Accepted  
Adds: An explicit shared-memory decision; updates v0.3's session-only starting point  
Amends: ADR-012 in v1.0  
Change note: SUBSTANTIVE — Require idempotent guarded Memory writes, close trust/classification handling, and defer task-bound context to Gate 2 [D4, D6, D13].

## Context

The first release should let existing chat/CLI/bot clients retain useful context when an executor or harness changes. A vector database, a transcript and a reliable task ledger solve different problems. A shared service must not imply one global readable memory pool.

## Decision

Expose authenticated memory and task-context functions through HTTP and MCP. Keep application contracts independent of a chosen reasoning harness. Server-side identity determines permitted scopes; caller-provided tags do not authorize access.

Separate four classes:

| Class                | Authority and handling                                                            |
| -------------------- | --------------------------------------------------------------------------------- |
| Task/operation state | Structured, versioned authoritative records; direct reads for current status      |
| Learned knowledge    | Scoped facts/notes with source and validity metadata; useful evidence, not policy |
| Conversation history | Native durable harness transcript with task links                                 |
| Artifacts            | Retained files referenced by metadata and integrity digest                        |

Initially store explicit memory records and a searchable catalog in scoped Durable Object SQLite. Reuse existing harness context/session features for its own conversation. Add FTS when keyword retrieval is needed and Vectorize only after semantic recall provides measured benefit. Treat indexes, embeddings and model summaries as rebuildable projections. D1 is optional for cross-scope reporting; it is not a required second truth store.

Each memory record identifies workspace/scope, source, producer, schema version, record revision and relevant observation/validity times. Support supersession, export and authorized deletion. Preserve enough provenance to distinguish a human decision from a model inference or a quoted external message. Permission policies and credentials are not ordinary writable memory.

`context.build(task_id, token_budget)` first resolves authorized current task state, then approved constraints, relevant evidence and selected memory. Return compact content plus IDs/references for detail. Keep untrusted source content identifiable and subject to tool authorization even when included in context. A summary must not silently replace current acceptance criteria or authorization state.

Avoid a custom extraction pipeline in the first release. Explicit writes from existing clients establish usefulness first. Automatic extraction, contradiction handling and longer-term consolidation can be added through an evaluated existing engine. Cloudflare's separate Agent Memory product is currently private beta and is not a required dependency. Experimental Session/Think APIs remain behind the application boundary.

### v1.1 memory and activation amendment

memory.remember and memory.forget require the current scope/record fence and an idempotency_key. Same key and canonical logical arguments return the original record result after authority checks; different arguments are IDEMPOTENCY_CONFLICT. MemoryStatus, Classification and TrustClass are closed sets in CONTRACTS. Provenance never grants a policy write or permits an excluded data class.

Gate 1 is memory-only: memory.remember/recall/read/forget plus current authorization and inactive restore. Task-bound context.build and Task/Assignment records are Active from Gate 2. Existing clients can retrieve permitted Memory with provenance without creating Task machinery. ADR-014 specifies subject/workspace mapping and model-read-only policy; ADR-015 specifies finite retention and recovery prerequisites.

## Alternatives and consequences

One undifferentiated global memory, vector-only task state and sandbox-local authoritative memory are rejected. A mature external memory service remains an option if integration effort and data constraints justify it. Owning a small stable record/API contract adds modest application work but preserves interchangeability.

## Acceptance and reconsideration

Two allowed clients can recall the same scoped facts; an unauthorized client cannot. A replacement executor reconstructs current task context without receiving unrelated memory. Test supersession, stale summary handling, provenance, export/import, deletion policy and restore. Introduce a semantic engine only against a representative recall evaluation.

Related: [ADR-001](001-platform-scope-and-reuse.md), [ADR-002](002-existing-harness-and-template-baseline.md), [ADR-004](004-executor-contracts-and-portability.md), [ADR-005](005-state-ownership-and-durable-execution.md), [ADR-006](006-authorization-and-external-effects.md), [ADR-007](007-tools-skills-and-configuration.md), [ADR-010](010-mvp-scope-and-release-gates.md), [ADR-013](013-event-driven-superagent-coordination.md), [ADR-014](014-identity-and-authorization.md), [ADR-015](015-operational-baseline.md).
Sources: S04, S06–S09, S20.
