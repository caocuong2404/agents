# ADR-008 — Share mission-level services and keep physical control at the edge

Date: 2026-11-02  
Status: Accepted boundary; device integration and physical deployment deferred  
Supersedes: ADR-008 in baseline v0.3

## Context

The same platform may later coordinate IoT devices, robots and digital representations of equipment. Hardware systems have timing, connectivity and safety requirements different from software agents. A shared cloud service should not replace domain-specific controllers.

## Decision

Reuse identity, task/mission records, policy, approval, evidence and observability at a mission-level boundary. An adapter translates a scoped mission into the protocol and capabilities of an existing gateway or controller. Keep device firmware, real-time loops, motion planning where appropriate, safety interlocks and emergency stop behavior inside the edge domain.

Examples of mission requests are “inspect zone A” or “report an abnormal temperature.” They are not open-ended cloud streams of motor commands. A cloud or LLM failure must not remove a device's local safety behavior.

Specify disconnection behavior per capability. A device may complete a bounded previously authorized mission, pause, or stop safely according to its local policy. It cannot expand authority while disconnected. Commands carry identity, command ID, sequence/generation, expiry and preconditions; reject duplicates, stale generations and expired commands. Reconnection requires explicit reconciliation, not automatic replay of every queued actuator command.

Use established domain technology through adapters. MQTT is a candidate for IoT messaging; an existing robotics/ROS 2 interface is a candidate for robots. No hardware transport, robotics distribution or device-management product is selected by this ADR. Do not force high-rate telemetry, video or safety loops through MCP or an LLM prompt. Feed relevant observations/events to reasoning and retain raw streams in suitable domain storage only when needed.

If digital twins become a confirmed requirement, distinguish reported state, desired state, observation time and data freshness. A stored desired state is not evidence that a physical action happened. Evaluate an existing twin system, such as Eclipse Ditto, before building one. “Twin-flame” remains ambiguous terminology from discussion; it is not interpreted as an approved product or requirement.

## Alternatives and consequences

Cloud-dependent real-time actuation is rejected. A new universal robot/IoT runtime is deferred. Existing controllers remain authoritative for physical safety, so integration effort includes capability mapping and domain validation.

## Acceptance and reconsideration

A later simulator must prove expired-command rejection, disconnection behavior, duplicate handling and state freshness. Physical deployment additionally requires hardware-specific safety analysis, emergency-stop validation, approved permissions and a separate release gate. Passing software-agent tests does not establish physical-system safety.

Related: [ADR-001](001-platform-scope-and-reuse.md), [ADR-004](004-executor-contracts-and-portability.md), [ADR-005](005-state-ownership-and-durable-execution.md), [ADR-006](006-authorization-and-external-effects.md), [ADR-009](009-observability-evaluation-and-recovery.md), [ADR-010](010-mvp-scope-and-release-gates.md), [ADR-014](014-identity-and-authorization.md).
Sources: S15, S16, S17.
