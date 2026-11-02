# Shared Agent Platform

A personal platform where existing agent clients share scoped project knowledge and submit durable work. This glossary names the domain; architecture and implementation decisions live in the ADRs.

## Language

**Owner**:
The enrolled human who controls project access, reviews eligible actions, and can stop work.
_Avoid_: task owner, executor.

**Task owner**:
The trusted application authority that owns a Task's accepted state and transitions.
_Avoid_: owner, worker.

**Client**:
An enrolled application or bot that accesses permitted project knowledge or submits work.
_Avoid_: executor, owner.

**Executor**:
An enrolled execution environment that may perform an authorized Assignment within its registered capabilities.
_Avoid_: client, universal worker.

**Workspace**:
A permission domain containing project knowledge and work records. The initial owner uses one Workspace.
_Avoid_: conversation, project.

**Project scope**:
A resource boundary within the Workspace that determines which project knowledge or work a Principal may access.
_Avoid_: client identity, public access.

**Principal**:
A verified owner, client, executor, webhook, or internal service identity to which explicit authority can be granted.
_Avoid_: display name, caller-supplied role.

**WorkspaceGrant**:
The explicit actions and resource restrictions granted to one Principal within one Workspace.
_Avoid_: remembered instruction, assumed access.

**Memory**:
A scoped, versioned knowledge record with provenance, classification, and a lifecycle.
_Avoid_: conversation transcript, policy.

**Project decision**:
A decision recorded as project knowledge, with its source and current or historical status.
_Avoid_: access grant, inferred consent.

**Conversation**:
The history of messages and tool interactions belonging to a client or harness session.
_Avoid_: Task, shared project memory.

**Submission**:
A durably accepted request for a reasoning turn that can be inspected or cancelled.
_Avoid_: completed Task, external-effect receipt.

**Task**:
An accepted objective with scope, acceptance criteria, current revision, and an authoritative work lifecycle.
_Avoid_: todo note, conversation, Submission.

**Assignment**:
A bounded grant for a registered Executor to work on a particular Task revision.
_Avoid_: permanent permission, queue delivery.

**Operation**:
A typed intent for an externally visible action, with its target, arguments, preconditions, and outcome evidence.
_Avoid_: arbitrary command, completed effect.

**Approval**:
A verified human decision bound to one eligible Operation's exact intent.
_Avoid_: workspace access, blanket consent.

**Policy**:
The owner-controlled rules that determine permitted access and actions.
_Avoid_: learned memory, prompt suggestion.

**Router**:
The coordination role that correlates requests, presents progress, and dispatches permitted work.
_Avoid_: universal writable memory, unrestricted executor.
