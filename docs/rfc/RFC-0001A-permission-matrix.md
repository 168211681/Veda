RFC-0001A — Constitutional Permission Matrix

RFC: RFC-0001A

Title: Constitutional Permission Matrix

Status: Draft

Version: 0.1.0

Category: Security / Authorization

Depends on: RFC-0001 (Veda Constitution)

⸻

Abstract

This RFC defines how Veda enforces constitutional authority through a structured permission system.

RFC-0001 establishes who has authority.

RFC-0001A defines how authority is evaluated, granted, scoped, revoked, logged, and enforced.

Every consequential action executed by Veda MUST pass through the Permission Matrix before entering the Capability Fabric.

The Permission Matrix is the first executable policy layer of the Veda architecture.

⸻

Motivation

A highly capable AI without permission boundaries is effectively a root process with unlimited authority.

Veda intentionally separates five concepts:

Intelligence
    ↓
Capability
    ↓
Permission
    ↓
Authorization
    ↓
Execution

This separation prevents:

* silent privilege escalation,
* unrestricted tool usage,
* unauthorized system modification,
* uncontrolled autonomous behavior.

⸻

Scope

This RFC governs:

* Capability evaluation.
* Permission rules.
* Risk classification.
* Approval levels.
* Resource scoping.
* Capability leases.
* Revocation.
* Emergency policies.
* Audit requirements.

It applies to:

* Human users.
* AI models.
* Internal agents.
* External agents.
* MCP tools.
* Future NCP agents.
* Veda Runtime.
* Future Veda OS.

⸻

Permission Model Overview

The permission pipeline is:

Action Proposal
      ↓
Capability Lookup
      ↓
Scope Validation
      ↓
Risk Classification
      ↓
Policy Evaluation
      ↓
Lease Validation
      ↓
Approval Engine
      ↓
Capability Granted
      ↓
Execution
      ↓
Verification
      ↓
Audit

A capability MUST NOT execute before completing this pipeline.

⸻

Permission Primitives

Capability

A named executable operation.

Examples:

filesystem.read
filesystem.write
filesystem.delete
process.execute
network.request
github.commit
github.push
docker.run
model.generate
system.package.install

Capabilities are immutable identifiers.

⸻

Permission

Permission answers one question:

May this capability be used in this context?

Permission depends on:

* actor,
* capability,
* target,
* resource scope,
* policy,
* lease,
* risk class.

⸻

Authorization

Authorization is a concrete approval decision for one proposed action.

Authorization MUST include:

* authorization_id
* actor_id
* action_id
* capability
* policy_id
* approval_level
* expiration
* timestamp

Authorization MUST be auditable.

⸻

Capability Registry

Every capability MUST exist inside the registry.

Example schema:

capability:
  id: filesystem.write
  category: filesystem
  description: Write file contents.
  reversible: true
  default_risk: R1
  approval_default: L1
  audit_required: true

Registry fields:

Field	Description
id	Stable capability identifier.
category	Capability family.
description	Human-readable explanation.
reversible	Can it be rolled back?
default_risk	Initial risk class.
approval_default	Default approval level.
audit_required	Whether execution always generates audit records.

⸻

Capability Categories

Filesystem

* filesystem.read
* filesystem.write
* filesystem.create
* filesystem.rename
* filesystem.move
* filesystem.delete
* filesystem.snapshot

Process

* process.execute
* process.kill
* process.inspect

Network

* network.request
* network.download
* network.upload
* network.websocket

Development

* git.read
* git.commit
* git.branch.create
* github.push
* github.pull_request

Containers

* docker.run
* docker.build
* docker.stop

Knowledge

* brain.read
* brain.write
* memory.read
* memory.write

Security

* capability.grant
* capability.revoke
* policy.modify
* security.modify

System

* system.package.install
* system.package.remove
* system.reboot
* system.shutdown

⸻

Risk Classification

Every action MUST receive a runtime risk score.

R0 — Read Only

Characteristics:

* no persistent modification,
* no external side effect,
* reversible by definition.

Examples:

* Read documents.
* Search knowledge.
* Inspect logs.
* Query CPU usage.

Default approval: L0

⸻

R1 — Reversible Local Change

Characteristics:

* local,
* reversible,
* limited scope.

Examples:

* Create project file.
* Modify sandbox.
* Generate code.
* Compile software.

Default approval: L1

⸻

R2 — Significant Persistent Change

Characteristics:

* persistent modification,
* affects important resources,
* rollback recommended.

Examples:

* Delete project files.
* Install packages.
* Modify configuration.
* Push Git commits.

Default approval: L2 / L3

⸻

R3 — External High Impact

Characteristics:

* external services,
* financial,
* publication,
* infrastructure.

Examples:

* Publish release.
* Deploy production.
* Send email.
* API mutation.
* Cloud infrastructure.

Default approval: L3

⸻

R4 — Constitutional / Critical

Characteristics:

* destructive,
* irreversible,
* security root,
* constitutional boundary.

Examples:

* Modify Constitution.
* Disable Audit.
* Change root identity.
* Security policy rewrite.

Default approval: DENY

⸻

Approval Levels

Veda supports five approval levels.

L0 — Autonomous Safe

Requirements:

* R0 only.
* Within policy scope.
* Audit generated.

Human interaction is not required.

Examples:

* Read documentation.
* Search books.
* Analyze code.

⸻

L1 — Autonomous Scoped

Requirements:

* R1.
* Inside approved workspace.
* Capability lease valid.

Examples:

* Create markdown.
* Modify development files.
* Run formatter.

Audit is mandatory.

⸻

L2 — Notify Human

Requirements:

* Moderate impact.
* Allowed by persistent policy.

Execution proceeds.

Chronicle notifies human.

Examples:

* Commit Git.
* Create branch.
* Install development dependency.

⸻

L3 — Explicit Human Approval

Requirements:

* Significant risk.
* External effect.
* Persistent modification.

Execution waits for approval.

Examples:

* Push repository.
* Delete project.
* Deploy server.
* Upload sensitive files.

⸻

L4 — Constitution Protected

Requirements:

* Constitutional boundary.
* Security root.
* Identity root.
* Audit root.

Execution is blocked.

Requires governance procedure.

⸻

Resource Scope Model

Permissions MUST include resource scopes.

Workspace

/veda/workspace/**

Temporary development files.

⸻

Projects

/veda/projects/*

Project source code.

⸻

Brain

/veda/brain/**

Knowledge library.

⸻

Memory

/veda/memory/**

Persistent memories.

⸻

Logs

/veda/logs/**

Append-only logs.

⸻

Cache

/veda/cache/**

Disposable cache.

⸻

System

Examples:

/etc
/usr
/boot
/var/lib

Restricted resources.

⸻

Scope Rules

Example:

scope:
  capability: filesystem.write
  allow:
    - /veda/projects/runtime/**
    - /veda/workspace/**
  deny:
    - /boot/**
    - /etc/**

Rules:

* deny overrides allow.
* scopes are path-aware.
* scopes may include tags and entity identifiers in future RFCs.

⸻

Actor Classes

Permissions differ by actor type.

Actor	Default Authority
Human Owner	Root constitutional authority
Human Delegate	Scoped authority
Veda Core	Constitutional executor
Coding Agent	Development scope
Research Agent	Read-heavy scope
Security Agent	Audit and diagnostics only
External MCP Tool	Explicit capability only
External AI Model	Intelligence only

External AI models have zero implicit capability authority.

⸻

Capability Lease

Capabilities SHOULD be temporary whenever possible.

Example:

lease:
  id: lease_runtime_write
  actor: coding-agent
  capability: filesystem.write
  scope:
    - /veda/projects/runtime/**
  expires: 2026-09-20T18:00:00Z
  max_operations: 100
  risk_limit: R1

Lease fields:

Field	Meaning
expires	Expiration timestamp
max_operations	Usage limit
risk_limit	Highest allowed risk
scope	Resource scope
renewable	Whether renewal is allowed

Expired leases MUST revoke capability immediately.

⸻

Permission Evaluation Algorithm

Pseudo specification:

fn evaluate(action: Action) -> PermissionResult {
    let capability = registry.lookup(action.capability)?;
    validate_scope(action.target)?;
    let risk = classify(action);
    validate_policy(action.actor, capability, risk)?;
    validate_lease(action.actor, capability)?;
    validate_time_window(action)?;
    approval_engine(action, risk)
}

Possible results:

* ALLOW
* NOTIFY
* REQUIRE_APPROVAL
* DENY
* LEASE_EXPIRED
* OUT_OF_SCOPE

⸻

Permission Matrix

Capability	Risk	Default	Audit
filesystem.read	R0	ALLOW	Yes
filesystem.write	R1	ALLOW (Scoped)	Yes
filesystem.delete	R2	REQUIRE APPROVAL	Yes
process.execute	R1	ALLOW (Sandbox)	Yes
process.kill	R2	REQUIRE APPROVAL	Yes
network.request	R1	ALLOW	Yes
network.upload	R2	REQUIRE APPROVAL	Yes
github.commit	R1	ALLOW	Yes
github.push	R2	NOTIFY / APPROVAL	Yes
docker.run	R1	ALLOW	Yes
system.package.install	R2	APPROVAL	Yes
system.reboot	R3	APPROVAL	Yes
security.modify	R4	DENY	Yes
constitution.modify	R4	DENY	Yes

⸻

Policy Resolution

Policies are evaluated in order.

Priority:

1. Constitution
2. Security Policy
3. Human Policy
4. Workspace Policy
5. Project Policy
6. Agent Policy
7. Temporary Lease

Higher priority overrides lower priority.

⸻

Revocation Rules

Capabilities MAY be revoked because of:

* lease expiration,
* policy update,
* security incident,
* human action,
* emergency mode.

Revocation creates an Event.

⸻

Emergency Modes

Safe Mode

* Disable write capabilities.
* Read-only operation.

Isolation Mode

* Disable external network.
* Disable external MCP tools.

Lockdown Mode

* Disable all consequential capabilities.
* Audit remains active.

Transition into emergency mode MUST generate a security event.

⸻

Audit Requirements

Every permission evaluation MUST create an audit record.

Minimum fields:

audit:
  trace_id:
  action_id:
  actor:
  capability:
  scope:
  risk:
  approval_level:
  result:
  timestamp:

Audit results include:

* ALLOWED
* DENIED
* EXPIRED
* REVOKED
* EXECUTED
* VERIFIED
* ROLLBACK

⸻

Compliance Examples

Example A — Create Markdown File

Capability:

filesystem.write

Scope:

/veda/workspace/docs/

Risk:

R1

Result:

ALLOW

⸻

Example B — Push GitHub

Capability:

github.push

Risk:

R2

Approval:

L3

Result:

Execution waits for approval.

⸻

Example C — Delete Boot Directory

Capability:

filesystem.delete

Target:

/boot

Result:

DENY

Audit is generated.

⸻

Example D — Install Rust in Workspace Container

Capability:

system.package.install

Target:

Development container.

Risk:

R2

Result:

Requires approval.

⸻

Security Invariants

The following MUST remain true.

ID	Invariant
PM-1	Every consequential action requires capability evaluation.
PM-2	Scope validation occurs before execution.
PM-3	Deny rules override allow rules.
PM-4	Expired leases invalidate permissions.
PM-5	Every permission decision is auditable.
PM-6	External intelligence has zero implicit authority.
PM-7	Constitution-protected capabilities cannot execute autonomously.
PM-8	Human override may revoke any non-constitutional lease immediately.

⸻

Relationship to Future RFCs

This RFC defines the enforcement layer.

Future RFC dependencies:

* RFC-0002 — World Model (resource entities).
* RFC-0003 — Event Model (audit events).
* RFC-0008 — Action Model (permission evaluation input).
* RFC-0009 — Capability Registry.
* RFC-0010 — Authorization Policy Engine.
* RFC-0031 — Event & Audit Fabric.
* RFC-0033 — Identity & Trust Model.

⸻

Versioning

Version: 0.1.0

Future versions may extend capability categories and policy syntax without violating RFC-0001 constitutional invariants.

⸻

Status

Draft v0.1.0

This document defines the constitutional authorization layer that every future Veda Runtime, Agent, MCP tool, and Veda OS component MUST implement before executing consequential actions.
