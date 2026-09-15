RFC-0010: Authorization & Policy

Status: Draft
Version: 0.1.0
Layer: Layer 4 — Authority
Module: Authorization & Policy
Path: docs/rfc/RFC-0010-authorization-and-policy.md

⸻

1. Abstract

RFC-0010 defines the Authorization and Policy Model of Veda.

Authorization determines whether a proposed Action is permitted to execute within a specific context.

Policy defines the rules under which authorization decisions are made.

The fundamental separation is:

Capability = What can technically be done
Authorization = What is permitted now
Policy = Why it is permitted or denied

Therefore:

Capability
    ↓
Policy Evaluation
    ↓
Authorization Decision
    ↓
Action Execution

Authorization is a security boundary between Veda’s cognitive system and its ability to affect the World.

No amount of intelligence, urgency, confidence, or model capability may bypass this boundary.

⸻

2. Motivation

Veda will eventually have capabilities such as:

filesystem
terminal
browser
network
Git
GitHub
database
cloud infrastructure
devices
financial systems
communication systems
model providers

Possessing these capabilities does not mean Veda should always use them.

For example:

Capability:
filesystem.delete
Target:
/veda/docs/test.md
Possible authorization:
DENY

or:

Capability:
filesystem.delete
Target:
/veda/tmp/test.md
Authorization:
ALLOW

The technical capability is identical.

The policy context is different.

Therefore authorization MUST be contextual.

⸻

3. Core Principle

Veda must never confuse the ability to perform an operation with permission to perform it.

Formally:

Capability ≠ Authorization

And:

Authorization ≠ Goal

A Goal describes what should become true.

Authorization determines whether Veda may perform a particular Action to pursue that Goal.

⸻

4. Authority Hierarchy

Authorization decisions SHOULD follow this conceptual hierarchy:

Constitution
    ↓
Safety Constraints
    ↓
Human Authority
    ↓
System Policy
    ↓
Agent Authority
    ↓
Capability Scope
    ↓
Action Scope
    ↓
Execution

Higher-level restrictions MUST NOT be overridden by lower-level instructions.

⸻

5. Authorization Object

A canonical Authorization SHOULD contain:

authorization_id:
version:
issuer:
subject:
action_id:
capability_ref:
scope:
operations:
targets:
purpose:
policy_refs:
decision:
reason:
risk:
approval_level:
issued_at:
expires_at:
conditions:
delegation:
lease_ref:
evidence:
status:

⸻

6. Authorization Identity

Every authorization MUST have a unique:

authorization_id

Example:

auth_01JVEDA...

The identity MUST remain stable throughout the authorization lifecycle.

⸻

7. Issuer

The issuer identifies the authority that issued the authorization.

Possible issuers:

human
policy_engine
system_governance
authorized_agent

An ordinary AI reasoning process MUST NOT automatically become an authorization issuer.

⸻

8. Subject

The subject identifies who or what receives authorization.

Examples:

Veda
ResearchAgent
CodingAgent
FilesystemExecutor

Authorization MUST be bound to an identifiable subject.

⸻

9. Action Binding

Authorization SHOULD reference a specific Action whenever practical.

action_id: act_001

This prevents authorization from becoming unnecessarily broad.

Prefer:

Authorize:
write /veda/docs/file.md

over:

Authorize:
everything filesystem-related

⸻

10. Capability Binding

Authorization MUST reference the required capability.

Example:

capability_ref:
  capability_id: filesystem.write

Authorization for one capability MUST NOT automatically authorize unrelated capabilities.

⸻

11. Scope

Authorization MUST define its scope.

Scope may include:

target
operation
resource
time
environment
data
network
budget
quantity

Example:

scope:
  filesystem:
    root: /veda/docs
  operations:
    - read
    - write

⸻

12. Effective Scope

The final executable scope is the intersection of all applicable restrictions:

Effective Scope =
Capability Scope
∩ Authorization Scope
∩ Lease Scope
∩ Action Scope
∩ Policy Scope

If any layer excludes the requested operation:

DENY

This prevents broad permissions from silently overriding narrower constraints.

⸻

13. Authorization Decisions

The canonical decisions are:

ALLOW
DENY
REQUIRE_APPROVAL
DEFER

Possible extended decisions:

ALLOW_WITH_CONDITIONS
ALLOW_ONCE
ALLOW_UNTIL_EXPIRY
REQUIRE_REAUTHORIZATION

⸻

14. ALLOW

The Action satisfies all required policy conditions.

Execution may proceed provided:

* capability is available;
* authorization remains valid;
* preconditions remain valid;
* scope remains valid.

⸻

15. DENY

The Action MUST NOT execute.

Denial SHOULD include a reason.

Example:

decision: DENY
reason:
  code: OUT_OF_SCOPE
  message: Target is outside authorized filesystem scope.

⸻

16. REQUIRE_APPROVAL

The policy allows execution only after an additional approval step.

Example:

High-risk Action
        ↓
Authorization
        ↓
REQUIRE_APPROVAL
        ↓
Human approval
        ↓
ALLOW

⸻

17. DEFER

The system cannot safely decide yet.

Possible reasons:

missing evidence
ambiguous target
unavailable policy
uncertain identity
external state unresolved
risk assessment incomplete

DEFER is preferable to guessing.

⸻

18. Policy

A Policy is a rule governing whether an Action may occur.

Policy may consider:

actor
identity
capability
operation
target
scope
risk
time
environment
goal
context
data sensitivity
resource usage
approval
trust
history

⸻

19. Policy Types

Veda SHOULD support:

Security Policy

Controls security-sensitive operations.

Privacy Policy

Controls access to private information.

Resource Policy

Controls CPU, RAM, storage, network, tokens, and cost.

Operational Policy

Controls system behavior.

Safety Policy

Prevents dangerous or unacceptable actions.

Governance Policy

Controls authority and system changes.

Data Policy

Controls data movement and retention.

User Preference Policy

Represents user-specific preferences.

⸻

20. Policy Precedence

When policies conflict, the system MUST have deterministic precedence.

Suggested order:

Constitution
    ↓
Hard Safety Policy
    ↓
Security Policy
    ↓
Human Explicit Restriction
    ↓
Governance Policy
    ↓
Privacy Policy
    ↓
Resource Policy
    ↓
Operational Policy
    ↓
User Preference
    ↓
Optimization

A lower-level preference MUST NOT override a higher-level safety restriction.

⸻

21. Hard vs Soft Policy

Policies SHOULD be classified as:

HARD
SOFT

Hard Policy

Cannot be overridden by ordinary optimization.

Example:

Never expose private credentials.

Soft Policy

May be overridden under an explicitly defined authority process.

Example:

Prefer local models when possible.

⸻

22. Policy Evaluation

The authorization engine SHOULD evaluate:

Identity
    ↓
Capability
    ↓
Action
    ↓
Target
    ↓
Scope
    ↓
Policy
    ↓
Risk
    ↓
Approval
    ↓
Authorization Decision

⸻

23. Default Deny

Sensitive capabilities SHOULD follow:

Default = DENY

An operation becomes executable only when the required authority exists.

This is safer than:

Default = ALLOW

followed by trying to guess what the AI probably meant.

⸻

24. Explicit Authorization

Explicit authorization should be required for sensitive operations.

Examples:

delete important data
send external communication
access sensitive information
modify security configuration
change system identity
transfer valuable resources
modify core Veda infrastructure

⸻

25. Authorization Context

An authorization decision MUST consider context.

Example:

filesystem.write

may be:

ALLOW:
development environment
DENY:
production environment

Same capability.

Different context.

Different decision.

⸻

26. Target Authorization

Authorization SHOULD bind to the target whenever practical.

Example:

target:
  repository: Veda

This does not automatically authorize:

all repositories

⸻

27. Operation Authorization

Authorization SHOULD specify operations.

Example:

ALLOW:
github.repository.read
DENY:
github.repository.delete

A read authorization MUST NOT imply write authorization.

⸻

28. Parameter Constraints

Authorization MAY constrain parameters.

Example:

constraints:
  max_file_size: 10MB
  allowed_extensions:
    - .md
    - .txt

The Action MUST satisfy these constraints.

⸻

29. Conditional Authorization

Authorization MAY depend on conditions.

Example:

ALLOW IF:
tests_pass == true
AND
target_branch == development

If the condition becomes false before execution:

Authorization = INVALID

⸻

30. Temporal Authorization

Authorization SHOULD support time limits.

Example:

issued_at: 10:00
expires_at: 10:30

After expiration:

Authorization = INVALID

Expired authorization MUST NOT be silently renewed.

⸻

31. One-Time Authorization

Sensitive Actions MAY use one-time authorization.

Example:

ALLOW_ONCE

After execution:

authorization.status = CONSUMED

The same authorization cannot be reused.

⸻

32. Authorization Revocation

Authorization MUST be revocable.

Revocation may occur because:

user revoked permission
risk increased
capability compromised
policy changed
context changed
lease expired
security incident

Revoked authorization MUST block future execution.

⸻

33. Authorization Revalidation

Authorization SHOULD be revalidated immediately before execution for sensitive Actions.

Why?

Because:

Authorization at T1

does not necessarily mean:

Authorization at T2

is still valid.

⸻

34. Time-of-Check vs Time-of-Use

Veda MUST account for:

TOCTOU
Time Of Check
    ↓
state changes
    ↓
Time Of Use

Example:

Target file is safe at T1.
Target file changes at T2.
Action executes at T3.

The system SHOULD revalidate important conditions immediately before execution.

⸻

35. Risk-Based Authorization

Authorization strength SHOULD scale with risk.

Example:

R0:
automatic
R1:
automatic
R2:
policy evaluation
R3:
strong policy + verification
R4:
explicit approval
R5:
multi-factor / human / governance control

Exact thresholds SHOULD be configurable by policy.

⸻

36. Risk Is Contextual

Risk MUST NOT depend solely on capability name.

Example:

filesystem.write

may be:

R1:
temporary file
R4:
security configuration

Therefore:

Capability Risk
+
Action Context
=
Effective Risk

⸻

37. Human Authority

The user may explicitly authorize actions.

However, human authorization MUST still operate within constitutional and system safety constraints.

Human instruction:

"Do anything necessary."

MUST NOT automatically become:

unlimited authority

Human authority is powerful but still needs boundaries for system integrity.

⸻

38. Human Approval Record

A human approval SHOULD contain:

approval_id
approver
action_id
decision
timestamp
scope
reason
expiration
authentication context

The approval becomes an auditable event.

⸻

39. Approval UI Requirements

A human approval interface SHOULD clearly show:

What will happen?
Why?
Target?
Parameters?
Risk?
Expected effect?
Potential side effects?
Rollback?
Expiration?

The interface MUST NOT hide significant side effects behind vague labels such as:

"Continue"

⸻

40. Agent Authorization

Agents may receive delegated authority.

Example:

Veda
  ↓
Coding Agent
  ↓
filesystem.write /veda/project

The delegated authority MUST be bounded.

Agent Authority ⊆ Veda Authority

⸻

41. Delegation

Delegation MUST define:

issuer
subject
capability
scope
operations
purpose
expiration
risk limit

Example:

delegation:
  issuer: veda
  subject: coding_agent
  capability: filesystem.write
  scope: /veda/project
  expires_at: ...

⸻

42. No Privilege Amplification

Delegation MUST NOT amplify authority.

Invalid:

Veda:
filesystem.write /veda
Agent:
filesystem.admin /

Valid:

Veda:
filesystem.write /veda
Agent:
filesystem.write /veda/project

⸻

43. Policy Evaluation Result

The Policy Engine SHOULD produce a structured result.

Example:

decision: ALLOW
risk:
  level: R1
scope:
  filesystem:
    root: /veda/docs
conditions:
  - target_exists
expires_at: ...
policy_refs:
  - policy.filesystem.project

This result becomes the basis for Authorization.

⸻

44. Policy Explanation

Authorization decisions SHOULD be explainable.

Example:

DENY
Reason:
Target /system/config is outside the authorized
filesystem scope /veda.

Explanations MUST not reveal sensitive policy information unnecessarily.

⸻

45. Policy Provenance

Every authorization decision SHOULD identify the policies that influenced it.

Example:

policy_refs:
  - constitution.data_protection
  - security.filesystem
  - user.project_scope

This supports debugging and auditing.

⸻

46. Policy Versioning

Policies MUST be versioned.

Example:

policy.filesystem.v3

Authorization records SHOULD reference the policy version used.

This allows historical reconstruction.

⸻

47. Policy Changes

Policy changes MUST be treated as governed changes.

A policy engine MUST NOT allow an ordinary Action to silently modify the rules that govern that Action.

This prevents:

Action
→ change policy
→ Action becomes allowed

⸻

48. Self-Authorization Prevention

Veda MUST NOT authorize an Action solely because:

the Action was generated by Veda

or:

the model predicted it was safe

or:

the planner marked it necessary

or:

the goal requires it

Necessity does not equal authority.

⸻

49. Policy Conflict

Policies may conflict.

Example:

Policy A:
Allow write.
Policy B:
Deny production write.

The system MUST resolve the conflict according to deterministic precedence.

For safety-sensitive policies:

DENY wins

unless a formally defined higher-authority process applies.

⸻

50. Policy Evaluation Order

Recommended evaluation:

1. Identity
2. Capability
3. Action validity
4. Target
5. Scope
6. Hard constraints
7. Safety policy
8. Security policy
9. Privacy policy
10. Governance
11. Resource constraints
12. Risk
13. Approval requirements
14. Conditions
15. Final decision

⸻

51. Policy Decision Caching

Authorization decisions MAY be cached only when safe.

Cache entries MUST include:

action characteristics
policy version
authorization version
expiration
scope
context assumptions

A cached decision MUST be invalidated when relevant policy or context changes.

⸻

52. Policy Determinism

Given the same:

policy version
action
identity
capability
world state
context

the authorization engine SHOULD produce the same decision.

Non-deterministic policy decisions MUST be explicitly documented.

⸻

53. Policy Testing

Policies SHOULD have automated tests.

Example:

Given:
filesystem.write
target=/veda/docs/test.md
Expect:
ALLOW

And:

Given:
filesystem.write
target=/system/config
Expect:
DENY

Policy tests become part of the Veda security suite.

⸻

54. Policy Simulation

Policy changes SHOULD be testable in simulation before deployment.

Example:

Current policy:
DENY
Proposed policy:
ALLOW
Simulate:
10,000 historical Actions
Result:
unexpected permission increase

This allows policy regression detection.

⸻

55. Historical Replay

Authorization decisions SHOULD be replayable against historical Actions.

This supports:

* security audits;
* incident investigation;
* policy debugging;
* regression testing;
* governance review.

⸻

56. Authorization Events

The Authorization Engine SHOULD emit events:

AuthorizationRequested
AuthorizationEvaluated
AuthorizationAllowed
AuthorizationDenied
AuthorizationDeferred
AuthorizationApprovalRequested
AuthorizationApproved
AuthorizationRejected
AuthorizationExpired
AuthorizationRevoked
AuthorizationConsumed
AuthorizationRevalidated

These events become part of the Veda Chronicle.

⸻

57. Authorization Lifecycle

Canonical lifecycle:

REQUESTED
   ↓
EVALUATING
   ↓
┌─────────────┬──────────────┐
│             │              │
ALLOW        DENY          DEFER
│                            │
↓                            ↓
ACTIVE                  APPROVAL_PENDING
│                            │
↓                            ↓
CONSUMED / EXPIRED      APPROVED / REJECTED

⸻

58. Authorization States

REQUESTED

Authorization has been requested.

EVALUATING

Policies are being evaluated.

ALLOWED

The policy permits execution.

DENIED

Execution is prohibited.

DEFERRED

A safe decision cannot yet be made.

APPROVAL_PENDING

Human or higher authority approval is required.

ACTIVE

Authorization may be used.

CONSUMED

One-time authorization has been used.

EXPIRED

Authorization is no longer valid.

REVOKED

Authorization has been explicitly withdrawn.

⸻

59. Authorization vs Lease

Authorization:

“This Action is allowed.”

Lease:

“This capability may be used under these temporary constraints.”

They are related but not identical.

Authorization
+
Capability Lease
=
Executable Authority

Both MUST remain independently enforceable.

⸻

60. Authorization vs Trust

Trust influences policy.

Trust does not replace authorization.

Trusted Agent
≠
Authorized Agent

A highly trusted agent still requires valid authorization for sensitive operations.

⸻

61. Authorization vs Confidence

Model confidence MUST NOT be treated as authorization.

95% model confidence

does not mean:

95% permission

Confidence is epistemic.

Authorization is normative.

These are fundamentally different concepts.

⸻

62. Authorization vs Urgency

Urgency MUST NOT automatically override policy.

Example:

Critical problem
↓
urgent Action

The Action may require emergency policy.

But:

Urgent ≠ Authorized

⸻

63. Emergency Authorization

Veda MAY support predefined emergency policies.

An emergency authorization SHOULD specify:

trigger
allowed capabilities
maximum scope
maximum risk
expiration
required audit
post-event review

Emergency mode MUST NOT become a general-purpose bypass.

⸻

64. Break-Glass Access

A break-glass mechanism MAY exist for critical recovery.

It SHOULD require:

strong authentication
explicit invocation
limited duration
limited scope
full audit
post-event review

Break-glass access MUST be exceptional.

⸻

65. Constitutional Restrictions

No ordinary authorization policy may override constitutional restrictions.

Conceptually:

Constitution
    ↓
cannot be overridden by ordinary Action

Changes to constitutional rules require the governance mechanism defined by RFC-0001 and future evolution RFCs.

⸻

66. Authorization for Self-Modification

Actions that modify:

authorization engine
policy engine
capability registry
audit system
security controls
constitution

require elevated governance.

The system MUST NOT allow:

AI
→ modify authorization
→ authorize modification

This would be a circular privilege escalation.

⸻

67. Multi-Party Authorization

Critical operations MAY require multiple approvals.

Example:

Approver A
+
Approver B
→
ALLOW

This is useful for high-risk infrastructure or irreversible operations.

⸻

68. Separation of Duties

The system SHOULD support separation of duties.

For example:

Agent A:
proposes Action
Agent B:
reviews Action
Human:
authorizes Action

No single component needs unrestricted control.

⸻

69. Four-Eyes Principle

For selected risk classes:

one actor proposes
another authority approves

This reduces the risk of a single compromised component causing significant damage.

⸻

70. Authorization Boundaries Across Agents

In a multi-agent system:

Agent A authorization
≠
Agent B authorization

An agent MUST NOT use another agent’s authority unless explicitly delegated.

⸻

71. Cross-Process Authorization

Authorization MUST remain bound to the intended Process or Action context.

A Process MUST NOT automatically reuse authorization for unrelated Actions.

⸻

72. Cross-Session Authorization

Authorization SHOULD NOT automatically survive beyond its intended session unless explicitly defined.

This prevents stale permissions from becoming permanent authority.

⸻

73. Authorization Revocation Propagation

When an authority is revoked:

Authorization
 ↓
Lease
 ↓
Dependent Actions
 ↓
Delegated Agents

the revocation SHOULD propagate according to policy.

⸻

74. Policy Failure

If the Policy Engine is unavailable:

For high-risk Actions:

DENY or DEFER

For explicitly pre-authorized low-risk operations:

MAY continue

only if the policy explicitly defines fail-open behavior.

Default for sensitive operations SHOULD be fail-closed.

⸻

75. Authorization Engine Failure

If authorization cannot be evaluated:

unknown authorization

MUST NOT automatically become:

ALLOW

Safe default:

DENY / DEFER

depending on policy.

⸻

76. Audit Requirements

The system MUST preserve:

who requested authorization
what Action was requested
which capability was requested
which policies were evaluated
which decision was made
why
when
under what policy version
who approved it
when it expires

⸻

77. Security Invariants

AUTH-1

No privileged Action may execute without valid authorization.

AUTH-2

Capability possession MUST NOT imply authorization.

AUTH-3

Authorization MUST be bound to an identifiable subject.

AUTH-4

Authorization MUST have explicit scope.

AUTH-5

Authorization MUST NOT silently expand scope.

AUTH-6

Authorization MUST have a validity period or explicitly defined lifetime.

AUTH-7

Expired authorization MUST NOT be used.

AUTH-8

Revoked authorization MUST NOT be used.

AUTH-9

Lower-priority policies MUST NOT override higher-priority hard restrictions.

AUTH-10

Model confidence MUST NOT grant authorization.

AUTH-11

Goal importance MUST NOT grant authorization.

AUTH-12

Urgency MUST NOT automatically grant authorization.

AUTH-13

Delegation MUST NOT amplify authority.

AUTH-14

Authorization decisions MUST be auditable.

AUTH-15

Authorization decisions SHOULD be reproducible.

AUTH-16

Policy versions MUST be recorded.

AUTH-17

Policy changes MUST themselves be governed.

AUTH-18

The authorization engine MUST NOT authorize its own security modifications.

AUTH-19

Unknown authorization state MUST NOT default to unrestricted access.

AUTH-20

Sensitive operations SHOULD require stronger authorization.

AUTH-21

Authorization SHOULD be revalidated before sensitive execution.

AUTH-22

One-time authorization MUST NOT be reusable.

AUTH-23

Authorization MUST respect capability scope.

AUTH-24

Authorization MUST respect Action scope.

AUTH-25

Constitutional restrictions MUST NOT be overridden by ordinary policy.

AUTH-26

A child authorization MUST NOT exceed its parent authority.

AUTH-27

Authorization failure MUST be observable.

AUTH-28

Approval records MUST be attributable to a verified authority.

AUTH-29

Security-sensitive policy decisions MUST fail closed or defer when policy evaluation is unavailable.

AUTH-30

No component may derive unlimited authority from the mere possession of intelligence.

⸻

78. Reference Authorization Architecture

                    ┌───────────────────────┐
                    │      Action           │
                    └───────────┬───────────┘
                                ↓
                    ┌───────────────────────┐
                    │ Identity Validation   │
                    └───────────┬───────────┘
                                ↓
                    ┌───────────────────────┐
                    │ Capability Validation │
                    └───────────┬───────────┘
                                ↓
                    ┌───────────────────────┐
                    │ Scope Evaluation      │
                    └───────────┬───────────┘
                                ↓
                    ┌───────────────────────┐
                    │ Policy Engine         │
                    └───────────┬───────────┘
                                ↓
                    ┌───────────────────────┐
                    │ Risk Evaluation       │
                    └───────────┬───────────┘
                                ↓
                    ┌───────────────────────┐
                    │ Approval Requirement  │
                    └───────────┬───────────┘
                                ↓
                    ┌───────────────────────┐
                    │ Authorization         │
                    └───────────┬───────────┘
                                ↓
                    ┌───────────────────────┐
                    │ Capability Executor   │
                    └───────────────────────┘

⸻

79. Complete Authority Chain

The complete authority model becomes:

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
Capability
        ↓
Action
        ↓
Executor
        ↓
World

This chain intentionally separates:

What should happen?
        ↓
Goal
What can happen?
        ↓
Capability
What may happen?
        ↓
Authorization
What is happening?
        ↓
Action
What actually happened?
        ↓
Verification

⸻

80. Example: Safe File Write

Suppose Veda wants to write:

/veda/docs/test.md

The chain is:

Intent
 ↓
Goal
 ↓
Process
 ↓
Action:
filesystem.write
 ↓
Capability:
filesystem.write
 ↓
Policy:
project files allowed
 ↓
Authorization:
ALLOW
scope=/veda/docs
 ↓
Execution
 ↓
Verification
 ↓
Commit

If the Action instead targets:

/system/config

then:

Policy
 ↓
DENY

Execution never begins.

⸻

81. Example: High-Risk Action

For a high-risk operation:

Action
 ↓
Capability
 ↓
Policy
 ↓
Risk = R5
 ↓
REQUIRE_APPROVAL
 ↓
Human approval
 ↓
Short-lived authorization
 ↓
Execution
 ↓
Independent verification
 ↓
Audit

The model cannot skip the human authorization layer.

⸻

82. Example: Unauthorized Model Proposal

Model produces:

{
  "operation": "filesystem.delete",
  "target": "/important/data"
}

The model may be highly confident.

It may even claim:

"This is necessary to achieve the goal."

Authorization still evaluates:

Capability:
filesystem.delete
Target:
/important/data
Policy:
protected resource
Decision:
DENY

The Action is blocked.

⸻

83. Example: Authorization Expiration

10:00
Authorization issued
10:15
Authorization expires
10:20
Action attempts execution

Result:

DENY

The Action must obtain new authorization.

⸻

84. Example: Context Change

At T1:

production = false

Authorization:

ALLOW

At T2:

production = true

Action:

modify configuration

Policy re-evaluation:

DENY

This prevents stale authorization.

⸻

85. Relationship With Other RFCs

RFC-0010 depends on:

RFC-0001 Constitution
RFC-0002 World Model
RFC-0003 Event Model
RFC-0004 State & World Transition
RFC-0008 Action Model
RFC-0009 Capability Model

RFC-0010 is consumed by:

RFC-0011 Capability Lease & Token
RFC-0026 Verification Engine
RFC-0027 Rollback & Recovery
RFC-0028 Tool & Capability Registry
RFC-0031 Event/Audit/Trace Fabric
RFC-0033 Veda Self Model
RFC-0037 Evolution Engine
RFC-0040 Agent Passport
RFC-0041 Trust Engine
RFC-0044 Multi-Agent World
RFC-0046 Federation Protocol

⸻

86. Final Authority Model

The complete conceptual separation is:

INTELLIGENCE
"What might we do?"
INTENT
"What do we want?"
GOAL
"What should become true?"
PLAN
"How might we achieve it?"
ACTION
"What exact operation will occur?"
CAPABILITY
"Can the system technically perform it?"
POLICY
"Under what rules?"
AUTHORIZATION
"Is it allowed now?"
EXECUTION
"What was actually attempted?"
VERIFICATION
"Did the expected effect occur?"
WORLD
"What is actually true now?"

These layers MUST NOT collapse into one another.

⸻

87. Final Principle

The security philosophy of Veda can be reduced to:

Capability tells Veda what it can do.
Policy tells Veda what rules apply.
Authorization tells Veda what it may do.
Action tells Veda what it is about to do.
Verification tells Veda what actually happened.

Therefore:

Intelligence ≠ Authority
Capability ≠ Permission
Permission ≠ Execution
Execution ≠ Outcome
Outcome ≠ Truth without Evidence

Veda’s intelligence may become extraordinarily capable.

Its authority must remain deliberately constrained.

⸻

88. Status

RFC-0010 Status: Draft v0.1.0

This RFC defines the conceptual and structural contract for Authorization and Policy.

Temporary authority, capability leases, tokens, expiration, and scoped delegation are formalized in:

RFC-0011 — Capability Lease & Token.