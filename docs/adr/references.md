# Sources and decision provenance

Reviewed: 2026-11-02 (Asia/Saigon). Vendor documentation and repositories can change. Resolve exact implementation commits, package versions and plan availability during qualification.

These sources establish available concepts and interfaces. They do not prove this project's deployment reliability, security, cost or performance. Architecture decisions and application contracts in this set are project choices. No marketing benchmark, star count or “world-class” label substitutes for the gates in VALIDATION.md.

## Project provenance

- AI Agent Platform baseline v0.3 — Telegram HITL, dated 2026-10-19. **Not publicly accessible:** private owner-provided document; no public retrieval URL or claim of an archived v0.3 copy is supplied. Retained defaults: public/permitted workloads, separate trust domains, no assumed always-on runner, provisional budget targets, Telegram private-DM approval, 15-minute expiry and disabled Tier-2 actions. This source was read in full for consolidation.
- **Not publicly accessible:** the owner-provided export `Claude-Thuật ngữ chỉ trung tâm chỉ huy agentic-20261102-1418.md`, dated 2026-11-02, and the current project discussion. These establish the goals of Cloudflare-hosted shared services, interchangeable CLI/harness execution, shared memory, reduced custom implementation and future hardware extension. Third-party assistant text in that export was treated as a design reference and checked where material, not as implementation evidence.
- The current discussion leaves “twin-flame” undefined. No specific product or interpretation is accepted by this set.

## Primary technical sources

### S01 — DeepSeek Super-agent design and prototype

- [Original post/article](https://x.com/DeepSeekHarness/status/2116857257245987236)
- [Public article mirror](https://threadnavigator.com/thread/2116857257245987236/)
- [Prototype repository](https://github.com/deepseek-ai/dsh-superagent-prototype)

Used for the logical Router/Worker split, event-driven intake, subscribed follow-up routing, on-demand context retrieval and independent recovery. The original image assets were inspected. Article text was recovered through a public mirror and is described as a machine translation. The repository identifies itself as a research prototype, narrower than the proposed architecture. Its scheduling experiments do not establish coding quality or this platform's production readiness.

### S02 — Cloudflare Agents runtime and harnesses

- [Agents overview](https://developers.cloudflare.com/agents/)
- [Runtime](https://developers.cloudflare.com/agents/runtime/)
- [Think](https://developers.cloudflare.com/agents/harnesses/think/)
- [PiHarness](https://developers.cloudflare.com/agents/harnesses/pi/)
- [Long-running agents](https://developers.cloudflare.com/agents/concepts/agentic-patterns/long-running-agents/)

Used for durable identities, native persistence, lifecycle capabilities and existing harness selection. Think/Pi experimental surfaces require pinning and qualification. Hibernation preserves durable state, not arbitrary process memory or open external operations.

### S03 — Authenticated MCP ingress

- [Official authenticated MCP example](https://github.com/cloudflare/agents/tree/main/examples/mcp-worker-authenticated)
- [Example README](https://github.com/cloudflare/agents/blob/main/examples/mcp-worker-authenticated/README.md)
- [Remote MCP guide](https://developers.cloudflare.com/agents/model-context-protocol/guides/remote-mcp-server/)

Used for the authenticated handler/OAuth integration pattern. Application identity binding, workspace authorization and capability policy remain project responsibilities. A stateless MCP handler does not persist application work by itself.

### S04 — Durable programmatic submissions

- [Think submissions example](https://github.com/cloudflare/agents/tree/main/examples/think-submissions)
- [README](https://github.com/cloudflare/agents/blob/main/examples/think-submissions/README.md)
- [Server source](https://github.com/cloudflare/agents/blob/main/examples/think-submissions/src/server.ts)

Used for durable ACK, idempotent admission, status, cancellation and scheduled submissions. Source/configuration were reviewed. This set does not report executing the example.

### S05 — Multi-step durable workflows

- [Think Workflows example](https://github.com/cloudflare/agents/tree/main/examples/think-workflows)
- [Agents with Workflows](https://developers.cloudflare.com/agents/concepts/workflows/)
- [Workflows limits](https://developers.cloudflare.com/workflows/reference/limits/)

Used for checkpointed steps, waits and approval integration. Completed-step recovery does not establish exactly-once effects in an external system. Pass artifact references rather than relying on unbounded workflow payloads; check current limits in the implementation.

### S06 — Persistent context and conversation memory

- [Think getting started](https://github.com/cloudflare/agents/blob/main/docs/think/getting-started.md)
- [Conversation state and memory](https://developers.cloudflare.com/agents/concepts/conversation-state-and-memory/)

Used for persistent native history, context blocks, search and compaction. Session memory APIs include experimental interfaces. Native per-agent context is not automatically a cross-harness shared-memory API.

### S07 — doobidoo memory service

- [Repository](https://github.com/doobidoo/mcp-memory-service)
- [Cloudflare backend](https://github.com/doobidoo/mcp-memory-service/blob/main/docs/cloudflare-setup.md)

Reviewed as an optional community memory engine. It uses a Python service and can use Cloudflare D1/Vectorize/R2 storage. It is not assumed to be a drop-in Worker-only runtime.

### S08 — Supermemory self-hosting boundary

- [Repository](https://github.com/supermemoryai/supermemory)
- [Self-hosting overview](https://supermemory.ai/docs/self-hosting/overview)

Reviewed as an optional vendor memory solution. The public OSS components and the downloadable server are not identical licensing/source scopes; the self-hosting documentation states that the server binary comes from a non-public codebase. No proprietary engine is copied or required by this architecture.

### S09 — Graphiti

- [Repository and requirements](https://github.com/getzep/graphiti)

Reviewed as an optional graph-memory engine. It adds Python and graph-database requirements. Reference MCP/REST server examples are not assumed to provide production authorization or isolation.

### S10 — Model access through Cloudflare AI Gateway

- [REST interfaces](https://developers.cloudflare.com/ai-gateway/usage/rest-api/)
- [AI Gateway](https://developers.cloudflare.com/ai-gateway/)

Used for optional metered provider integration. Provider support, routing semantics and billing path are checked for the selected adapter; CLI subscription traffic is not presumed to use the gateway.

### S11 — Budget-limit caveats

- [AI Gateway spend limits](https://developers.cloudflare.com/ai-gateway/features/spend-limits/)

Used for eventual-consistency/possible-overshoot limitations and the distinction between model and infrastructure spend. Project numerical targets are inherited engineering defaults, not vendor pricing or vendor-enforced combined caps.

### S12 — Separating reasoning, session and execution

- [Anthropic: Scaling Managed Agents](https://www.anthropic.com/engineering/managed-agents)

Used as an architecture reference for independent session logs, harness lifecycle and execution environments, and credential separation. The project does not depend on the managed service or claim compatibility with every vendor runtime.

### S13 — Durable Object concurrency and storage

- [Rules of Durable Objects](https://developers.cloudflare.com/durable-objects/best-practices/rules-of-durable-objects/)
- [SQLite storage](https://developers.cloudflare.com/durable-objects/api/sqlite-storage-api/)

Used for state ownership and explicit transaction/concurrency design. Single-threaded execution is not a blanket transaction across awaited remote calls or several actors.

### S14 — Sandbox credential and network mediation

- [Calling an authenticated API from a sandbox](https://developers.cloudflare.com/sandbox/network/call-an-authenticated-api/)
- [Sandbox overview](https://developers.cloudflare.com/sandbox/)

Used as a candidate mechanism for trusted request mediation. Exact APIs and containment properties must be validated for the selected version and executor. Host/method/resource restrictions do not by themselves prove non-exfiltration.

### S15 — Robotics real-time boundary

- [ROS 2 real-time background](https://design.ros2.org/articles/realtime_background.html)

Used for the distinction between deadline guarantees and average low latency. A cloud LLM call is not assumed to satisfy a physical controller's timing or safety requirements.

### S16 — IoT messaging

- [MQTT](https://mqtt.org/)
- [MQTT FAQ](https://mqtt.org/faq/)

Used to identify an established publish/subscribe option for future constrained-device integration. No MQTT broker, QoS level or device transport is selected yet. Transport delivery guarantees do not establish exactly-once physical actuation.

### S17 — Digital-twin reuse

- [Eclipse Ditto](https://eclipse.dev/ditto/)
- [Protocol overview](https://eclipse.dev/ditto/protocol-overview.html)

Used for the optional distinction between reported/desired state and a live device channel. No twin service is required by the MVP, and no interpretation of the unresolved “twin-flame” phrase is implied.

### S18 — Telegram bot interfaces

- [Telegram Bot API](https://core.telegram.org/bots/api)
- [Bot platform](https://core.telegram.org/bots/)

Used for private-chat integration, inline callback data limits, callback acknowledgment and webhook secret mechanisms. All application permissions and approval records are enforced independently on the server.

### S19 — Telegram privacy boundary

- [Telegram FAQ](https://telegram.org/faq)

Used for the Cloud Chat versus Secret Chat distinction. This platform's policy keeps sensitive artifacts and credentials out of approval messages.

### S20 — Managed Agent Memory availability

- [Cloudflare Agent Memory](https://developers.cloudflare.com/agent-memory/)

The documentation reviewed labels the standalone Agent Memory product private beta. It is excluded as a required initial dependency; ordinary Durable Object persistence remains available independently.

### S21 — Durable Object SQLite recovery

- [Official SQLite storage and PITR API](https://developers.cloudflare.com/durable-objects/api/sqlite-storage-api/)

Verified 2026-11-02: per-object SQL/KV recovery within the preceding 30 days; bookmarks and next-session restoration; not supported in local development. The vendor window is not a selected project retention/RPO. ADR-015 supplies application recovery requirements, not a claimed global snapshot.

### S22 — R2 S3 API compatibility and verification limit

- [Official compatibility reference](https://developers.cloudflare.com/r2/api/s3/api/)

Reviewed 2026-11-02. Native version-history recovery and a coordinated R2/DO restore guarantee were not verified from this official reference. Neither is assumed. ADR-015 recommends explicit generation keys, artifact copies and manifests as project design; this source is not cited as proof of versioned backups.

### S23 — R2 bucket locks

- [Official bucket-lock documentation](https://developers.cloudflare.com/r2/buckets/bucket-locks/)

Verified 2026-11-02: configured locks prevent overwrite/deletion; authorized configuration changes can remove rules, and locks override lifecycle deletion. A lock is not an independent backup or an irrevocable defense against account compromise. Project durations remain OPEN.

### S24 — R2 object lifecycle

- [Official lifecycle documentation](https://developers.cloudflare.com/r2/buckets/object-lifecycles/)

Verified 2026-11-02: lifecycle rules manage object expiration/deletion and transitions. They are not recovery copies. No sample expiration duration has been adopted as project policy.

### S25 — Workers secrets and version deployment

- [Official Workers secrets documentation](https://developers.cloudflare.com/workers/configuration/secrets/)

Verified 2026-11-02: sensitive values belong in secret bindings rather than plaintext variables. The immediate secret-put command deploys; the versions secret-put command creates a version for explicit deployment. Upstream revocation and consumer verification remain project/operator responsibilities; no automatic revocation guarantee is inferred.

### S26 — Workers ingress rate limits

- [Official Rate Limiting binding documentation](https://developers.cloudflare.com/workers/runtime-apis/bindings/rate-limit/)

Verified 2026-11-02: approximate counters apply per Cloudflare location; a strict cross-location limit can use a Durable Object. Project thresholds and protected stop capacity remain OPEN. An edge counter is not an exact worldwide budget cap.

## Selection evidence and limits

The review covered official examples plus community alternatives and read selected source/configuration files. It did not establish a certified production baseline. A source-reviewed template may still need authentication, scope enforcement, dependency updates, resource configuration and failure tests. Re-check upstream changes when adopting or upgrading a dependency.

## v1.1 archive provenance

The original delivery retained its immutable v1.0 input ZIP, SHA-256 3fd1d8ba0159cf6e242b9b9f390083365c853b92e6c8e356f8fecbc774919158. This public repository retains the digest and active v1.1 documents; the historical ZIP and private evidence remain outside the repository. See [source provenance](source-provenance.md#historical-archive).
