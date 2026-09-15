RFC-0009: Capability Model

Status: Draft
Version: 0.1.0
Layer: Layer 4 — Authority
Module: Capability System
Path: docs/rfc/RFC-0009-capability-model.md

⸻

1. Abstract

RFC-0009 defines the Capability Model of Veda.

A Capability represents a bounded technical ability that an actor, agent, process, or system component can use to perform an operation.

The Capability Model separates:

Capability
≠
Permission
≠
Authorization
≠
Authority

A capability answers:

“What can this executor technically do?”

Authorization answers:

“Is it allowed to do it now, in this context, against this target?”

This separation is fundamental to Veda’s security architecture.

The Capability Model defines:

* capability identity;
* capability types;
* capability providers;
* capability scopes;
* capability parameters;
* capability contracts;
* capability discovery;
* capability versioning;
* capability dependencies;
* capability composition;
* capability delegation;
* capability isolation;
* capability lifecycle;
* capability revocation;
* capability risk;
* capability trust;
* capability leases;
* capability execution boundaries.

⸻

2. Motivation

Veda may eventually have access to many capabilities:

filesystem
terminal
browser
network
Git
GitHub
database
camera
microphone
calendar
email
messaging
cloud infrastructure
local models
remote models
containers
devices
sensors
financial systems

A naïve architecture might expose all of these directly to the AI.

That is unacceptable.

Instead:

Veda Intelligence
       ↓
Action
       ↓
Capability
       ↓
Authorization
       ↓
Executor
       ↓
World

The model never receives unrestricted system authority.

⸻

3. Core Principle

The most important rule is:

A capability defines technical ability, not permission.

Formally:

Capability ≠ Permission

A filesystem executor may technically support:

read
write
delete

but a particular Action may only be authorized for:

read

Therefore:

Technical Ability
        ≠
Allowed Operation

⸻

4. Capability Definition

A Capability is a named, versioned, bounded interface through which Veda can interact with a resource or system.

Formally:

Capability =
Identity
+ Provider
+ Operations
+ Scope
+ Parameters
+ Constraints
+ Risk
+ Contract
+ Lifecycle

Example:

capability_id: filesystem
version: 1.0.0
provider:
  type: local
  executor: filesystem_executor
operations:
  - read
  - write
  - create
  - delete
scope:
  type: filesystem

⸻

5. Capability vs Action

An Action describes a specific operation.

A Capability describes the mechanism that can perform it.

Example:

Capability:
filesystem.write
Action:
write /veda/docs/test.md

Therefore:

Capability = reusable ability
Action = specific use of that ability

⸻

6. Capability vs Permission

A Capability may support:

read
write
delete

A permission may grant only:

read

Example:

Capability:
filesystem
Permission:
filesystem.read:/veda/**

The Capability remains technically capable of writing.

The current authority is not.

⸻

7. Capability vs Authorization

Authorization is contextual.

Example:

Capability:
filesystem.write
Authorization A:
allowed for /veda/docs
expires 12:00
Authorization B:
denied for /system

Therefore:

Capability = ability
Authorization = contextual decision

⸻

8. Capability Object

A canonical Capability SHOULD contain:

capability_id:
version:
name:
description:
provider:
executor:
operations:
input_schema:
output_schema:
scope:
constraints:
risk:
reversibility:
dependencies:
trust_level:
isolation:
resource_requirements:
security_requirements:
supports_simulation:
supports_dry_run:
supports_rollback:
supports_idempotency:
status:
created_at:
updated_at:
expires_at:

⸻

9. Capability Identity

Every Capability MUST have a stable identifier.

Example:

filesystem
terminal
browser
github
database
model.invoke

Capability identity MUST remain stable across compatible versions.

Breaking changes require a new major version.

⸻

10. Capability Namespace

Capabilities SHOULD use hierarchical namespaces.

Example:

filesystem.read
filesystem.write
filesystem.delete
terminal.execute
browser.navigate
browser.read
browser.click
github.repository.read
github.repository.write
database.query
database.write
model.invoke

This enables precise authorization.

⸻

11. Operations

A Capability MUST explicitly declare its operations.

Example:

capability_id: filesystem
operations:
  - read
  - write
  - create
  - delete

Unknown operations MUST be rejected.

An executor MUST NOT accept arbitrary operation names simply because a model requested them.

⸻

12. Capability Contract

Each Capability MUST expose a machine-readable contract.

The contract SHOULD define:

accepted inputs
required parameters
output format
error types
side effects
resource usage
security requirements
timeout behavior
idempotency behavior
rollback behavior

This allows Veda to reason about capabilities without knowing their implementation details.

⸻

13. Input Schema

Capabilities SHOULD define structured input schemas.

Example:

operation: read
input:
  path:
    type: string
    required: true

Invalid inputs MUST be rejected before execution.

This creates an additional boundary between model output and real execution.

⸻

14. Output Schema

Capability results SHOULD use structured output.

Example:

success: true
result:
  content: ...
metadata:
  size: 1024
  modified_at: ...

Unstructured executor output SHOULD NOT be treated as trusted state without parsing and verification.

⸻

15. Capability Provider

A Provider implements a Capability.

Examples:

LocalFilesystemProvider
GitProvider
DockerProvider
BrowserProvider
PostgresProvider
GitHubProvider
ModelProvider

Multiple providers MAY implement the same Capability.

Example:

filesystem
├── local provider
├── sandbox provider
└── remote provider

⸻

16. Provider vs Capability

A Capability defines:

what can be done.

A Provider defines:

how it is done.

Example:

Capability:
model.invoke
Provider:
LocalModelProvider
Provider:
OpenAIProvider
Provider:
AnthropicProvider
Provider:
HuggingFaceProvider

The intelligence layer should therefore be able to swap providers without changing the semantic Action model.

⸻

17. Executor

An Executor performs the actual operation.

Capability
    ↓
Provider
    ↓
Executor
    ↓
External World

The Executor MUST operate within the scope and constraints assigned to it.

⸻

18. Least Privilege

Capabilities SHOULD follow the principle of least privilege.

Prefer:

filesystem.read

over:

filesystem.admin

Prefer:

github.repository.read

over:

github.full_control

Broad capabilities SHOULD be exceptional.

⸻

19. Capability Scope

A Capability MAY have a default scope.

Example:

scope:
  filesystem:
    root: /veda

This means the executor cannot access:

/system
/private
/other_user

unless explicitly granted additional scope.

⸻

20. Scope Types

Veda SHOULD support several scope dimensions.

Resource scope

/veda/project

Operation scope

read
write

Time scope

09:00–18:00

Network scope

approved domains

Data scope

public
private
sensitive

Environment scope

development
staging
production

⸻

21. Scope Intersection

Effective authority SHOULD be calculated using scope intersection.

Conceptually:

Effective Scope =
Capability Scope
∩
Authorization Scope
∩
Lease Scope
∩
Action Scope

If any component excludes the target:

Execution = DENIED

This prevents a broad capability from accidentally overriding a narrow authorization.

⸻

22. Capability Constraints

Capabilities MAY define constraints.

Examples:

maximum file size
maximum execution time
maximum network request size
maximum number of requests
maximum storage
maximum CPU
allowed destinations
allowed file extensions

Constraints MUST be enforced by the execution layer.

⸻

23. Capability Risk

Capabilities SHOULD have baseline risk.

Example:

Capability	Baseline Risk
filesystem.read	R0
filesystem.write	R1
filesystem.delete	R3
terminal.execute	R3
browser.read	R1
browser.write	R2
database.read	R1
database.write	R3
external_message.send	R3
financial.transfer	R5
system.admin	R5

Actual Action risk MAY be higher than the capability baseline.

The system MUST use the higher applicable risk.

⸻

24. Capability Reversibility

A Capability SHOULD declare whether its operations are reversible.

Example:

filesystem.create
→ reversible
filesystem.delete
→ potentially reversible
external_message.send
→ generally irreversible
financial.transfer
→ potentially compensatable

The Capability MUST NOT falsely claim reversibility.

⸻

25. Capability Dependencies

Capabilities MAY depend on other capabilities.

Example:

github.push
    ↓
network
    ↓
credentials

Dependencies MUST be explicitly declared.

A Capability MUST NOT secretly obtain additional capabilities during execution.

⸻

26. Capability Composition

Multiple capabilities MAY be composed.

Example:

browser.read
+
filesystem.write

may support:

download webpage
→ save document

Composition MUST preserve each capability’s individual authority boundary.

A composition MUST NOT create implicit privilege escalation.

⸻

27. Capability Discovery

Veda SHOULD maintain a Capability Registry.

The Registry answers:

What capabilities exist?
Who provides them?
What operations do they support?
What scopes exist?
What risk do they have?
Are they available?
Are they trusted?
Are they healthy?

This registry will be formalized further in RFC-0028.

⸻

28. Capability Availability

A registered capability is not necessarily currently available.

States SHOULD include:

REGISTERED
AVAILABLE
DEGRADED
UNAVAILABLE
DISABLED
REVOKED
EXPIRED

Example:

browser
→ REGISTERED
→ provider crashed
→ DEGRADED

Actions depending on the capability SHOULD be blocked or rerouted.

⸻

29. Capability Health

Veda SHOULD continuously monitor capability health.

Possible signals:

latency
error rate
availability
resource usage
authentication state
provider integrity
version
dependency health

Capability health MAY affect routing but MUST NOT automatically grant authority.

⸻

30. Capability Trust

Trust and authority are separate.

Trust ≠ Authority

A highly trusted capability may still be unauthorized for a particular Action.

A low-trust capability may be technically capable but prohibited from sensitive operations.

⸻

31. Capability Identity

Each Capability Provider SHOULD have an identity.

Example:

provider:
  id: provider_local_filesystem
  owner: veda

Identity enables:

* provenance;
* accountability;
* trust evaluation;
* revocation;
* auditing.

⸻

32. Capability Versioning

Capabilities SHOULD use semantic versioning.

MAJOR.MINOR.PATCH

Compatible changes MAY increment:

MINOR
PATCH

Breaking contract changes MUST increment:

MAJOR

Actions SHOULD record the capability version used.

⸻

33. Capability Compatibility

An Action created for:

filesystem.write v1

MUST NOT automatically execute through:

filesystem.write v2

if the contract is incompatible.

Compatibility MUST be explicitly established.

⸻

34. Capability Lifecycle

Canonical lifecycle:

DISCOVERED
    ↓
REGISTERED
    ↓
VALIDATED
    ↓
AVAILABLE
    ↓
DEGRADED
    ↓
DISABLED
    ↓
REVOKED

A capability MAY return from:

DEGRADED → AVAILABLE

but:

REVOKED

requires explicit re-registration.

⸻

35. Registration

A Capability SHOULD be registered before use.

Registration SHOULD contain:

identity
provider
contract
operations
scope
risk
dependencies
security requirements
version

Unknown capabilities MUST NOT be trusted merely because an executor exposes an API.

⸻

36. Validation

Before activation, Veda SHOULD validate:

schema
provider identity
operation contracts
scope enforcement
sandboxing
security controls
logging
failure handling
resource limits

A capability failing validation SHOULD remain unavailable.

⸻

37. Revocation

Capabilities MUST be revocable.

Revocation may occur because of:

security incident
provider compromise
credential compromise
software corruption
policy change
user request
resource exhaustion
unexpected behavior

Revoked capabilities MUST NOT execute new Actions.

⸻

38. Capability Disablement

Disablement differs from revocation.

DISABLED:
temporarily unavailable
REVOKED:
authority to use the capability has been withdrawn

This distinction is important for recovery and audit.

⸻

39. Capability Lease

Capabilities MAY be granted temporarily through a Capability Lease.

Example:

lease:
  capability: filesystem.write
  scope: /veda/docs
  expires_at: ...

A lease SHOULD define:

capability
scope
operations
expiration
maximum risk
usage limits
issuer
subject
purpose

RFC-0011 defines the formal Capability Lease & Token system.

⸻

40. Capability Lease Principle

A lease grants temporary bounded use.

It does NOT grant unlimited authority.

Lease
≠
Permanent Permission

⸻

41. Capability Delegation

Capabilities MAY be delegated.

Delegation MUST obey:

Child Authority ⊆ Parent Authority

A child MUST NOT receive broader capability scope than the delegator possesses.

Example:

Veda:
filesystem.write /veda
Agent:
filesystem.write /veda/docs

Valid.

But:

Veda:
filesystem.write /veda
Agent:
filesystem.admin /

Invalid.

⸻

42. Capability Escalation

A Capability MUST NOT acquire additional capabilities automatically.

Example:

browser

cannot silently obtain:

filesystem.write

because the browser happens to download files.

If multiple capabilities are required, each MUST be explicitly declared and authorized.

⸻

43. Capability Isolation

Capabilities SHOULD be isolated from each other where practical.

Example:

Browser Sandbox
      ↓
Browser Capability
Filesystem Sandbox
      ↓
Filesystem Capability

A compromise in one capability SHOULD NOT automatically compromise others.

⸻

44. Capability Sandbox

High-risk capabilities SHOULD execute inside isolated environments.

Possible isolation:

container
sandbox
VM
restricted process
OS permission boundary
network namespace

The isolation layer MUST enforce the declared scope.

⸻

45. Capability Credentials

Credentials required by a capability SHOULD be separated from model-visible context.

Example:

Model:
"Use GitHub."
Capability:
GitHub Provider
Credential:
stored securely outside model context

The model SHOULD receive:

operation result

not:

raw secret

⸻

46. Secret Handling

Capabilities MUST NOT expose secrets unnecessarily.

Examples:

API keys
passwords
private keys
session tokens
OAuth credentials
database credentials

Secrets SHOULD be:

* stored in protected storage;
* injected only at execution time;
* excluded from ordinary logs;
* rotated where possible;
* revocable.

⸻

47. Capability Parameter Filtering

Before execution, the Capability layer SHOULD validate and sanitize parameters.

Example:

Model:
filesystem.delete("/veda/../../system")

The capability layer MUST reject the request if path normalization escapes the permitted scope.

Model intent is not a security boundary.

⸻

48. Capability Output Validation

Capability results MUST be treated according to their trust level.

External data MAY be:

untrusted
partially trusted
trusted
verified

The Capability layer SHOULD attach provenance.

⸻

49. Capability and World Model

Capability execution may change the World.

Example:

Capability:
filesystem.write
Action:
create file
Event:
FileCreated
World:
new file exists

The Capability MUST report relevant effects so the World Model can be updated.

⸻

50. Capability and Event Model

Every meaningful Capability execution SHOULD produce events.

Example:

CapabilityInvoked
CapabilityExecutionStarted
CapabilityExecutionCompleted
CapabilityExecutionFailed

These events SHOULD connect to the Action and Chronicle.

⸻

51. Capability and Verification

A Capability SHOULD declare what verification mechanisms it supports.

Example:

filesystem.write
→ file hash
database.write
→ row version
github.push
→ remote commit verification

Verification remains separate from capability execution.

⸻

52. Capability Failure

Failures SHOULD be classified.

Examples:

CAPABILITY_UNAVAILABLE
INVALID_INPUT
SCOPE_VIOLATION
AUTHENTICATION_FAILURE
AUTHORIZATION_FAILURE
RESOURCE_LIMIT
DEPENDENCY_FAILURE
PROVIDER_FAILURE
TIMEOUT
UNKNOWN

Failure information MUST be structured where possible.

⸻

53. Capability Rate Limits

Capabilities MAY define rate limits.

Example:

rate_limit:
  requests: 60
  period: 1m

Rate limits protect:

* external services;
* local resources;
* budgets;
* system stability.

Rate limits MUST NOT be bypassed by creating multiple equivalent executor instances.

⸻

54. Capability Resource Limits

Capabilities MAY define:

max_cpu
max_memory
max_storage
max_network
max_execution_time
max_tokens
max_cost

The executor MUST enforce these limits where technically possible.

⸻

55. Capability Cost

Some capabilities incur cost.

Examples:

cloud model calls
API requests
storage
compute
network
external services

Capability metadata SHOULD expose expected cost.

The Planner and Value/Decision Engine may use this information.

⸻

56. Capability Privacy Class

Capabilities SHOULD declare privacy requirements.

Suggested levels:

P0 — public
P1 — internal
P2 — private
P3 — sensitive
P4 — highly sensitive

A capability requiring sensitive data SHOULD NOT automatically receive unrestricted context.

⸻

57. Capability Data Boundary

Each capability SHOULD define:

output data allowed
storage behavior
retention
external transmission

This prevents accidental data leakage.

⸻

58. Capability Context Boundary

A Capability SHOULD receive only the context required for its operation.

For example:

filesystem.write

does not need:

entire user history
all memories
private conversations
all credentials

Least-context is the cognitive equivalent of least privilege.

⸻

59. Capability Discovery by Intelligence

The intelligence layer MAY ask:

What capabilities can achieve this?

The registry may return:

filesystem.write
git.commit
github.push

However, discovery MUST NOT automatically grant access.

Discovery ≠ Authorization

⸻

60. Capability Selection

Multiple capabilities may implement the same operation.

Veda MAY select among providers based on:

availability
latency
cost
quality
privacy
trust
risk
resource usage
compatibility

This is separate from authorization.

⸻

61. Capability Fallback

If a provider fails, Veda MAY choose another provider if:

compatible
authorized
trusted
within policy

A fallback MUST NOT silently broaden authority.

⸻

62. Capability Routing

The routing process may be:

Requested Operation
        ↓
Capability Registry
        ↓
Compatible Capabilities
        ↓
Policy Filter
        ↓
Trust Filter
        ↓
Resource Filter
        ↓
Cost / Performance Ranking
        ↓
Selected Provider

⸻

63. Capability Health and Routing

An unhealthy provider SHOULD receive lower routing priority.

Example:

Provider A
latency 20ms
error 1%
Provider B
latency 500ms
error 15%

Veda may prefer A.

However:

⸻

64. Capability Provenance

Every Capability SHOULD expose provenance.

Example:

provenance:
  provider: local
  source: built_in
  version: 1.0.0
  verified_at: ...

External capabilities SHOULD identify their origin.

⸻

65. Capability Integrity

The system SHOULD detect unauthorized modification of capability implementations.

Possible mechanisms:

hash
signature
package integrity
sandbox verification
version pinning
trusted source

A modified capability SHOULD be marked:

UNTRUSTED

until revalidated.

⸻

66. Capability Self-Test

Capabilities SHOULD provide health checks where possible.

Example:

filesystem:
  permission test
  read test
  write test
  cleanup test

Self-test results MUST NOT grant authority.

⸻

67. Capability Shutdown

Veda SHOULD support controlled capability shutdown.

Example:

browser compromised
↓
disable browser capability
↓
revoke active leases
↓
block dependent Actions
↓
audit event

⸻

68. Capability Dependency Graph

Capabilities MAY form a dependency graph.

github.push
    ↓
network
    ↓
credential_provider

The system MUST detect dependency cycles where relevant.

⸻

69. Capability Availability During Execution

If a capability becomes unavailable during execution:

Action
↓
Capability failure

The Action MUST transition according to its recovery policy.

It MUST NOT automatically substitute an unrelated capability.

⸻

70. Capability Replacement

A capability provider MAY be replaced.

Example:

BrowserProvider A
        ↓
BrowserProvider B

Replacement MUST preserve:

* semantic contract;
* authorization boundaries;
* auditability;
* verification requirements.

If behavior differs materially, the capability version MUST change.

⸻

71. Capability Contract Testing

Before production use, capabilities SHOULD undergo contract tests.

Tests SHOULD verify:

input validation
scope enforcement
permission boundaries
failure handling
timeout
resource limits
logging
verification hooks
secret handling

⸻

72. Capability Certification

Veda MAY assign certification levels.

Example:

C0 — unverified
C1 — locally tested
C2 — security tested
C3 — production trusted
C4 — critical-system certified

Certification affects trust and routing.

It MUST NOT directly grant authorization.

⸻

73. Capability Risk Escalation

An Action using a low-risk capability MAY still become high-risk because of:

target
parameters
scope
context
combination
side effects

Example:

may be low risk for:

but high risk for:

Therefore risk MUST be evaluated at Action level.

⸻

74. Capability Combination Risk

Multiple individually safe capabilities may create dangerous combined authority.

Example:

+
filesystem.write
+
network

may enable large-scale data extraction.

Veda MUST evaluate capability combinations where appropriate.

⸻

75. Capability Policy Boundary

The Capability layer answers:

What is technically possible?

The Authorization layer answers:

What is allowed?

The Policy layer answers:

Under what rules is it allowed?

Therefore:

Capability
    ↓
Authorization
    ↓
Policy

These layers MUST remain separable.

⸻

76. Capability State Machine

Canonical capability state:

DISCOVERED
    ↓
REGISTERED
    ↓
VALIDATING
    ↓
AVAILABLE
    ↓
DEGRADED
    ↓
DISABLED
    ↓
REVOKED

Possible recovery:

DEGRADED → AVAILABLE
DISABLED → AVAILABLE

Revoked capabilities require explicit reactivation.

⸻

77. Security Invariants

CAP-1

Every Capability MUST have a unique identity.

CAP-2

Every Capability MUST declare supported operations.

CAP-3

Unknown operations MUST be rejected.

CAP-4

Capability existence MUST NOT imply authorization.

CAP-5

Capabilities MUST operate within explicit scope.

CAP-6

Capability scope MUST NOT expand implicitly.

CAP-7

Delegated authority MUST NOT exceed parent authority.

CAP-8

Capability providers MUST be identifiable.

CAP-9

Revoked capabilities MUST NOT execute new Actions.

CAP-10

Secrets MUST NOT be exposed to models unnecessarily.

CAP-11

Capability execution MUST respect resource limits.

CAP-12

Capability failures MUST be observable.

CAP-13

Capability execution SHOULD be auditable.

CAP-14

Capability composition MUST NOT create implicit privilege escalation.

CAP-15

Capability replacement MUST preserve authorization boundaries.

CAP-16

Capability version changes MUST be explicit.

CAP-17

Untrusted capability implementations MUST NOT be treated as trusted.

CAP-18

Capability health MUST NOT grant authority.

CAP-19

Capability discovery MUST NOT grant authorization.

CAP-20

A capability MUST NOT grant itself additional capabilities.

CAP-21

Capability credentials MUST remain outside ordinary model context.

CAP-22

Capability output MUST NOT automatically become trusted World state.

CAP-23

Capability scope MUST be enforced by the execution boundary, not merely by model instructions.

CAP-24

High-risk capabilities SHOULD have stronger isolation.

CAP-25

Capability revocation MUST invalidate dependent execution where required.

⸻

78. Reference Capability Architecture

                    ┌──────────────────────┐
                    │     Intelligence     │
                    └──────────┬───────────┘
                               ↓
                         Action Proposal
                               ↓
                    ┌──────────────────────┐
                    │ Capability Registry  │
                    └──────────┬───────────┘
                               ↓
                    Capability Resolution
                               ↓
                    ┌──────────────────────┐
                    │ Policy / Authority   │
                    └──────────┬───────────┘
                               ↓
                         Authorization
                               ↓
                    ┌──────────────────────┐
                    │ Capability Provider  │
                    └──────────┬───────────┘
                               ↓
                         Executor
                               ↓
                    ┌──────────────────────┐
                    │    External World    │
                    └──────────────────────┘

⸻

79. Example: Filesystem Capability

capability_id: filesystem
version: 1.0.0
operations:
  - read
  - create
  - write
  - delete
scope:
  root: /veda
risk:
  read: R0
  create: R1
  write: R2
  delete: R3
supports:
  dry_run: true
  rollback: partial
  idempotency: true

An Action may then request:

operation: write
target:
  path: /veda/docs/example.md

The capability does not itself authorize the write.

⸻

80. Example: Terminal Capability

capability_id: terminal.execute
version: 1.0.0
operations:
  - execute
constraints:
  timeout: 30s
  network: disabled
  filesystem_root: /veda

This is substantially safer than:

"give the AI a shell"

The latter is not an architecture. It is optimism wearing a hoodie.

⸻

81. Example: Model Capability

capability_id: model.invoke
operations:
  - generate
  - classify
  - embed
constraints:
  max_tokens: 8192
  privacy_class: P2

The model capability can be used by:

Brain
Planner
Researcher
Verifier
Coder

without granting those components unrelated system capabilities.

⸻

82. Example: Browser Capability

capability_id: browser
operations:
  - navigate
  - read
  - interact
scope:
  destinations:
    - approved_domains
constraints:
  downloads: disabled
  credentials: isolated

A browser Action requiring downloads would need an explicit capability that supports it.

⸻

83. Capability Registry Record

Example:

capability_id: filesystem
version: 1.0.0
provider:
  id: veda.filesystem.local
status: AVAILABLE
operations:
  - read
  - write
  - create
  - delete
scope:
  root: /veda
risk:
  baseline: R2
trust:
  level: high
health:
  status: healthy

⸻

84. Capability Selection Example

Suppose Veda needs to inspect a repository.

Available:

filesystem.read
git.repository.read
github.repository.read

Veda evaluates:

local state required?
network required?
freshness required?
privacy?
latency?
cost?
authorization?

It may select:

git.repository.read

rather than accessing GitHub.

Capability selection is therefore an intelligence/routing problem.

Capability authorization remains separate.

⸻

85. Capability Failure Example

Action:
github.repository.read
Provider:
GitHub API
Result:
authentication expired

Capability state:

DEGRADED

Action:

BLOCKED / FAILED

Veda may attempt another provider only if:

compatible
authorized
trusted

⸻

86. Capability Security Boundary

The final execution path MUST be:

Model
  ↓
Intent
  ↓
Goal
  ↓
Process
  ↓
Action
  ↓
Capability Resolution
  ↓
Authorization
  ↓
Scope Enforcement
  ↓
Executor
  ↓
External World

The model MUST NOT bypass this path.

⸻

87. Relationship With Future RFCs

RFC-0009 provides the foundation for:

RFC-0010 Authorization & Policy
RFC-0011 Capability Lease & Token
RFC-0015 Intelligence Provider Interface
RFC-0016 Intelligence Router
RFC-0028 Tool & Capability Registry
RFC-0029 External World Interface
RFC-0030 MCP Integration
RFC-0040 Agent Passport
RFC-0041 Trust Engine
RFC-0046 Federation Protocol

⸻

88. Final Principle

Veda must distinguish four concepts:

Can
↓
Capability
May
↓
Authorization
Should
↓
Policy / Goals / Values
Did
↓
Execution + Verification

This distinction is fundamental.

A system that knows how to perform an operation does not therefore have permission to perform it.

A system that has permission does not therefore have a reason to perform it.

A system that performed it successfully does not therefore know that the desired outcome occurred.

Therefore:

Capability
    ≠
Authorization
    ≠
Intent
    ≠
Outcome

⸻

89. Status

RFC-0009 Status: Draft v0.1.0

This RFC defines the conceptual and structural contract for Veda Capabilities.

Authorization semantics are intentionally deferred to:

RFC-0010 — Authorization & Policy.
