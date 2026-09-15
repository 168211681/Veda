RFC-0011: Capability Lease & Token

Status: Draft
Version: v0.1.0
Layer: Layer 4 — Authority
Module: Temporary Authority / Execution Credential
Depends On: RFC-0001, RFC-0008, RFC-0009, RFC-0010
Related: RFC-0031, RFC-0039, RFC-0040, RFC-0041

⸻

1. Abstract

RFC-0011 defines the Capability Lease & Token Model for Veda.

A Capability defines what the system can technically perform.

An Authorization defines whether a specific operation is permitted.

A Capability Lease defines a temporary, bounded, revocable grant allowing a subject to exercise an authorized capability within a specific scope and time window.

A Token is a machine-verifiable representation of a lease that may be presented to an executor or capability provider.

The fundamental distinction is:

Capability     = What can technically be done
Policy         = What rules apply
Authorization  = What is permitted
Lease          = What is permitted for a bounded period/scope
Token          = Proof of the lease
Action         = What operation is being attempted
Verification   = What actually happened

A token MUST NOT become an independent source of authority.

The underlying authority remains defined by the Authorization, Lease, Policy, Identity, and Capability constraints.

⸻

2. Motivation

Long-lived authority is dangerous in an autonomous system.

Consider:

Agent
  ↓
Authorization
  ↓
Capability
  ↓
Unlimited access

If the agent is compromised, misconfigured, or simply makes a bad decision, the damage may continue indefinitely.

Veda instead uses bounded authority:

Authorization
      ↓
Capability Lease
      ↓
Short-lived Token
      ↓
Action
      ↓
Capability Executor
      ↓
Verification

This provides:

* time limitation
* scope limitation
* operation limitation
* resource limitation
* risk limitation
* usage limitation
* revocation
* delegation control
* auditability
* replay protection
* reduced blast radius

The lease therefore acts as an execution boundary between abstract authorization and concrete execution.

⸻

3. Design Goals

RFC-0011 MUST provide:

1. Temporary authority.
2. Explicit scope.
3. Explicit operation restrictions.
4. Expiration.
5. Revocation.
6. Optional maximum usage.
7. Resource limits.
8. Risk limits.
9. Delegation constraints.
10. Token verification.
11. Replay protection where required.
12. Audience binding.
13. Purpose binding.
14. Auditability.
15. Authorization traceability.
16. Compatibility with local and distributed execution.
17. Protection against privilege escalation.
18. Protection against indefinite self-renewal.
19. Compatibility with human approval.
20. Compatibility with multi-agent execution.

⸻

4. Non-Goals

RFC-0011 does not define:

* user identity itself
* authentication protocols in full
* general policy semantics
* capability implementation
* tool execution
* model intelligence
* agent reasoning
* governance
* constitutional authority

These are defined by other RFCs.

⸻

5. Core Principles

5.1 Capability Is Not Authority

Capability ≠ Authorization

Having the technical ability to perform an operation does not mean the operation is permitted.

⸻

5.2 Authorization Is Not a Lease

Authorization ≠ Lease

Authorization determines whether an action may occur.

A lease provides bounded execution authority derived from that authorization.

⸻

5.3 Lease Is Not Identity

Lease ≠ Identity

A lease grants authority to a subject.

It does not establish who that subject fundamentally is.

⸻

5.4 Token Is Not Authority

Token ≠ Authority

A token is evidence that a lease exists.

The token itself MUST NOT be treated as an unrestricted authority source.

⸻

5.5 Token Possession Is Not Sufficient Identity

Possessing a token MUST NOT automatically imply trusted identity.

Where appropriate, tokens SHOULD be bound to:

* subject identity
* process identity
* agent identity
* session identity
* device identity
* cryptographic proof

⸻

5.6 No Privilege Amplification

A lease MUST NOT grant authority greater than the authority from which it was derived.

Formally:

LeaseScope ⊆ AuthorizationScope

For delegated leases:

ChildLeaseScope ⊆ ParentLeaseScope

For capability use:

EffectiveScope =
CapabilityScope
∩ AuthorizationScope
∩ LeaseScope
∩ ActionScope
∩ PolicyScope

⸻

6. Capability Lease

A Capability Lease is a bounded authority grant.

Conceptually:

Lease =
Capability
+
Authorization
+
Subject
+
Scope
+
Time
+
Conditions
+
Limits

Example:

Capability:
    filesystem.write
Authorization:
    allow coding agent to modify project files
Lease:
    subject = coding-agent-01
    scope = /workspace/project/
    operations = write, create
    expires = 10 minutes
    max_uses = 50

The agent does not receive unrestricted filesystem authority.

It receives a temporary execution lease.

⸻

7. Lease Object

A canonical Capability Lease SHOULD contain:

lease_id:
version:
issuer:
subject:
capability_ref:
operations:
scope:
purpose:
risk_limit:
resource_limits:
conditions:
issued_at:
not_before:
expires_at:
max_uses:
remaining_uses:
renewable:
renewal_policy:
revocable:
parent_lease:
delegation_chain:
authorization_ref:
policy_refs:
token_ref:
status:
created_at:
updated_at:
revoked_at:
revocation_reason:
evidence:

⸻

8. Lease Field Definitions

8.1 lease_id

Globally unique identifier.

Example:

lease-01JVEDA-7F3X

⸻

8.2 version

Schema version.

Example:

1

⸻

8.3 issuer

Identity that issued the lease.

The issuer MUST possess sufficient authority to issue that lease.

An executor MUST NOT issue a lease to itself unless explicitly authorized by the governing authority model.

⸻

8.4 subject

The entity receiving the lease.

Possible subjects:

* Veda core
* agent
* process
* session
* tool
* device
* service
* human-approved workflow

⸻

8.5 capability_ref

Reference to the capability being leased.

Example:

filesystem.write

⸻

8.6 operations

Explicit operations permitted by the lease.

Example:

operations:
  - create
  - write

The absence of an operation means it is not granted.

⸻

8.7 scope

Defines the resources to which the lease applies.

Examples:

/workspace/veda/docs/**

or:

github://168211681/Veda/docs/**

or:

database://veda/tasks/*

Scope MUST be narrower than or equal to the parent authority.

⸻

8.8 purpose

Human- and machine-readable explanation of why the lease exists.

Example:

Modify RFC documentation for the current Veda development task.

Purpose SHOULD be used for audit and policy validation.

⸻

8.9 risk_limit

Maximum risk level permitted.

Example:

risk_limit:
  level: medium

An action exceeding this level MUST be rejected or require additional authorization.

⸻

8.10 resource_limits

Limits on execution resources.

Examples:

resource_limits:
  max_cpu_seconds: 300
  max_memory_mb: 4096
  max_network_requests: 100
  max_storage_write_mb: 50

These limits reduce damage caused by runaway processes.

⸻

8.11 conditions

Conditional requirements.

Examples:

conditions:
  require_process: process-123
  require_network: trusted-network
  require_human_approval: false

Conditions MUST be evaluated before sensitive execution.

⸻

8.12 issued_at

Time at which the lease was issued.

⸻

8.13 not_before

Earliest time at which the lease becomes valid.

⸻

8.14 expires_at

Hard expiration time.

After expiration:

Lease cannot authorize new actions.

Expiration MUST NOT be silently extended.

⸻

8.15 max_uses

Maximum number of operations that may consume the lease.

Example:

max_uses = 10

A one-shot lease can use:

max_uses = 1

⸻

8.16 remaining_uses

Current remaining usage count.

This value MUST be updated atomically when the lease is consumable.

⸻

8.17 renewable

Whether renewal is permitted.

renewable = false

SHOULD be the safer default for high-risk leases.

⸻

8.18 renewal_policy

Defines when and how renewal may occur.

Renewal MUST NOT create indefinite authority.

Example:

renewal_policy:
  max_renewals: 3
  max_total_duration: 30m
  require_reauthorization: true

⸻

8.19 revocable

Defines whether the lease can be revoked before expiration.

High-risk leases SHOULD always be revocable.

⸻

8.20 parent_lease

Reference to the lease from which this lease was delegated.

⸻

8.21 delegation_chain

Records the chain of authority.

Example:

Human
 ↓
Veda
 ↓
Coding Agent
 ↓
Process
 ↓
Tool

Every delegation MUST remain within the original authority boundary.

⸻

8.22 authorization_ref

Reference to RFC-0010 authorization.

A lease MUST NOT exist without an appropriate authorization basis.

⸻

8.23 policy_refs

Policies used when issuing the lease.

⸻

8.24 token_ref

Reference to token representations associated with the lease.

A lease may exist without exposing a token.

⸻

9. Lease State Machine

The canonical lifecycle is:

REQUESTED
    ↓
ISSUED
    ↓
ACTIVE
    ↓
 ┌──┼──────────────┐
 ↓  ↓              ↓
SUSPENDED       CONSUMED
 ↓
ACTIVE
ACTIVE
 ↓
EXPIRED
ACTIVE
 ↓
REVOKED

Possible terminal states:

DENIED
REJECTED
EXPIRED
CONSUMED
REVOKED

⸻

10. Lease States

10.1 REQUESTED

A lease has been requested but not yet issued.

⸻

10.2 ISSUED

The lease has been created and authorized but is not yet active.

⸻

10.3 ACTIVE

The lease may be used.

⸻

10.4 SUSPENDED

Temporary execution is prohibited.

The lease may later return to ACTIVE if policy permits.

⸻

10.5 CONSUMED

The lease’s usage limit has been exhausted.

⸻

10.6 EXPIRED

The lease exceeded its expiration time.

⸻

10.7 REVOKED

The lease was explicitly invalidated.

Revocation is stronger than expiration because it can occur before the natural expiration time.

⸻

11. Lease Issuance

The issuance flow SHOULD be:

Lease Request
      ↓
Identity Validation
      ↓
Capability Validation
      ↓
Authorization Lookup
      ↓
Policy Evaluation
      ↓
Scope Validation
      ↓
Risk Evaluation
      ↓
Resource Limit Evaluation
      ↓
Delegation Validation
      ↓
Lease Creation
      ↓
Token Issuance
      ↓
Audit Event

No lease may bypass authorization.

⸻

12. Lease Request

A request SHOULD contain:

subject:
capability_ref:
operations:
scope:
purpose:
requested_duration:
requested_limits:
risk_tolerance:
parent_process:
parent_action:
authorization_ref:

The requested lease is not automatically granted.

⸻

13. Scope Reduction

The issuer SHOULD reduce requested scope to the minimum necessary.

Example:

Requested:

/workspace/**

Required:

/workspace/veda/docs/rfc/**

The resulting lease SHOULD use:

/workspace/veda/docs/rfc/**

Least privilege is preferable to trusting an agent to “behave.”

Agents are software. Software does not have a conscience.

⸻

14. Effective Lease Scope

The effective lease scope is the intersection of all applicable constraints:

EffectiveLeaseScope =
CapabilityScope
∩ AuthorizationScope
∩ ParentLeaseScope
∩ PolicyScope
∩ RequestedScope

If the intersection is empty:

Lease = DENIED

⸻

15. Time Constraints

A lease MUST support:

not_before
expires_at

Optional:

maximum_duration
idle_timeout
absolute_deadline

High-risk operations SHOULD use short expiration periods.

⸻

16. Usage Constraints

A lease MAY restrict:

* number of actions
* number of requests
* amount of data
* CPU
* memory
* network traffic
* monetary value
* storage
* execution duration

Example:

max_uses: 5
resource_limits:
  max_write_mb: 10
  max_network_requests: 20

⸻

17. Risk Limits

Every lease MAY define a maximum risk level.

Example:

LOW
MEDIUM
HIGH
CRITICAL

If:

ActionRisk > LeaseRiskLimit

then:

DENY

or:

REQUIRE_APPROVAL

depending on policy.

⸻

18. Purpose Binding

A lease SHOULD be purpose-bound.

Example:

Purpose:
"Modify Veda RFC documentation."

A lease issued for documentation editing MUST NOT automatically become valid for unrelated operations.

Purpose binding reduces authority reuse outside the intended workflow.

⸻

19. Process Binding

A lease SHOULD optionally bind to a Process.

Example:

lease.process_id = process-abc

This prevents one process from freely transferring authority to unrelated processes.

⸻

20. Action Binding

A high-risk lease MAY be bound to a specific Action.

Example:

lease.action_id = action-xyz

This is appropriate for one-time operations.

Example:

Human Approval
      ↓
One Action
      ↓
One Lease
      ↓
Execution

⸻

21. Token Model

A Token is a machine-verifiable representation of a lease.

The conceptual model is:

Lease
  ↓
Token
  ↓
Executor
  ↓
Validation

The token SHOULD contain only the minimum information necessary for validation.

⸻

22. Token Object

A canonical token SHOULD contain:

token_id:
token_type:
lease_id:
issuer:
subject:
capability_ref:
operations:
scope:
issued_at:
expires_at:
nonce:
audience:
proof:
version:

⸻

23. Token ID

Every token MUST have a unique identifier.

Example:

token-01JVEDA-93KF

⸻

24. Token Type

The token type identifies the representation and verification mechanism.

Examples:

VedaLeaseToken
VedaSignedCapabilityToken
VedaSessionToken
VedaOneShotToken

The exact cryptographic encoding is implementation-specific.

⸻

25. Audience Binding

Tokens SHOULD be bound to an intended audience.

Example:

audience = filesystem-executor

A token issued for one executor MUST NOT automatically work with another executor.

⸻

26. Nonce

Tokens SHOULD contain a unique nonce where replay protection is required.

Example:

nonce = cryptographically-random-value

For one-shot high-risk operations, nonce tracking SHOULD be mandatory.

⸻

27. Token Proof

A token MUST provide verifiable proof of issuance.

Possible mechanisms include:

* digital signatures
* MACs
* secure local references
* hardware-backed proofs

The implementation MUST choose a mechanism appropriate to its trust model.

⸻

28. Token Expiration

Tokens MUST NOT outlive the lease.

Therefore:

TokenExpiry ≤ LeaseExpiry

A token with a longer expiration than its lease is invalid.

⸻

29. Token Validation

Before an action uses a token, the executor SHOULD validate:

1. Token structure
2. Token signature/proof
3. Token issuer
4. Token subject
5. Token audience
6. Token expiration
7. Token not-before
8. Lease existence
9. Lease status
10. Lease expiration
11. Capability status
12. Operation scope
13. Resource limits
14. Risk limit
15. Policy conditions
16. Replay state
17. Revocation state

Failure of any mandatory validation MUST prevent execution.

⸻

30. Lease vs Token

The relationship is:

Authorization
      ↓
     Lease
      ↓
    Token
      ↓
   Executor

The lease is the authority object.

The token is the execution proof.

This distinction is important.

A compromised token should not permanently redefine the authority model.

⸻

31. Token Revocation

Tokens MAY be invalidated through:

Lease Revocation
Token Revocation
Subject Revocation
Capability Revocation
Policy Change
Security Incident

Lease revocation SHOULD invalidate associated tokens.

⸻

32. Revocation Propagation

If:

Parent Lease
    ↓
Child Lease
    ↓
Token

and the parent lease is revoked:

Parent Lease = REVOKED
        ↓
Child Lease = REVOKED
        ↓
Token = INVALID

Propagation MUST be auditable.

⸻

33. Delegation

A lease MAY permit delegation.

Delegation MUST be explicit.

A child lease MUST satisfy:

ChildScope ⊆ ParentScope
ChildOperations ⊆ ParentOperations
ChildRisk ≤ ParentRiskLimit
ChildExpiry ≤ ParentExpiry
ChildResourceLimits ≤ ParentResourceLimits

The child MUST NOT gain authority unavailable to the parent.

⸻

34. No Recursive Privilege Expansion

The following is forbidden:

Parent
 ↓
Child
 ↓
Child expands scope
 ↓
Grandchild gains more authority

Authority can only remain equal or decrease.

Formally:

Authority(child) ≤ Authority(parent)

⸻

35. Renewal

Renewal is not automatic infinite extension.

A renewal request SHOULD be evaluated as a new authority decision.

Example:

Lease
 ↓
Renewal Request
 ↓
Policy Evaluation
 ↓
Current World Check
 ↓
Risk Check
 ↓
Authorization Revalidation
 ↓
Renew

For high-risk leases:

require_reauthorization = true

SHOULD be the default.

⸻

36. Maximum Lifetime

A lease SHOULD define:

maximum_duration

Even renewable leases MUST have an upper lifetime unless governance explicitly permits otherwise.

Example:

expires_at: 10:10
maximum_total_duration: 30m

The agent cannot keep renewing itself forever.

⸻

37. One-Shot Lease

A one-shot lease is designed for exactly one operation.

Example:

max_uses: 1
renewable: false
expires_at: +60s

Recommended for:

* destructive operations
* privileged configuration
* credential rotation
* sensitive external actions
* high-risk system changes

⸻

38. Session Lease

A session lease may cover a bounded interactive session.

Example:

Duration: 30 minutes
Scope: /workspace/veda/
Operations: read/write
Risk: medium

The session MUST still obey all underlying policy constraints.

⸻

39. Task Lease

A task lease is associated with a specific Process or Goal.

Example:

Goal:
Implement RFC-0011
Process:
process-0011
Lease:
filesystem.write
scope = docs/rfc/**

When the process terminates, the lease SHOULD be automatically revoked or expired.

⸻

40. Recurring Lease

Recurring leases MAY exist but SHOULD be treated as higher risk than one-time leases.

They MUST define:

* schedule
* maximum lifetime
* maximum execution count
* scope
* risk limits
* renewal policy
* revocation mechanism

A recurring lease MUST NOT become unlimited authority.

⸻

41. Human Approval

A high-risk operation may follow:

Action
 ↓
Authorization
 ↓
Human Approval
 ↓
Short-Lived Lease
 ↓
One-Shot Token
 ↓
Execution
 ↓
Verification

The approval MUST become an auditable event.

Human approval MUST NOT silently grant broader authority than the requested action.

⸻

42. Emergency / Break-Glass

Veda MAY support emergency leases.

Emergency authority MUST be:

* explicitly marked
* strongly audited
* time-limited
* scope-limited
* reason-bound
* reviewed afterward

Example:

emergency: true
reason: "Recover failed storage subsystem"
expires_at: +5m

Emergency mode MUST NOT disable constitutional constraints.

⸻

43. Secret Protection

Raw tokens MUST NOT be freely exposed to intelligence models.

For example:

Model
  ↓
"Give me the GitHub token."

The capability system SHOULD instead provide:

Model
  ↓
Request capability
  ↓
Authority Engine
  ↓
Secure Executor
  ↓
GitHub

Secrets should remain inside the secure execution boundary whenever possible.

⸻

44. Token Storage

Tokens SHOULD be stored using secure mechanisms appropriate to the platform.

Possible protections include:

* encrypted storage
* operating-system keychain
* hardware-backed storage
* memory isolation
* short lifetime
* automatic destruction

Tokens SHOULD NOT be placed into:

* general conversation memory
* unrestricted logs
* model prompts
* plaintext debug output
* public artifacts

⸻

45. Replay Protection

For sensitive operations, the system SHOULD prevent token replay.

Possible mechanisms:

nonce
+
token ID tracking
+
action ID
+
idempotency key
+
usage counter

A one-shot token MUST NOT be reusable after successful consumption.

⸻

46. Clock Handling

Distributed systems may have clock differences.

Lease validation SHOULD account for controlled clock skew.

Where necessary:

not_before - clock_skew
expires_at + controlled_grace

Grace periods MUST NOT create meaningful security bypasses.

High-risk operations SHOULD rely on a trusted time source where available.

⸻

47. Offline Verification

Some local capabilities may need offline verification.

In such environments:

* tokens MUST have short lifetimes
* cached authority MUST be bounded
* revocation information SHOULD have freshness limits
* offline mode MUST NOT silently expand authority

If revocation cannot be checked for a high-risk operation, the system SHOULD fail closed.

⸻

48. Lease Use Flow

The canonical flow is:

Action Proposed
      ↓
Authorization Check
      ↓
Lease Requested
      ↓
Lease Issued
      ↓
Token Issued
      ↓
Action Precheck
      ↓
Token Validation
      ↓
Lease Validation
      ↓
Capability Validation
      ↓
Policy Revalidation
      ↓
Execution
      ↓
Observation
      ↓
Verification
      ↓
Lease Usage Update
      ↓
Audit

⸻

49. Lease Consumption

When a lease has usage limits:

remaining_uses = remaining_uses - 1

This operation MUST be atomic.

Concurrent execution MUST NOT allow:

max_uses = 1

to result in multiple successful executions.

⸻

50. Concurrency

When multiple processes attempt to consume the same lease:

Lease
 ├── Action A
 └── Action B

the lease manager MUST enforce its usage and resource constraints atomically.

Possible mechanisms:

* atomic counters
* transactional locks
* compare-and-swap
* serialized lease consumption

⸻

51. Lease and Action

RFC-0008 Action MAY reference:

authorization_ref:
lease_ref:
token_ref:

The relationships are:

Authorization
    ↓
Lease
    ↓
Token
    ↓
Action

An Action MUST NOT treat possession of a token as permission to bypass its authorization requirements.

⸻

52. Lease and Capability

The lease references a capability:

Lease
  ↓
Capability

The executor MUST verify that the capability is still:

AVAILABLE

or otherwise valid under its lifecycle rules.

A revoked capability invalidates dependent leases.

⸻

53. Lease and Process

A lease SHOULD be associated with a Process where practical.

Example:

Process P1
    ↓
Lease L1
    ↓
Action A1

If:

Process P1 = CANCELLED

then:

Lease L1

SHOULD be suspended or revoked unless policy explicitly permits continuation.

⸻

54. Lease and World State

Lease validity may depend on World state.

Example:

Lease:
"Modify production configuration only while maintenance mode is active."

If:

maintenance_mode = false

then the lease becomes unusable even if it has not expired.

Therefore:

Lease Validity =
Time
∩ Authorization
∩ Policy
∩ Capability
∩ Conditions
∩ World State

⸻

55. Policy Revalidation

Long-lived leases SHOULD periodically revalidate policy.

High-risk actions SHOULD revalidate immediately before execution.

This protects against:

Policy changed
      ↓
Old lease still active
      ↓
Old authority incorrectly continues

⸻

56. Lease Violation

A violation occurs when a subject attempts to use a lease outside its limits.

Examples:

wrong capability
wrong operation
wrong target
wrong scope
expired lease
revoked lease
wrong audience
excessive risk
excessive resource usage
invalid process
invalid purpose

The system MUST reject the operation.

Repeated violations MAY trigger:

lease suspension
agent suspension
capability quarantine
security review

⸻

57. Lease Events

The following events SHOULD be supported:

LeaseRequested
LeaseIssued
LeaseActivated
LeaseSuspended
LeaseResumed
LeaseUsed
LeaseRenewalRequested
LeaseRenewed
LeaseRevoked
LeaseExpired
LeaseConsumed
LeaseViolationDetected
LeaseDelegated
LeaseValidationFailed
TokenIssued
TokenValidated
TokenRejected
TokenRevoked
TokenReplayDetected

All events SHOULD reference:

actor
subject
lease_id
action_id
process_id
authorization_id
timestamp
reason
result

⸻

58. Audit Requirements

Every sensitive lease lifecycle event MUST be auditable.

Minimum trace:

Intent
 ↓
Goal
 ↓
Process
 ↓
Action
 ↓
Authorization
 ↓
Lease
 ↓
Token
 ↓
Capability
 ↓
Execution
 ↓
Verification
 ↓
World Transition

This allows Veda to answer:

Why was this action allowed?

and:

Who or what granted the authority?

and:

How long was that authority valid?

and:

What actually happened?

⸻

59. Failure Handling

Lease failures SHOULD be classified.

Examples:

LEASE_EXPIRED
LEASE_REVOKED
LEASE_SUSPENDED
LEASE_SCOPE_DENIED
LEASE_OPERATION_DENIED
LEASE_RISK_EXCEEDED
LEASE_RESOURCE_EXCEEDED
LEASE_AUDIENCE_MISMATCH
LEASE_SUBJECT_MISMATCH
LEASE_CAPABILITY_REVOKED
LEASE_POLICY_CHANGED
LEASE_PARENT_REVOKED
LEASE_REPLAY_DETECTED
LEASE_INVALID_TOKEN
LEASE_CONDITION_FAILED

Failure MUST NOT silently downgrade into unrestricted execution.

⸻

60. Security Model

The lease system MUST defend against:

60.1 Privilege Escalation

Low authority → High authority

Forbidden.

⸻

60.2 Scope Expansion

Lease A
scope = /project/a

MUST NOT become:

/project/**

without a new authorization decision.

⸻

60.3 Token Theft

A stolen token SHOULD have limited:

* lifetime
* scope
* audience
* operations
* risk
* usage

⸻

60.4 Token Replay

Sensitive one-shot tokens MUST support replay protection.

⸻

60.5 Self-Issuance

An agent MUST NOT create an authority lease granting itself new authority.

⸻

60.6 Self-Renewal

An agent MUST NOT indefinitely renew its own authority without an independent authorization mechanism.

⸻

60.7 Revocation Bypass

Revoked leases MUST NOT remain usable because a cached token still exists.

⸻

60.8 Delegation Abuse

Delegation MUST never increase authority.

⸻

61. Trust vs Lease

Trust does not replace authorization.

Trust = How much Veda believes a subject is reliable
Lease = What bounded authority the subject currently has

A highly trusted agent may still receive a narrow lease.

A low-trust agent may receive no lease.

⸻

62. Confidence vs Lease

Model confidence MUST NOT create authority.

Confidence = Belief about correctness
Lease = Bounded execution authority

A model saying:

"I am 99% certain."

does not authorize anything.

⸻

63. Urgency vs Lease

Urgency MUST NOT automatically expand authority.

Urgent ≠ Authorized

Emergency policies must explicitly define when urgency can affect authorization.

⸻

64. Lease Risk Classes

Veda SHOULD classify leases.

L0 — Read Only

Examples:

filesystem.read
knowledge.search
world.read

Minimal risk.

L1 — Reversible Write

Examples:

create file
edit document
temporary configuration

L2 — External Side Effect

Examples:

send message
publish content
external API mutation

L3 — Privileged System Action

Examples:

system configuration
credential changes
security settings

L4 — Critical Authority

Examples:

constitutional changes
core security changes
architecture self-modification

L4 SHOULD require strong governance and MUST NOT be granted merely because an agent requests it.

⸻

65. Example: Documentation Agent

lease_id: lease-doc-001
subject: agent-coder
capability_ref: filesystem.write
operations:
  - create
  - write
scope:
  - /workspace/veda/docs/rfc/**
purpose:
  "Create RFC documentation"
issued_at: 2026-09-16T04:00:00
expires_at: 2026-09-16T04:10:00
max_uses: 20
risk_limit:
  level: medium
renewable: false
authorization_ref:
  auth-001

The agent can modify RFC files.

It cannot use this lease to:

modify /etc
delete the repository
access credentials
change security policy
modify the Veda constitution

⸻

66. Example: One-Time High-Risk Action

Action
  ↓
Risk = HIGH
  ↓
Human Approval
  ↓
Authorization
  ↓
One-Shot Lease
  ↓
Token
  ↓
Action Execution
  ↓
Verification
  ↓
Lease Consumed

The authority disappears after use.

⸻

67. Example: Delegated Agent

Human
  ↓
Veda
  ↓
Coding Agent
  ↓
Process
  ↓
Tool

If Veda has:

filesystem.write
scope = /workspace/project/

then the coding agent might receive:

filesystem.write
scope = /workspace/project/src/

The agent MUST NOT receive:

scope = /

⸻

68. Example: Revocation

Initial:

Lease = ACTIVE
Token = VALID

Security event occurs:

Capability compromised

System performs:

Capability → REVOKED
      ↓
Lease → REVOKED
      ↓
Token → INVALID
      ↓
Future Action → DENIED

The event is recorded in the audit system.

⸻

69. Example: Process Termination

Process P1
    ↓
Lease L1
    ↓
Token T1

If:

Process P1 = CANCELLED

then the system SHOULD automatically evaluate:

Should L1 remain active?

Default:

NO

unless the lease explicitly allows independent continuation.

⸻

70. Lease Caching

Lease information MAY be cached for performance.

However:

Cache ≠ Authority

Cached data MUST respect:

* expiration
* revocation freshness
* policy version
* capability status

High-risk operations SHOULD avoid stale authority decisions.

⸻

71. Deterministic Validation

Given the same:

Lease
Token
Policy
Capability
World State
Time
Action

the validation result SHOULD be deterministic.

This supports:

* debugging
* replay
* auditing
* simulation
* security testing

⸻

72. Simulation

Lease validation SHOULD support dry-run.

Example:

Would this lease allow Action A?

Result:

allowed: false
reason:
  - scope_violation
lease_scope:
  /workspace/veda/docs/**
action_target:
  /workspace/veda/secrets/**

Simulation MUST NOT consume a one-shot lease.

⸻

73. Observability

The system SHOULD expose:

active leases
expired leases
revoked leases
leases by subject
leases by process
leases by capability
leases by risk
token usage
violations
renewals

Sensitive token values MUST NOT be displayed.

⸻

74. Performance

Lease validation SHOULD be inexpensive enough for frequent local actions.

Possible architecture:

Local Lease Cache
       ↓
Fast Validation
       ↓
Authoritative Revalidation
       ↓
Sensitive Execution

Caching MUST NOT bypass security requirements.

⸻

75. Compatibility

RFC-0011 MUST integrate with:

RFC-0001 Constitution
RFC-0008 Action Model
RFC-0009 Capability Model
RFC-0010 Authorization & Policy
RFC-0028 Tool & Capability Registry
RFC-0031 Event/Audit/Trace Fabric
RFC-0039 Veda Identity
RFC-0040 Agent Passport
RFC-0041 Trust Engine

⸻

76. Security Invariants

The following invariants MUST hold.

LEASE-1

Every lease MUST have a unique identifier.

LEASE-2

Every lease MUST reference an authorization basis.

LEASE-3

A lease MUST NOT exceed its authorization scope.

LEASE-4

A delegated lease MUST NOT exceed its parent lease.

LEASE-5

A lease MUST have a bounded validity period unless explicitly governed otherwise.

LEASE-6

Expired leases MUST NOT authorize new actions.

LEASE-7

Revoked leases MUST NOT authorize new actions.

LEASE-8

Suspended leases MUST NOT authorize prohibited actions.

LEASE-9

A token MUST NOT outlive its lease.

LEASE-10

A token MUST NOT expand lease authority.

LEASE-11

Token possession MUST NOT automatically imply unrestricted identity.

LEASE-12

An executor MUST NOT self-authorize.

LEASE-13

An agent MUST NOT self-grant broader authority.

LEASE-14

An agent MUST NOT indefinitely self-renew authority.

LEASE-15

Delegation MUST NOT increase authority.

LEASE-16

High-risk leases SHOULD be short-lived.

LEASE-17

High-risk operations SHOULD require policy revalidation.

LEASE-18

One-shot leases MUST NOT be reusable.

LEASE-19

Lease usage counters MUST be concurrency-safe.

LEASE-20

Lease revocation MUST propagate to dependent execution credentials.

LEASE-21

Capability revocation MUST invalidate dependent lease usage.

LEASE-22

Policy changes MUST be able to invalidate future lease usage where required.

LEASE-23

Lease scope MUST be explicitly represented.

LEASE-24

Lease operations MUST be explicitly represented.

LEASE-25

Lease expiration MUST be auditable.

LEASE-26

Lease issuance MUST be auditable.

LEASE-27

Lease revocation MUST be auditable.

LEASE-28

Token validation failures MUST be auditable when security-relevant.

LEASE-29

Raw tokens MUST NOT be written into unrestricted logs.

LEASE-30

A token MUST NOT become an independent authority source.

⸻

77. Canonical Authority Chain

The canonical Veda authority chain is:

Human / Governance
        ↓
Constitution
        ↓
Policy
        ↓
Authorization
        ↓
Capability Lease
        ↓
Token
        ↓
Capability
        ↓
Action
        ↓
Executor
        ↓
Verification
        ↓
World

Each layer has a distinct responsibility.

⸻

78. Conceptual Separation

The complete separation is:

Identity
    = Who is acting?
Capability
    = What can technically be done?
Policy
    = What rules apply?
Authorization
    = What is permitted?
Lease
    = What is permitted within a bounded authority window?
Token
    = What proof can be presented to an executor?
Intent
    = What does the user want?
Goal
    = What should become true?
Action
    = What exact operation is being attempted?
Execution
    = What was attempted?
Verification
    = What actually happened?
World
    = What is true now?

⸻

79. Relationship to Veda’s Core Loop

RFC-0011 integrates into the Veda cognitive-execution loop:

Reality
   ↓
Observe
   ↓
World Model
   ↓
Attention
   ↓
Intent
   ↓
Goal
   ↓
Planner
   ↓
Future / Simulation
   ↓
Decision
   ↓
Action
   ↓
Authorization
   ↓
Lease
   ↓
Token
   ↓
Capability
   ↓
Execution
   ↓
Observation
   ↓
Verification
   ↓
World Update
   ↓
Audit
   ↓
Experience

The critical boundary is:

Thinking
   ↓
Authority
   ↓
Execution

Thinking alone never creates authority.

⸻

80. Relationship to Human Control

Veda may become highly autonomous.

However:

Autonomy ≠ Unlimited Authority

The lease system provides a mechanism through which Veda can operate autonomously while remaining bounded.

Instead of:

"Veda may do anything."

the system becomes:

"Veda may perform these operations,
against these resources,
for this purpose,
within this time,
under these constraints."

That is the correct foundation for safe autonomy.

⸻

81. Future Extensions

Future RFCs MAY define:

* hardware-backed capability leases
* distributed lease consensus
* cryptographic token formats
* zero-knowledge authorization proofs
* capability escrow
* multi-party leases
* quorum authorization
* geographic restrictions
* device-bound leases
* biometric approval
* hardware security modules
* secure enclaves
* remote attestation
* cross-agent lease federation

These MUST preserve the core invariants of RFC-0011.

⸻

82. Final Principle

Veda should never operate under the assumption:

"I can do it,
therefore I may do it."

Instead:

I can do it
    ↓
Capability
I am permitted to do it
    ↓
Authorization
I may do it now,
within this scope,
for this purpose,
under these limits
    ↓
Lease
I can prove that bounded authority
to the executor
    ↓
Token
I attempt the operation
    ↓
Action
The system observes what happened
    ↓
Verification
The World is updated only after verification

The fundamental rule is:

Capability ≠ Authority
Authority ≠ Permanence
Token ≠ Authority
Possession ≠ Permission
Autonomy ≠ Unlimited Power

And therefore:

Authorization
    →
Bounded Lease
    →
Verifiable Token
    →
Capability Execution
    →
Verification

is the canonical execution-authority model for Veda.

⸻

End of RFC-0011