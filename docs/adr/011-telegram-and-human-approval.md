# ADR-011 — Use Telegram for scoped phone approvals with trusted server-side state

Date: 2026-11-02  
Status: Accepted owner-directed default; not implemented  
Supersedes: ADR-011 in baseline v0.3

## Context

The owner requested a free Telegram Bot channel for phone approvals. The platform needs asynchronous review without a running sandbox, and a fallback when the channel is unavailable. Messaging identity and a button click have limited assurance and cannot define the application's permissions.

## Decision

Use a paired Telegram Bot private DM for notifications and eligible Tier-1 approvals. Keep authoritative pending operations, approval state, policy and receipts in the trusted task store. Use an authenticated web inbox for fallback and future step-up. Human-approval capabilities are not available to ordinary agent/MCP credentials.

### Enrollment

The owner creates a bot through BotFather and initiates interaction. In the authenticated web console, issue a short-lived single-use pairing challenge. Validate the returned `/link` event and bind the verified application owner to the exact numeric Telegram `from.id` and private `chat.id`, with explicit workspace permissions. Support revocation. No username-only identity and no group-chat approval.

Store the bot token and webhook secret in trusted secret configuration. They never enter a model prompt, sandbox, browser bundle or audit payload.

### Approval flow

1. Persist an immutable operation descriptor: operation ID, owner/workspace/task, task revision, capability, target, arguments hash/reference, relevant preconditions, risk and expiry. Bind the execution generation at dispatch; ownership changes invalidate stale execution authority.
2. Send a concise non-sensitive summary with Approve/Reject controls. `callback_data` contains only an opaque unpredictable reference plus a decision discriminator, within Telegram's 1–64-byte limit. Retain token verification material server-side.
3. Validate the webhook secret, allowed update type, numeric owner/chat identity, workspace permission, pending status, expiry and current descriptor. Deduplicate webhook deliveries. Atomically approve or reject once; concurrent callbacks cannot win twice. Respond with `answerCallbackQuery`.
4. Before dispatch, recheck current policy, task revision/generation, target preconditions and approval scope. Execute through ADR-006's broker, then record and report the outcome.
5. Default approval expiry is 15 minutes. Reject late first dispatch. If an operation has an uncertain remote outcome, allow scoped reconciliation; a new or changed write requires a valid approval. Expiry never means rollback of an action already performed.

Telegram approval is consent for an eligible operation, not an access grant. A changed argument, target or accepted task revision requires a fresh descriptor and approval. A bot may propose an action but cannot approve its own request.

**OPEN: owner decision — OD-09:** the retained 15-minute approval expiry needs calibration for overnight phone approvals. Options are to keep it and request while the owner is available; choose a longer owner-specified expiry after review; or send a non-authorizing overnight notice and create a fresh short-lived Approval when the owner opens review (recommended). No new duration, automatic extension or approval by silence is selected. ADR-014 separates the owner, bot and webhook principals; ADR-015 covers secret rotation and retention.

### Failure and privacy behavior

If delivery, pairing or Telegram fails, hold the operation and expose it in the web inbox; expire normally. No approval by silence. A successful send API call is not proof that the owner read the message. Bot conversations are Cloud Chats rather than end-to-end encrypted Secret Chats, so omit source code, private documents, credentials and sensitive identifiers from notifications.

Tier-2 actions remain disabled. If later enabled through a separate decision, Telegram provides a notice/link to authenticated web step-up, not single-tap authorization. Local physical safety remains mandatory.

## Alternatives and consequences

Web-only approval remains a valid fallback. Other notification providers are deferred. Keep a narrow Approval record contract (request creates PENDING; decision updates that record) so transport replacement does not replace policy. Telegram adds account/device and delivery dependencies.

## Acceptance and reconsideration

Test wrong user/chat, groups, forged webhook secret, duplicate updates, concurrent approve/reject, expiry, changed descriptor, revoked access, unpairing, replay and channel outage. Test a crash around dispatch and remote receipt. A bot-token compromise requires rotation, review and invalidation of pending callback references.

Related: [ADR-005](005-state-ownership-and-durable-execution.md), [ADR-006](006-authorization-and-external-effects.md), [ADR-009](009-observability-evaluation-and-recovery.md), [ADR-010](010-mvp-scope-and-release-gates.md), [ADR-014](014-identity-and-authorization.md), [ADR-015](015-operational-baseline.md).
Sources: S18, S19.
