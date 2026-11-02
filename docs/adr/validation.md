# Validation and rollout gates

Date: 2026-11-02  
Current evidence: source/documentation review and architecture consistency checks only. No deployment, runtime qualification, penetration test, hardware safety test or performance benchmark has been completed by this ADR set.

## Gate 0 — Reproducible implementation baseline

Record chosen source commit, package lockfile, runtime/compatibility date, resource bindings, identity model and enabled capabilities. Review [ADR-014 identity and authorization](014-identity-and-authorization.md) and [ADR-015 operational baseline](015-operational-baseline.md) before enablement. Verify the enrolled owner/recovery binding, authoritative grants, model-read-only policy, executor registration procedure, credential revocation, kill switch and applicable OPEN decisions. Remove example tools and unwanted scheduled prompts. Use public or explicitly allowed test data. Confirm free-tier/paid requirements before provisioning; these ADRs do not activate billing or grant production access.

Configuration review must identify finite retention/replay/dedupe policy, selected backup/copy and non-rollback revocation strategy, secret update/revocation procedure and ingress/admission limits. Actual values/selections remain OPEN in OPEN-DECISIONS.md; a recommendation is not an owner decision. Review forged workspace/object IDs and the server lookup order specified in ADR-014. Test allowed data only; Tier 2 stays disabled.

Evidence to obtain: reproducible local build, configuration review, dependency/license inventory, data classification and a list of deferred features. No production release while retention, restore or identity configuration is unspecified.

## Gate 1 — Shared memory and task context boundary

This gate is memory-only. Use two independently authenticated clients and at least two scopes under ADR-014. Store explicit permitted Memory records through memory.remember; use memory.recall/read with provenance from a fresh client environment. Do not create Task, Assignment, Approval or Operation records, start a scheduler or require context.build. Task-bound context is Active from Gate 2. Gate 1 is separable from that machinery. Disposable tests do not establish retained-data reliance; applicable retention/recovery settings must be chosen first.

| Test                                                          | Expected outcome                                                                                                                                                                      |
| ------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Cross-client same-scope recall                                | Authorized clients retrieve consistent current records with provenance                                                                                                                |
| Forged scope or object ID                                     | [ADR-014](014-identity-and-authorization.md) mapping/grants applied; requested and stored object workspace both checked; denied before content disclosure or object-existence leakage |
| Updated fact/decision                                         | Current version used; old source retained or tombstoned according to policy                                                                                                           |
| Prompt injection in a memory                                  | No new capability, policy write or secret access                                                                                                                                      |
| Missing or stale artifact/index                               | Visible error or rebuild; authority remains intact                                                                                                                                    |
| Export/import and restore                                     | Correct records/digests; inactive import and fresh authority epoch                                                                                                                    |
| Forget/delete policy                                          | Normal recall excludes tombstones; permanent erasure follows the owner-selected ADR-015 policy                                                                                        |
| Unmapped OAuth subject, wrong issuer/audience, revoked client | Denied under ADR-014, even if supplied scope appears valid                                                                                                                            |
| Memory mutation replay / same key changed arguments           | Same canonical request returns the original record; changed arguments return IDEMPOTENCY_CONFLICT                                                                                     |
| Old memory/control fence after revoke or restore              | Rejected; no stale mutation or grant resurrection                                                                                                                                     |
| Memory-only deployment inventory                              | No Task/Assignment/Approval/Operation stores or runtime required                                                                                                                      |

Obtain passing evidence before presenting the service as shared cross-harness memory. Include the ADR-015 memory-only restore drill: permitted export/import, bytes/digests, inactive restore, fresh epoch and current grants/revocations; no task/effect replay is possible at this gate. Semantic search is optional and requires a recall evaluation against representative questions.

## Gate 2 — Existing reasoning runtime

Activate Task, Assignment, BudgetReservation and task-bound context only here. Use one current task, the existing native harness executor and read-only tools under the CONTRACTS claim/fence protocol. Check durable intake, replay of an idempotency key, disconnect/reconnect, restart during a turn, a failed model call, a changed instruction and cancellation. Ensure the application task record, not a stale transcript summary, controls the current revision.

Compare Think and Pi only where required. Record developer time, changes needed, accepted outcomes, human intervention, latency and actual cost. Adopt the simpler successful runtime. Pin the selected versions; no performance winner is declared by this ADR set.

## Gate 3 — Replaceable isolated executor

Run a permitted repository inspection/test proposal in an isolated on-demand environment. No production keys or external write authority.

The central failure test is:

1. Executor A claims revision 1 in generation 1 and saves a checkpoint.
2. Disconnect or kill A.
3. The user changes acceptance criteria; authority records revision 2.
4. A qualified B claims the current assignment in a newer generation.
5. A's old heartbeat, checkpoint, report and operation proposal arrive late.
6. The service rejects stale authority. B receives revision 2 and can continue from valid evidence without accepting an obsolete result.

### Per-call stale-fence cases required at Gate 3

Each row is a separate negative test with otherwise valid authentication and a new idempotency key. Compare pre/post authority, lease, receipts and dedupe state: no current-state mutation. Repeat with old epoch, old generation, changed task revision and expired/revoked Assignment where applicable. Error is STALE_FENCE with the appropriate FenceReason, retryable=false and REFETCH_AND_REAUTHORIZE; revoked credentials may be rejected earlier without disclosure. A supplied epoch/generation tuple is not a token.

| Mutating entry point    | Stale input and expected rejection                                                                                                                                                |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| executor.claim          | Old ScopeFence after epoch/policy/scope revision change; no Assignment or lease created                                                                                           |
| executor.heartbeat      | Old ExecutionFence after reassignment or matching lease expiry; no renewal                                                                                                        |
| executor.checkpoint     | Old ExecutionFence/current-looking supplied revision; no accepted checkpoint                                                                                                      |
| executor.report         | Old ExecutionFence; no result/status acceptance                                                                                                                                   |
| artifact.commit         | Old ExecutionFence; no canonical artifact reference accepted                                                                                                                      |
| operation.propose       | Old ExecutionFence; no Operation, approval or proposal dedupe record created                                                                                                      |
| operation.dispatch      | Independently test old broker RecordFence and old execution_fence with the other current; both rejected; no dispatch/send; an executor token alone cannot authenticate the broker |
| operation.recordOutcome | Old service RecordFence/epoch/record revision; no outcome overwritten                                                                                                             |
| operation.reconcile     | Old service RecordFence; no receipt/state mutation or new write                                                                                                                   |

Operation machinery is Active from Gate 4. At Gate 3 its disabled entry points receive negative front-door contract tests only: authenticate and validate the fence before rejecting an otherwise current call as gate-inactive. They persist no Operation or Approval and have no effect adapter. This tests stale rejection without enabling a mutation capability. Gate 4 repeats these cases with the selected fixture active. A RecordFence for current service reconciliation is deliberately distinct from a revoked worker fence; only the former may record late evidence.

Exercise every Task/Assignment transition, including review ACCEPT/REWORK/CHANGE_REQUIREMENTS, explicit versioned continuation, wait/resume, cancellation reconciliation and restore. Lease expiry must produce RUNNING to QUEUED, generation+1 and LEASE_EXPIRED on the old Assignment. No transition may bypass its CONTRACTS guard.

Also test concurrent claims, lease renewal races, unavailable environments, failed checkpoint upload, digest mismatch and cancellation while a command is running. Inspect isolation and egress with planted test secrets. Do not infer isolation from a task directory or shell wrapper.

## Gate 4 — Human approval and one controlled mutation

First enable owner pairing and notifications without execution. Operation and Approval machinery becomes eligible here. **OPEN: owner decision — OD-08** blocks enabling the first Tier-1 fixture. Options: an application-owned Task-store canary receipt in a dedicated test namespace (recommended), or a fixture-file mutation in an isolated on-demand scratch directory without network/third-party mounts. Owner must name the exact destination and permitted operation. Both are reversible, scoped and free of third-party effects; they do not authorize a real external integration. Treat the chosen fixture as Tier 1 to test approval even though its effects are contained. Keep Tier 2 disabled.

Use one Approval vocabulary and the status/transitions in CONTRACTS. Exercise PENDING to APPROVED/REJECTED, expiry before decision/first dispatch, revocation, changed-intent supersession and replay as a no-op. Repeat Gate 3 per-call stale tests with current/stale fences and the fixture active. memory.remember and operation.propose must each reject same-key/different-argument retries; proposal replay/replacement must preserve the trusted-owner-minted operation_id.

OD-09 remains OPEN: calibrate the inherited 15-minute expiry for overnight phone review. Do not extend it automatically. Test notice-then-fresh-review only if that option is selected by the owner.

| Test                                           | Expected outcome                                                                                      |
| ---------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| Valid owner/chat/descriptor                    | One recorded decision and eligible operation                                                          |
| Wrong owner/chat, group, forged webhook secret | Denied                                                                                                |
| Duplicate or concurrent callbacks              | One transition; no duplicate dispatch                                                                 |
| Expired approval or changed revision/arguments | New write denied; fresh approval required                                                             |
| Revoked permission or stale generation         | Denied even if a prior button was approved                                                            |
| Crash before remote call                       | Durable intent recovers without losing authorization checks                                           |
| Fixture success, receipt lost                  | UNKNOWN then fixture reconciliation; no blind duplicate; this is not evidence for any remote provider |
| Simulated provider idempotency window expired  | Reconcile before any new write; a later real integration must repeat against its actual API           |
| Telegram unavailable                           | Hold/web inbox/expiry; no approval by silence                                                         |
| Attempted Tier-2 action                        | Rejected regardless of Telegram decision                                                              |

## Gate 5 — Bounded autonomy and operational readiness

Enable one reviewed event source/rule. Test deterministic filtering, same-thread pass-through, unrelated follow-up hand-back, burst backpressure, authenticated stop priority and bounded concurrency. Speculative read-only work needs a budget and a defined notification condition.

Inject a stuck task, repeated provider/tool failures and a Router failure. Verify independent monitoring, backoff/circuit breaking, operator visibility and bounded recovery. Verify concurrent budget reservations and reconciliation to provider/infrastructure usage. Document any possible overshoot and residual failure modes.

Before reliance on retained data, complete the applicable [ADR-015 restore procedure](015-operational-baseline.md), with [ADR-014 non-rollback identity controls](014-identity-and-authorization.md). Gate 1 already requires a memory-only drill; this gate exercises combined state, artifact and operation recovery. Restore individual DO bookmarks or selected logical exports plus matching R2 bytes/manifests in an inactive context; verify digests and current revocations, rotate authority_epoch, reject all old executor/operation fences and revoke unused approvals. Restore RUNNING to QUEUED with generation+1 while dispatch stays paused. Reconcile post-snapshot effects/UNKNOWN before explicit owner reopen. Measure observed loss/recovery time against owner-selected RPO/RTO, and exercise missing-artifact, deleted-content, stale-revocation-root and lost-receipt cases. Native PITR needs a hosted permitted-data test because S21 excludes local development. No coordinated DO/R2 point-in-time snapshot is presumed. A backup setting is not restore evidence. A Cloudflare-hosted monitor does not establish independence from a Cloudflare-wide outage.

## Later hardware gate — Separate workstream

A fake executor can test protocol extensibility earlier. Real IoT/robotics begins only with a concrete device/mission requirement, suitable existing controller/gateway, approved data/permissions and domain validation. Test command expiry, freshness, disconnection policy, stale generation, duplicates, local emergency stop and reconciliation. Software MVP success does not authorize actuation or establish physical safety.

## Evidence to retain for every gate

- Build/source/dependency and configuration versions.
- Task inputs and explicit acceptance criteria using permitted data.
- Failure injection point, expected outcome and observed outcome.
- Correlation IDs, state transitions, redacted receipts and evidence references.
- Actual inference/infrastructure cost and intervention time where applicable.
- Open defects, residual risks and the decision to enable only the qualified capability.

The primary outcome metric is human minutes per accepted result. Track quality, completion time, intervention/retry count, cost and unauthorized-action attempts alongside it. Establish quantitative thresholds from measured representative workloads before claiming an SLO.
