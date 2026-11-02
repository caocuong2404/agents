# ADR-015 — Make operational configuration a prerequisite to retained-data reliance

Date: 2026-11-02  
Status: Accepted; operational values and recovery selection remain open  
Adds: Operational prerequisites absent from v1.0  
Amends: ADR-005, ADR-006, ADR-009, ADR-010, ADR-011, ADR-012, ADR-014  
Change note: SUBSTANTIVE — require explicit retention/replay policy, recoverable manifests, secret lifecycle controls and ingress limits before applicable release gates [D7].

## Context

The architecture has no deployment. “Accepted” establishes implementation requirements; it does not supply operational settings or evidence. Retention, recoverability, credential handling and admission limits cannot be inferred from a successful template build. Existing products and bindings are sufficient for this decision; no new product is selected.

## Decision

### Retention and replay

**OPEN: owner decision — OD-04.** Choose a finite policy with actual values for Memory and superseded/tombstoned content; Tasks/Assignments/checkpoints; Operation/Approval/receipt and idempotency evidence; Event/webhook dedupe records; retained artifacts; redacted logs/optional diagnostic traces; exports/backups and deletion completion. Configure maximum accepted retry/replay age with dedupe retention. Options are (A) one finite window for all permitted test records, simpler; (B) shorter content/trace windows and longer minimum necessary decision/receipt/dedupe metadata, recommended; (C) owner-managed export followed by finite deletion of inactive content, preserving required replay evidence. No duration is selected here. Raw prompt/tool traces remain disabled by default.

Erasure covers projections, artifact objects and retained copies according to the chosen policy. Keep enough non-content revocation/dedupe evidence to reject admissible old requests; if that evidence is purged, reject requests outside a configured admission horizon rather than treating them as new. Tombstones are reversible application state, not a promise of immediate physical erasure. Reconcile backup recovery and deletion obligations explicitly. A bucket lock cannot be enabled with an invented retention value.

### Backup and restore: verified capability versus project choice

Official documentation reviewed on 2026-11-02 confirms SQLite-backed Durable Objects can recover the entire object's SQL and KV data within the preceding **30 days**, using bookmarks and restoration on restart. This is a vendor capability, not the project's retention period or RPO. PITR is not supported in local development (S21).

R2 bucket locks prevent overwrite/deletion while configured; privileged configuration changes can remove rules (S23). Lifecycle rules delete/transition objects and are not recovery copies (S24). The current S3 compatibility reference was checked, but **native R2 version-history recovery and a coordinated DO-plus-R2 restore guarantee were not verified** (S22). Do not assume them. A generation-specific key and an upload version identifier are not, alone, an archived copy. Cross-object consistency and restore sequencing below are project requirements, not claimed vendor transactions.

**OPEN: owner decision — OD-05.** Select a strategy, recovery destination, export cadence, acceptable data loss (RPO), recovery time (RTO), restore evidence location and non-rollback revocation root:

| Option                                                     | Durable Object SQLite                                        | R2 artifacts/exports                                                                                              | Limitation                                                                                              |
| ---------------------------------------------------------- | ------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| A — Native recovery plus application manifests             | Object PITR plus versioned logical exports                   | Separate generation keys, digests and a protected export prefix/bucket in the same account; lock only after OD-04 | Shared account/provider/configuration failure domain remains                                            |
| B — A plus owner-controlled independent copy (recommended) | Same PITR/exports, copied through an operator procedure      | Manifest and required bytes copied to existing owner-controlled storage, on demand; no always-on runner           | Destination, protection, cadence and restore access need owner selection; no new storage product chosen |
| C — Logical export/import only for disposable tests        | Explicit versioned JSON/JSONL export/import with fresh epoch | Matching artifact copies and digest manifest                                                                      | Loses changes after the export; does not demonstrate native PITR                                        |

Recommendation B uses documented PITR and explicit artifact copies rather than an unverified native R2 versioning feature. It is a recommendation, not an owner selection or implemented backup. No cost, retention, cadence, RPO or RTO number is invented. A logical export is complete only after its manifest and all required artifact bytes are durably available and verified. Retain source schema versions and consistent per-owner checkpoints; no global instant across several Durable Objects and R2 is assumed. Quiesce writes or use an application snapshot/reconciliation protocol. Indexes are rebuildable; operation receipts and authority are not replaceable by a search index.

### Recovery procedure required by the gates

1. Authenticate the operator outside the restored data; engage the kill switch and pause admissions, dispatch and timers.
2. Identify affected object bookmarks/logical exports, schema/configuration versions, artifact keys/digests and the restore manifest. Preserve post-snapshot evidence where available.
3. Restore/import into an inactive recovery context. Verify records and matching artifact bytes; missing data blocks resume. Test native PITR in a permitted hosted test environment, because a local mock cannot establish that vendor behavior.
4. Establish a fresh authority_epoch from current recovery control configuration outside the rollback. Re-enroll affected credentials or apply the separately retained revocation record under ADR-014. Revoke old ExecutionFence handles and pending/unused Approvals. Apply the CONTRACTS restore transition rows; a restored RUNNING Task becomes QUEUED with generation+1, but stays undispatchable during recovery.
5. Reconcile every potentially executed Operation after the selected restore point with available provider/fixture receipts. Missing receipt means ambiguity, not permission to replay. UNKNOWN blocks dependent completion and new effects. Rebuild projections only from verified authority.
6. Check current owner, grants, kill switch, classification, deletion obligations and configured budgets/limits. Owner explicitly authorizes reopening eligible capabilities; historical effects never auto-replay. Record observed data loss, recovery time and remaining defects against OD-05 targets.

Gate 1 requires the memory-only export/import/epoch drill before retained-memory reliance. Gate 3 adds stale execution rejection; Gate 4 adds effect reconciliation; Gate 5 exercises the combined recovery procedure. These are future evidence requirements; no drill is reported as performed.

### Secrets and rotation

Use existing Workers secret bindings for bot, webhook and provider secrets. Keep sensitive values out of plaintext configuration variables, repositories, prompts and diagnostics. Official docs distinguish immediate deployment by `wrangler secret put` from creation of a version by `wrangler versions secret put` followed by explicit version deployment (S25). Secret configuration and upstream credential revocation are separate controls.

**OPEN: owner decision — OD-06.** Choose (A) versioned secret update with reviewed explicit deployment and consumer verification, recommended; or (B) immediate update during an owner-controlled maintenance change. Establish a break-glass operator procedure for compromise. In both cases issue replacement upstream credentials, update only trusted bindings/consumers, verify, revoke the predecessor at its issuer, invalidate affected callback/executor authority and audit the change without secret values. OD-03 supplies cadence/lifetimes/any overlap. This is operator maintenance, never an enabled model Tier-2 secret-rotation capability. Do not assume an older deployed version or cached consumer has lost its old credential merely because a binding changed.

### Ingress rate limiting and backpressure

Apply authentication, payload/schema limits, verified source replay checks and admission control before metered work. Rate-limit by verified principal/workspace/route and source registration after verification; untrusted workspace/user fields cannot choose another principal's bucket. Pre-auth abuse controls must not reveal object existence. Stop and revocation routes have separately protected capacity so normal work cannot starve them; they still require authentication.

Workers Rate Limiting is approximate and per Cloudflare location, not a strict worldwide quota. Official guidance uses a Durable Object counter for a strict cross-location limit (S26). Keep budget reservations and strict provider-credential admission at their existing authoritative ledger/object. Do not advertise an edge counter as an exact cost cap.

**OPEN: owner decision — OD-07.** Choose (A) approximate Worker ingress limits plus authoritative Durable Object admission for strict combined/provider quotas, recommended; or (B) authoritative per-principal Durable Object admission on all accepted routes, with the latency/coordination tradeoff measured. Set actual rates, supported windows, payload/concurrency bounds, backlog policy and protected stop capacity during Gate 0 configuration. No threshold is selected here. Denials return RATE_LIMITED without creating duplicate Tasks/Operations; bounded retry/backoff never enlarges grants.

## Alternatives and consequences

“Backups enabled” without a recovery drill is insufficient. R2 lifecycle deletion is not a backup, nor is SQLite PITR a global application snapshot. A new backup product or telemetry stack is unnecessary at this stage. Explicit manifests, revocation handling and owner settings add operational work; that work is a prerequisite rather than a claim of readiness.

## Acceptance and reconsideration

Gate 0 blocks operational enablement while applicable OD-03 through OD-07 values/procedures are unset. Gate 1 can remain a disposable, permitted-data implementation test until its retention/recovery prerequisites are resolved; it cannot be presented as a retained-memory service. VALIDATION defines the later drills and per-call stale-fence cases. Reconsider the recovery method if actual restore evidence fails the owner's selected requirements. Tier 2 remains disabled and hardware has its separate gate.

Related: [ADR-001](001-platform-scope-and-reuse.md), [ADR-003](003-model-providers-and-cost-control.md), [ADR-004](004-executor-contracts-and-portability.md), [ADR-005](005-state-ownership-and-durable-execution.md), [ADR-006](006-authorization-and-external-effects.md), [ADR-009](009-observability-evaluation-and-recovery.md), [ADR-010](010-mvp-scope-and-release-gates.md), [ADR-011](011-telegram-and-human-approval.md), [ADR-012](012-shared-memory-and-context-service.md), [ADR-013](013-event-driven-superagent-coordination.md), [ADR-014](014-identity-and-authorization.md).
Sources: S13, S21, S22, S23, S24, S25, S26.
