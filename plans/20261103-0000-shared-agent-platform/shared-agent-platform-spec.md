# Shared agent platform: project memory and durable work across authenticated clients

Date: 2026-11-03 (Asia/Ho_Chi_Minh)  
Status: Conversation-derived specification; implementation and runtime qualification pending.  
Publication: [GitHub issue #1](https://github.com/caocuong2404/agents/issues/1). Triage role: ready-for-agent.  
Baseline: Owner-provided Shared Agent Platform ADRs v1.1, dated 2026-11-02.

## Problem Statement

Project decisions and useful context are fragmented across coding agents, web conversations, and bots. The owner switches between Devin CLI/Desktop/cloud sessions, Codex, ChatGPT Plus, Meta AI website/mobile, and a third-party Grok bot on Telegram or Discord. A decision established in one client is not reliably available in another fresh session, causing repeated explanations, repeated research, and inconsistent project direction.

The owner needs one shared service for project knowledge, with authenticated access and explicit project boundaries. The platform must also retain the already-designed paths for durable reasoning submissions and task delegation. Choosing a two-client demonstration must not reduce the eventual client scope.

The current application is a Cloudflare chat starter. It provides chat persistence and demo behavior, but does not implement the application's authority registry, shared Memory contracts, durable Task/Assignment contracts, or retained-data recovery guarantees. Documentation research verifies available building blocks; it does not establish that this application has passed a runtime gate.

## Solution

Provide one authenticated MCP/HTTP application service that existing clients can use to store and recall project decisions. Start with one owner workspace and explicit project scopes. Clients receive separate identities and grants; clients authorized for the same project use the same project knowledge, while their native conversations remain separate.

Use Cloudflare's existing runtime, Durable Object SQLite, state capabilities, context-provider patterns, Think submissions, and retained child-agent tools where they satisfy the ADR contracts. Preserve small application boundaries for alternate clients, harnesses, models, memory backends, and external executors.

The first useful delivery is shared project memory, validated with public or invented technical fixtures and designed for eventual retained daily use. Durable submissions and delegation follow their existing gates. Daily reliance requires selected operational policies and demonstrated recovery. Client qualification must identify real connection paths rather than assume that every consumer app or third-party bot accepts custom tools.

## User Stories

1. As the owner, I want an accepted project decision recorded once, so that I do not have to restate it in every agent conversation.
2. As the owner, I want a fresh session in another authorized client to recall that decision, so that changing tools preserves project continuity.
3. As the owner, I want all my named clients included in the integration target, so that the platform is useful across my actual working tools.
4. As the owner, I want to keep using existing client interfaces, so that shared memory does not require moving all work into a new chat product.
5. As the owner, I want one workspace organized into project scopes, so that knowledge is shared deliberately between projects and clients.
6. As the owner, I want each client registered separately, so that its permissions can be inspected and revoked independently.
7. As an authorized client, I want access only to granted projects and operations, so that another project's knowledge is not disclosed.
8. As an authorized client, I want current decisions returned with their sources and revisions, so that I can distinguish evidence from an unsupported recollection.
9. As an authorized client, I want to read permitted historical decisions, so that I can understand why a project changed direction.
10. As the owner, I want an updated decision to supersede an earlier version atomically, so that normal recall presents the current decision.
11. As the owner, I want conflicting edits detected, so that one client cannot silently overwrite another client's newer decision.
12. As an authorized client, I want safe retries of memory writes, so that a lost response does not create duplicate records.
13. As an authorized client, I want changed arguments under a reused request key rejected, so that retries cannot silently become different mutations.
14. As the owner, I want to forget a memory through a reversible tombstone, so that ordinary recall stops returning it while the required audit history is preserved.
15. As the owner, I want permanent deletion governed by an explicit retention policy, so that content, indexes, retained copies, and replay evidence are handled consistently.
16. As an authorized client, I want concise relevant recall, so that useful project context fits within the model's context budget.
17. As the owner, I want conversation provenance kept separate from application authority, so that a remembered instruction cannot grant access or change policy.
18. As the owner, I want visibility into what was stored and where it came from, so that inaccurate or obsolete knowledge can be corrected.
19. As the owner, I want control over whether conversations are ingested, so that raw transcript retention is an explicit product decision.
20. As the owner, I want public fixtures used in the first proof, so that integration and isolation can be evaluated before any broader data authorization.
21. As the owner, I want excluded sensitive content rejected before retention, so that an integration cannot expand the approved data boundary.
22. As the owner, I want a client qualification matrix, so that native tools, account restrictions, and unresolved connections are visible.
23. As the owner, I want unsupported client paths reported accurately, so that an unverified Meta AI or third-party bot connection is not presented as completed.
24. As a Cloudflare-hosted reasoning agent, I want a context provider connected to the shared project service, so that hosted reasoning uses the same authorized knowledge as external clients.
25. As the owner, I want long-lived knowledge separated from conversation history and temporary working notes, so that unrelated sessions do not become one oversized prompt.
26. As the owner, I want bounded status views, so that I can inspect activity without exposing unrelated project content or authority records.
27. As the owner, I want a reasoning request acknowledged after durable acceptance, so that work can continue after the submitting caller returns.
28. As a submitting client, I want repeated submissions deduplicated, so that transport retries do not start duplicate reasoning turns.
29. As the owner, I want durable status inspection, so that I can check accepted work from an authorized client.
30. As the owner, I want to steer an active task with a new accepted revision, so that later work follows the updated objective.
31. As the owner, I want cancellation to account for workers and in-flight effects, so that a cancellation receipt does not falsely imply rollback.
32. As a registered executor, I want a scoped assignment with its objective, acceptance criteria, context, capabilities, lease, and budget, so that I know what work is authorized.
33. As the owner, I want native Cloudflare child-agent delegation reused, so that retained runs and recovery do not require a new agent kernel.
34. As the owner, I want external executors connected through qualified adapters, so that a CLI or hosted agent can participate without owning canonical task state.
35. As the owner, I want an executor replaced after failure, so that accepted checkpoints and evidence survive a disposable runner.
36. As the owner, I want late results from obsolete assignments rejected, so that an old worker cannot overwrite current work.
37. As the owner, I want proposed results reviewed against current acceptance criteria, so that worker completion is not mistaken for owner acceptance.
38. As the owner, I want shared spending reservations and reconciliation before metered work, so that concurrent tasks remain bounded by approved policy.
39. As the owner, I want eligible external mutations bound to exact-intent approval, so that changing a target or arguments cannot reuse old consent.
40. As the owner, I want ambiguous external outcomes reconciled before retry, so that a lost receipt does not cause duplicate effects.
41. As the owner, I want the planned phone approval channel and authenticated fallback to preserve the same trusted approval state, so that channel changes do not change authority.
42. As the owner, I want a stop control reachable without a model turn, so that work can be fenced even if the reasoning agent is stuck.
43. As the operator, I want finite retention and tested recovery targets, so that the service can be relied on for daily project knowledge.
44. As the operator, I want versioned exports and inactive restore, so that migration or recovery preserves records and integrity without replaying work.
45. As the owner, I want restored state to preserve current revocations and invalidate old grants, so that rollback cannot revive access.
46. As the operator, I want bounded independent monitoring, so that a stalled agent cannot indefinitely retry or consume budget.
47. As the owner, I want provider and harness changes to preserve application identity and memory, so that a tooling change does not erase project continuity.
48. As the operator, I want reproducible package and template versions, so that current examples are qualified against the code actually deployed.
49. As the owner, I want evidence for each release gate, so that documented vendor features are not confused with tested platform behavior.
50. As the owner, I want later autonomy enabled only for a demonstrated authorized workload, so that optional orchestration does not delay the useful memory release.

## Implementation Decisions

### Confirmed scope and domain model

- Use the v1.1 ADR vocabulary: Principal, WorkspaceGrant, MemorySpace, Memory, Task, Assignment, ExecutorRegistration, BudgetReservation, Artifact, Operation, Approval, Event, and the four MutationFence variants. A Task owner is the trusted repository service; the owner is the enrolled human.
- Confirmed owner selections are project-decision continuity, all named clients in target scope, one owner workspace with project scopes, public fixtures for initial validation, and retained daily memory as the intended outcome.
- Later unanswered or aborted prompts did not choose retention durations, a backup boundary, RPO/RTO, automatic extraction, or a manual context-transfer fallback. Keep those decisions open.
- Modules comprise authenticated transport adapters, authority/grant services, a scoped memory repository, staged task/executor services, qualified native harness adapters, and operational recovery/admission controls. Keep the implementation a small Worker application with authoritative Durable Object stores rather than a service per logical module.

### Cloudflare building blocks and state ownership

- Reuse maintained Cloudflare examples and runtime capabilities before implementing equivalent machinery. Qualify the authenticated MCP pattern for initial ingress, Think durable submissions for hosted reasoning, and native retained agent tools for Cloudflare child delegation.
- Use synced State for small values intended for all authorized connections to that instance. Use dedicated SQL tables for large or relational records and data that must not be broadcast. Readonly connections restrict writes; their readable state must still respect the intended disclosure boundary.
- Keep external clients' native transcripts in their native systems. Cloudflare-hosted sessions own hosted conversation history and working context. Shared project memory is a scoped application repository accessed through typed services.
- Start authoritative project Memory records/catalog in scoped Durable Object SQLite as specified by ADR-012. Full-text search or other projections may support recall without becoming competing authorities. Do not adopt a custom semantic-memory engine as an MVP prerequisite.
- Apply Cloudflare Agent Memory's namespace/profile/session concepts as design inspiration. A project profile, if used, is shared by its permitted clients; a client conversation contributes a separate session identity. The server resolves allowed scopes before choosing a backend profile.
- Managed Agent Memory remains an optional backend candidate, subject to access, contract, retention, recovery, and cost qualification. Selecting it as authoritative storage would require an explicit ADR amendment. Extraction, synthesis, and a model-selected topic do not establish the authoritative accepted decision.
- Keep immutable/versioned policy and approved instructions separate from learned content. Context providers may expose read-only context, bounded working notes, or searchable knowledge, but retrieved content cannot update grants or approve actions.
- Preserve the distinction between memory task notes, durable Think turn submissions, and application Task/Assignment authority. They serve different responsibilities and must not be substituted for each other.

### Authenticated application contracts

- HTTP adapters and MCP tools invoke the same application services and enforce the same behavior. Prefer Cloudflare's current stateless MCP handler and supported OAuth integration, while retaining durable application records behind it.
- Verify identity, issuer/audience/client binding, credential validity, current principal enablement, action grants, and project resource constraints before reading protected content. Requested workspace/profile identifiers and guessed object IDs never grant access.
- Enroll clients individually and keep owner, client/bot, executor, inbound webhook, and internal service authority distinct. Do not reuse owner administrative credentials in clients or runners.
- Enforce operation scopes and WorkspaceGrant checks in trusted handlers. OAuth token validation and advertised scope metadata do not replace those checks.
- Expose authority.inspect for current permitted authority. Restrict control.configure and recovery controls to trusted owner/operator routes; ordinary model tools cannot administer policy.
- Every mutation uses the contract's server-issued fence and stable idempotency key. Check current authentication, authorization, and fence validity before replay lookup. Exact committed retries return the original result; changed canonical arguments return IDEMPOTENCY_CONFLICT; stale authority must not be revived by an old request key.
- Preserve explicit schema versions and safe structured errors, including AUTHN_REQUIRED, ACCESS_DENIED, STALE_FENCE, REVISION_CONFLICT, and IDEMPOTENCY_CONFLICT. Authorized stale-fence failures use the documented non-retryable recovery behavior without changing current state or disclosing unauthorized metadata.
- Deny new mutating work when the authority registry is unavailable or the relevant stop switch is active. Owner recovery/status permissions remain narrow and cannot initiate a new external effect.

### Memory behavior

- Provide memory.remember, memory.recall, memory.read, and memory.forget through the common authenticated interface.
- Store the Memory contract's workspace, content/reference, subject, source, verified producer, schema and record revisions, observation/validity metadata, classification, trust class, status, and version-bound supersession reference.
- memory.remember creates a CURRENT record. Replacing a decision requires the expected prior revision and supersession reference; the repository atomically supersedes the old record and creates the new one. An exact replay returns the same committed identity and revision.
- memory.recall returns permitted CURRENT records with provenance and bounded results. memory.read supports authorized exact and historical inspection. A synthesized search answer does not replace the exact underlying decision record.
- memory.forget creates a reversible TOMBSTONED result under revision/fence checks. Permanent deletion is an operator/retention operation that must account for linked content, indexes, backups, and admissible replay evidence.
- PUBLIC data remains project-scoped. AUTHORIZED_PROJECT data needs separate owner data/destination authorization. EXCLUDED_SENSITIVE content remains denied in the MVP. Trust labels describe verified provenance; model content cannot promote itself to owner or service authority.
- Gate 1 requires explicit Memory operations. Whether to add automatic conversation extraction later remains open. If selected, ingestion must use approved conversation batches and account for retained raw messages; compaction alone is not deletion.

### Durable reasoning and delegation

- Gate 2 introduces Task, Assignment, task-bound context.build, budgets, and applicable artifacts using the existing harness. Reuse Think's durable admission, inspection, and cancellation rather than rebuilding its conversation execution loop.
- Preserve application Task revisions, authority epochs, generations, guarded lifecycle transitions, acceptance criteria, and review outcomes outside replaceable executors. Native submission/run IDs are references, not substitutes for the application task ledger.
- Gate 3 qualifies replaceable external execution. Registered executors claim eligible assignments atomically and receive an ExecutionFence; heartbeat, checkpoint, report, and later operation proposals require current authority and a live lease.
- Prefer native HTTP/RPC/SDK paths; add a small adapter only for demonstrated semantic differences. Enrollment and declared capabilities can narrow authority but cannot create new grants.
- Reuse retained Cloudflare child-agent tools for native children. Treat external Devin or CLI execution as separately qualified adapters; native child tooling does not establish cross-vendor execution compatibility.
- Keep one retry/timer owner for a step. Durable completion callbacks and external effect handling must tolerate repeated delivery. Preserve evidence and reconcile uncertainty rather than claim universal exactly-once execution or native-process continuation.
- Gate 4 introduces the trusted effect broker and exact-intent Approval/Operation lifecycle for one owner-selected test fixture. Telegram private-DM approval and authenticated fallback follow ADR-011. Tier 2 remains disabled.
- Gate 5 covers event-driven Router coordination and bounded autonomy only after an actual permitted workload, numerical criteria, and prior gates are qualified. The logical Router does not become a universal writable memory or unrestricted executor.

### Release gates and retained-data reliance

| Gate | Required delivery and qualification                                                                                                                                                      |
| ---- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 0    | Reproducible official scaffold/package baseline; identity/bootstrap design; applicable data, authority, operational, dependency, and license review.                                     |
| 1    | Memory-only proof with two independently authenticated clients and at least two resource scopes; provenance, guarded writes, replay, isolation, tombstones, and inactive memory restore. |
| 2    | Qualified existing reasoning runtime with durable tasks, assignments, context, and bounded accounting.                                                                                   |
| 3    | Qualified isolated external-executor replacement, lease/fence behavior, checkpoints, and artifact integrity.                                                                             |
| 4    | Exact-intent human approval and reconciliation for one selected reversible test mutation.                                                                                                |
| 5    | Bounded Router/autonomy and combined operational recovery for a selected workload.                                                                                                       |

- Gate 1 creates no Task, Assignment, Approval, Operation, execution scheduler, model runtime, or task-bound context.build. Later gates are additive; inactive records must not be fabricated to satisfy an earlier proof.
- A two-client demonstration proves the shared contract; it does not narrow the target portfolio. Track each named client's actual surface, version/plan, authentication path, project grants, read/write behavior, and qualification result.
- Before retained daily reliance, select finite retention/deletion policy, replay horizons, backup/recovery destinations and cadence, RPO/RTO, current revocation recovery, secret procedure, and ingress/admission limits; then demonstrate the applicable restore drill.
- Restore into an inactive environment, validate manifests/digests and current authority, establish a fresh authority epoch outside the restored snapshot, invalidate old fences/affected credentials, and reopen explicitly after verification.
- Existing numerical spend targets remain provisional engineering defaults rather than new spending permission or mathematical billing guarantees. Reserve/reconcile metered work when its gate is enabled; keep raw prompt/tool traces disabled by default.

## Testing Decisions

- **Primary test seam:** the externally observable authenticated application-call boundary. Exercise real MCP tool calls and HTTP requests against the same service with real persistence. The owner confirmed this MCP/HTTP seam during the setup draft review.
- Prefer one reusable behavioral conformance suite at that boundary. Do not assert private table layouts, implementation call counts, prompt wording, or SDK internals. Add only a narrow native adapter test when an observable guarantee cannot be exercised through the common boundary.
- Modules under test are authority/grants, scoped memory, transport adapters, recovery/admission, and, at their gates, Task/Assignment services and native/external executor adapters.
- **Prior art:** the starter has no application test suite or test script. Existing CI checks formatting, lint, types, and security scanning. Treat the v1.1 contract invariants and validation gates as requirements; Cloudflare examples are integration references rather than existing application test coverage.
- **Core fixture:** two projects, independently enrolled clients, and a public decision with known content/source/revision. One client writes it; another fresh authorized client recalls/reads it without importing the first client's conversation. Prove denied access to the second project and forged object IDs without content or existence disclosure.
- **Memory lifecycle:** prove current-only recall, authorized history, revision-guarded replacement, concurrent-edit conflict, superseded-record behavior, reversible forget, exact replay, same-key/changed-arguments conflict, and replay rejection after revocation or restore.
- **Authority:** prove invalid/unmapped identity, wrong audience/client, absent grant, revoked credentials, stop switch, unavailable authority, spoofed producer/trust, and memory-borne policy instructions cannot broaden access.
- **Transport equivalence:** assert equivalent domain outcomes and safe errors through MCP and HTTP, with protocol-specific envelopes allowed. Include bounded payloads/results and policy-selected admission limits.
- **Recovery:** export permitted records, interrupt/restart the service, restore inactive, verify exact records and referenced bytes/digests, preserve current revocation decisions, and reject pre-restore fences. Measure loss/recovery time against the owner-selected values.
- **Hosted proof:** native SQLite point-in-time recovery requires an approved hosted permitted-data drill; local development cannot establish its guarantees. Use a disposable namespace and approved test identities for hosted tests. Never use production secrets or real financial/external effects as fixtures.
- **Runtime and delegation gates:** exercise lost submission responses, duplicate admission, restart, steering/cancellation, executor failure/replacement, stale reports/checkpoints, lease expiry, and current-evidence review. Gate-inactive operation routes receive negative contract tests without enabling effect adapters.
- **Later effects:** after fixture selection, test expired/replayed/changed-intent approvals, concurrent decisions, crash around dispatch/receipt, and UNKNOWN reconciliation without blind retries.
- **Client qualification:** test actual client authentication and permitted read/write behavior. Public documentation support alone does not pass a client gate; Meta AI consumer access and the third-party Grok bot remain explicit unresolved cases.
- **Meaningful evidence:** retain fixture identities, package/scaffold versions, observed results, failure/recovery evidence, latency/cost measurements where applicable, and remaining gaps. No universal success percentage, latency SLO, RPO/RTO, or throughput number was selected in the discussion.
- Retention tests must cover all applicable record/copy families once policy values are selected. Conversation ingestion, if later enabled, needs additional raw-message deletion, extraction provenance, and freshness tests. Keep provisional test settings distinct from approved operational policy.

## Out of Scope

- A new agent kernel, universal model/provider abstraction, replacement of every client's native transcript, or one global prompt containing every project's history.
- A mandatory custom semantic/graph-memory engine, dynamic plugin marketplace, automatic installation of retrieved tools, or unreviewed dependency upgrades.
- Implicit browser automation, credential sharing, scraping private conversations, or a presumed manual bridge for closed clients. Those connection approaches were not selected.
- Claiming every named consumer interface is integrated before its actual supported path is demonstrated. The all-client requirement remains an integration target rather than a vendor capability assumption.
- Automatic transcript collection or extraction as a committed first-release requirement.
- Tier-2 production changes, destructive operations, financial commitments, secret rotation by agents, privileged infrastructure changes, or physical actuation.
- Hardware, robotics, digital twins, and the unresolved “twin-flame” workstream; these retain a separate future decision and gate.
- Unrestricted proactive autonomy, active-active multi-cloud state, an always-on local runner, or guarantees of portable native process/token-cache recovery.
- Treating this specification, native framework features, provisional budgets, or a ready-for-agent label as authorization to provision, deploy, spend, or enable external actions.

## Further Notes

**Decisions still open:** preserve these rather than convert recommendations into consent.

| Decision                                | Current position                                                                                                                                                                                                |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| OD-01: owner/recovery identity          | Login provider, verified subject/issuer/audience/client mapping, and bootstrap/recovery identities remain unset.                                                                                                |
| OD-02: principal/grant inventory        | One owner workspace with project scopes is selected; actual clients, identities, action grants, selectors, and executor registrations remain to be configured.                                                  |
| OD-03: credentials and execution timing | Lifetimes, rotation/overlap, pairing lifetime, leases/deadlines, retries, and backoff values remain unset for applicable gates.                                                                                 |
| OD-04: retention and replay             | No content lifetime, transcript policy, dedupe/tombstone window, replay horizon, or copy-deletion schedule was selected.                                                                                        |
| OD-05: recovery                         | No backup boundary/destination, cadence, RPO/RTO, or non-rollback revocation strategy was selected.                                                                                                             |
| OD-06: operator/secret procedure        | Actual enrollment, recovery access, reviewed deployment, consumer verification, and upstream revocation procedure remain to be configured.                                                                      |
| OD-07: ingress/admission                | Rate windows, payload bounds, concurrency, backlog, and protected stop capacity remain unset.                                                                                                                   |
| OD-08: Tier-1 fixture                   | No effect fixture/destination is selected; Gate 4 remains disabled until selection and qualification.                                                                                                           |
| OD-09: phone approval expiry            | The ADR's provisional 15-minute default is retained; overnight calibration was not selected.                                                                                                                    |
| OD-10: budget calibration               | Retain provisional USD 15/month infrastructure, USD 20/month and USD 3/day inference, USD 0.50 warning and USD 1.00 task stop as engineering targets; paid use and calibration are not authorized by this spec. |
| OD-11: permitted data                   | Public or invented technical fixtures are selected first; exact fixtures/destinations and any later non-sensitive personal-project data require explicit configuration.                                         |
| OD-12: runtime/workload                 | Think is the accepted default scaffold; actual model, source/package pins, workload, and quantitative qualification criteria remain to be chosen and tested.                                                    |
| OD-13: hardware                         | Deferred; no device integration or physical authority is selected.                                                                                                                                              |
| OD-14: stronger outage recovery         | Additional independent observation/recovery requirements remain open; a Cloudflare-hosted monitor shares Cloudflare's failure domain.                                                                           |

**Confirmed client inventory:** Devin CLI, Devin Desktop, Devin cloud/web sessions, Codex, ChatGPT Plus web, Meta AI website/mobile, and a third-party Grok bot used on Telegram or Discord. The owner does not control the Grok bot's code/configuration. Exact Codex surface, bot identity, account entitlements, and supported connection settings require qualification. No manual-bridge choice was recorded.

**Source and compatibility evidence:** the owner supplied the v1.1 ADR bundle; all 27 payload checksums matched. The [repository ADR index](https://github.com/caocuong2404/agents/blob/main/docs/adr/README.md) now organizes the 15 records with their original IDs/statuses, [contracts](https://github.com/caocuong2404/agents/blob/main/docs/adr/contracts.md), [validation gates](https://github.com/caocuong2404/agents/blob/main/docs/adr/validation.md), and [source catalog](https://github.com/caocuong2404/agents/blob/main/docs/adr/references.md). [Source provenance](https://github.com/caocuong2404/agents/blob/main/docs/adr/source-provenance.md) records the import boundary and historical archive digest; [project decisions](https://github.com/caocuong2404/agents/blob/main/docs/adr/project-decisions.md) distinguishes later owner choices from the original open register. Static archive checks do not establish runtime readiness.

Brave research and direct primary-source/package checks established the following reference basis:

- [Cloudflare State](https://developers.cloudflare.com/agents/runtime/capabilities/state/) and [Agent state facade](https://developers.cloudflare.com/agents/agent-class/state/) describe durable JSON state and synchronization.
- [Conversation state and memory](https://developers.cloudflare.com/agents/concepts/conversation-state-and-memory/) and [runtime Sessions](https://developers.cloudflare.com/agents/runtime/capabilities/sessions/) describe different documented API generations; qualify exact exports rather than mixing examples.
- [Think submissions](https://developers.cloudflare.com/agents/harnesses/think/programmatic-submissions/) and [retained agent tools](https://developers.cloudflare.com/agents/agent-class/agent-tools/) provide native execution building blocks without replacing application authority.
- [MCP handler](https://developers.cloudflare.com/agents/model-context-protocol/apis/handler-api/) and [MCP security](https://developers.cloudflare.com/agents/model-context-protocol/guides/securing-mcp-server/) provide authenticated transport patterns; handlers still enforce operation scopes.
- [Agent Memory isolation](https://developers.cloudflare.com/agent-memory/concepts/namespaces-profiles/) and [ingestion behavior](https://developers.cloudflare.com/agent-memory/concepts/how-agent-memory-works/) inform shared scope and capture design. [The product overview](https://developers.cloudflare.com/agent-memory/) currently marks the managed service private beta. Published documentation does not establish all retention/export/restore guarantees required here.
- Published [Agents 0.32.0](https://registry.npmjs.org/agents/0.32.0) and [Think 0.23.0](https://registry.npmjs.org/@cloudflare%2Fthink/0.23.0) were verified candidates, not selected deployment pins. Think 0.23.0 declares Agents >=0.28.0 <1 and AI SDK 7 peers. The starter currently locks Agents 0.17.4 and AI SDK 6, with no Think dependency. Qualify a compatible package set before adopting current examples.

**Issue-tracker configuration:** specs and tickets use GitHub Issues in caocuong2404/agents with the canonical triage labels. Publication follows the configured tracker workflow.
