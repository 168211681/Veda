RFC-0028 — Tool & Capability Registry

Status: Draft
Layer: 11 — Action Fabric
Depends On: RFC-0001, RFC-0008, RFC-0009, RFC-0010, RFC-0011, RFC-0015, RFC-0016, RFC-0020, RFC-0026, RFC-0027
Next: RFC-0029 — External World Interface

⸻

1. Abstract

RFC-0028 defines the Tool & Capability Registry of Veda.

The Registry provides the authoritative catalog of:

* tools
* capabilities
* interfaces
* operations
* schemas
* resources
* permissions
* risk classifications
* execution constraints
* versions
* health
* provenance
* ownership
* security status
* compatibility
* availability

The Registry answers:

“What can Veda do, through which mechanism, under what conditions, with what authority and risk?”

It does not itself grant authority.

Instead:

Registry
    ↓
describes capability
Authorization
    ↓
determines permission
Action Engine
    ↓
executes authorized operation
Verification
    ↓
determines actual result

⸻

2. Core Principle

The Registry is:

Capability Knowledge
+
Discovery
+
Metadata
+
Governance
+
Runtime Control Reference

It is NOT:

Authority

Therefore:

Tool exists
≠
Tool is usable
Tool is usable
≠
Agent is authorized
Agent is authorized
≠
Action is safe
Action is executed
≠
Action succeeded

⸻

3. Problem

Without a central registry, Veda may end up with:

Brain
 ├── random tool
 ├── MCP server
 ├── shell command
 ├── Python function
 ├── API
 ├── browser
 └── custom script

with no authoritative answer to:

* What is this tool?
* Who registered it?
* What can it access?
* What does it modify?
* What permissions does it require?
* Is it trusted?
* What version is running?
* Is it deprecated?
* Is it safe?
* Can Veda discover it?
* Can this agent use it?
* What happens if it fails?

A registry solves the inventory problem, but the registry MUST also be enforced at invocation time. A static document listing tools is not a security boundary. (AWS Documentation)

⸻

4. Goals

RFC-0028 provides:

1. Tool discovery
2. Capability discovery
3. Tool registration
4. Capability registration
5. Schema validation
6. Version management
7. Risk classification
8. Permission metadata
9. Compatibility checking
10. Health tracking
11. Provenance
12. Ownership
13. Deprecation
14. Revocation
15. Runtime lookup
16. Capability-to-tool mapping
17. Tool-to-capability mapping
18. Security assessment metadata
19. Resource requirements
20. Audit integration

⸻

5. Non-Goals

RFC-0028 does not:

* authorize an action
* execute a tool
* replace RFC-0010
* replace RFC-0011
* replace the Action Model
* determine whether a user request is legitimate
* determine whether an outcome succeeded
* replace verification
* replace MCP
* provide arbitrary code execution by itself

⸻

6. Fundamental Vocabulary

Veda MUST distinguish:

Tool
Capability
Operation
Resource
Interface
Provider
Adapter
Credential
Permission
Authorization

⸻

7. Tool

A Tool is an executable interface exposed to Veda.

Examples:

filesystem.read
filesystem.write
terminal.execute
git.commit
github.create_pull_request
browser.open
database.query
camera.capture
microphone.listen
device.bluetooth_scan

A tool defines:

Input
Operation
Output
Execution semantics
Failure semantics
Requirements

⸻

8. Capability

A Capability represents what Veda is able to accomplish through one or more tools.

Example:

Capability:
files.read

could be implemented by:

filesystem.read
cloud-storage.read
ssh.filesystem.read

Therefore:

Capability
    ↓
may have
    ↓
multiple Tools

The abstraction prevents Veda’s cognitive layer from becoming permanently coupled to a specific implementation.

⸻

9. Tool vs Capability

Example:

Tool:
python.execute

Capability:

code.execution

Another:

Tool:
github.create_issue

Capability:

issue.create

Another:

Tool:
stripe.refund

Capability:

payment.refund

Thus:

Tool = How
Capability = What

⸻

10. Operation

A Tool may expose multiple operations.

Example:

github
 ├── repository.read
 ├── issue.create
 ├── issue.update
 ├── pull_request.create
 └── pull_request.merge

Each operation MUST have its own:

* input schema
* output schema
* risk
* permissions
* side effects
* verification requirements
* resource requirements

⸻

11. Tool Object

Tool {
    tool_id
    version
    name
    description
    provider_id
    adapter_id
    source_type
    operations[]
    capabilities[]
    input_schema
    output_schema
    supported_modalities
    side_effects
    risk_class
    reversibility
    idempotency
    resource_requirements
    permission_requirements
    security_profile
    privacy_profile
    network_requirements
    filesystem_requirements
    credential_requirements
    execution_constraints
    health
    reliability
    provenance
    owner
    status
    compatibility
    created_at
    updated_at
}

⸻

12. Tool Status

Possible states:

REGISTERED
ACTIVE
DEGRADED
QUARANTINED
DISABLED
DEPRECATED
REVOKED
UNAVAILABLE
REMOVED

A tool in:

REVOKED

MUST NOT be callable even if an agent previously possessed permission.

⸻

13. Capability Object

Capability {
    capability_id
    version
    name
    description
    semantic_scope
    operations[]
    implementation_refs[]
    required_authority
    risk_class
    reversibility
    data_access
    resource_access
    network_access
    side_effects
    constraints
    verification_requirements
    approval_requirements
    compatible_agents[]
    status
    provenance
    created_at
    updated_at
}

⸻

14. Capability Scope

Capabilities MUST define scope.

Example:

files.read

is too broad.

Better:

files.read
scope:
/home/veda/projects/*

Even better:

files.read
scope:
/home/veda/projects/Veda/docs/**

The principle is:

Capability
+
Scope

not merely:

Capability

⸻

15. Least Privilege

Veda SHOULD expose only the minimum capability required for a task.

Example:

Task:

Read README.md

Required:

filesystem.read
scope=README.md

Not:

filesystem.write
terminal.execute
filesystem.delete
network.full

Least-privilege tool scoping is particularly important because agent tool selection can favor unnecessarily powerful tools, and transient failures can make that problem worse. (arXiv)

⸻

16. Capability Ceiling

Each agent MUST have a maximum capability ceiling.

Agent Ceiling
      ↓
Task Requirement
      ↓
Effective Capability

Example:

Agent ceiling:
files.read
files.write
Task:
delete file
Required:
files.delete
Result:
DENY

Even if a tool technically supports deletion, the agent cannot obtain that authority merely by requesting it.

⸻

17. Effective Capability

The effective capability set is:

Effective =
Agent Ceiling
∩
Task Scope
∩
Policy
∩
Capability Lease
∩
Resource Constraints
∩
Security State
∩
Tool Availability

If any required condition fails:

DENY

⸻

18. Capability Discovery

Veda MAY query:

discover_capabilities()

Example:

Query:
"Can I modify GitHub issues?"

Registry returns:

Capability:
issue.update
Tools:
github.issue.update
Requirements:
github.write
Risk:
MEDIUM
Verification:
remote_state
Authorization:
required

⸻

19. Discovery Must Be Filtered

The Registry MUST NOT expose every capability blindly.

Discovery MAY depend on:

Agent identity
Task
World
Policy
Security state
Data classification
Capability ceiling
Risk
Context

Therefore:

Global Registry
      ↓
Discovery Filter
      ↓
Agent-visible Registry

An agent should not necessarily even see capabilities it can never use.

⸻

20. Registry Layers

The Registry SHOULD contain:

Global Registry
        ↓
Environment Registry
        ↓
Agent Registry
        ↓
Task Registry
        ↓
Execution Registry

Example:

Global:
browser.open
Environment:
browser.open = available
Agent:
browser.open = permitted
Task:
browser.open(example.com) = permitted
Execution:
browser.open(example.com) = approved

⸻

21. Source Types

Tools MAY originate from:

NATIVE
LOCAL_PROCESS
CLI
API
MCP
OPENAPI
DATABASE
DEVICE
OS
PLUGIN
REMOTE_SERVICE
AGENT
HUMAN
CUSTOM

The Registry MUST preserve source provenance.

⸻

22. MCP Integration

MCP is treated as an integration mechanism, not as authority.

MCP Server
    ↓
Tool Discovery
    ↓
Registry
    ↓
Capability Mapping
    ↓
Authorization
    ↓
Execution

This means Veda does not blindly trust whatever an MCP server exposes.

A current security recommendation is to maintain an approved, version-controlled tool registry and block unapproved tools by default. (AWS Documentation)

⸻

23. External Tool Registration

A new external tool MUST pass:

Discovery
 ↓
Manifest Parsing
 ↓
Schema Validation
 ↓
Identity Verification
 ↓
Security Assessment
 ↓
Risk Classification
 ↓
Capability Mapping
 ↓
Policy Evaluation
 ↓
Registration

⸻

24. Tool Manifest

Every tool SHOULD provide a manifest.

ToolManifest {
    tool_id
    version
    name
    description
    provider
    operations[]
    input_schema
    output_schema
    capabilities[]
    permissions[]
    side_effects[]
    risk_class
    reversibility
    idempotency
    network_access
    filesystem_access
    data_access
    credentials
    resource_requirements
    health_endpoint
    verification_methods
    provenance
    signature
}

⸻

25. Manifest Signature

Tool manifests SHOULD support cryptographic verification.

Manifest
   ↓
Hash
   ↓
Signature
   ↓
Identity Verification

This helps prevent:

Tool metadata tampering
Capability substitution
Version spoofing
Provider impersonation

⸻

26. Tool Identity

Every tool MUST have a stable identity:

tool_id

Example:

veda.fs.read

Version:

veda.fs.read@1.2.0

Identity MUST remain stable across compatible versions.

⸻

27. Semantic Versioning

Where applicable:

MAJOR.MINOR.PATCH

Example:

1.4.2

Meaning:

MAJOR
breaking change
MINOR
compatible capability addition
PATCH
bug/security fix

Security policy MAY override semantic-version assumptions.

⸻

28. Operation Identity

Operations SHOULD have stable IDs.

tool:
github
operations:
github.repository.read
github.issue.create
github.issue.update
github.issue.delete

Permissions should bind to operations rather than broad tool names where possible.

⸻

29. Risk Classification

Operations MUST be classified.

R0 INFORMATIONAL
R1 LOW
R2 MODERATE
R3 HIGH
R4 CRITICAL
R5 EXTREME

Example:

Operation	Risk
Read text	R0
Search web	R1
Create file	R1
Modify source	R2
Deploy service	R3
Delete production data	R5
Move physical device	R4/R5

⸻

30. Side Effect Classification

Every operation SHOULD declare:

NONE
READ_ONLY
LOCAL_MUTATION
REMOTE_MUTATION
EXTERNAL_SIDE_EFFECT
FINANCIAL
PHYSICAL
IRREVERSIBLE

Example:

filesystem.read
READ_ONLY

versus:

filesystem.delete
LOCAL_MUTATION
potentially irreversible

⸻

31. Reversibility Metadata

Operations SHOULD declare:

REVERSIBLE
PARTIALLY_REVERSIBLE
COMPENSATABLE
NON_REVERSIBLE
UNKNOWN

This connects directly to RFC-0027.

⸻

32. Idempotency Metadata

Operations SHOULD declare:

IDEMPOTENT
CONDITIONALLY_IDEMPOTENT
NON_IDEMPOTENT
UNKNOWN

Example:

GET file
IDEMPOTENT
DELETE file
CONDITIONALLY_IDEMPOTENT
TRANSFER money
NON_IDEMPOTENT

⸻

33. Input Schema

Every operation MUST define an input schema where practical.

Example:

filesystem.read {
    path: string
    encoding?: string
}

Registry MUST validate:

type
required fields
allowed values
size
format
scope

before invocation.

⸻

34. Output Schema

Tools SHOULD define:

result
status
errors
metadata
evidence
timestamps

Example:

ToolResult {
    operation_id
    status
    output
    evidence_refs[]
    warnings[]
    errors[]
    execution_time
    resource_usage
    provenance
}

⸻

35. Tool Preconditions

Every operation MAY define:

preconditions[]

Example:

github.issue.update

requires:

authenticated
repository_exists
issue_exists
permission_granted

Preconditions MUST be checked before execution.

⸻

36. Tool Postconditions

Operations SHOULD define expected postconditions.

Example:

filesystem.write

Expected:

file.exists == true
file.content_hash == expected_hash

Verification uses these postconditions through RFC-0026.

⸻

37. Resource Requirements

Tools SHOULD declare:

CPU
RAM
GPU
Storage
Network
Battery
Time
Concurrency
Tokens
External quota

Example:

local.llm.inference
RAM: 8 GB
GPU: optional
CPU: 4 cores

⸻

38. Resource Limits

Each tool MAY define:

max_runtime
max_memory
max_cpu
max_concurrency
max_requests
max_cost
max_output

These become runtime constraints.

⸻

39. Credential Requirements

Tools MUST NOT expose raw secrets through normal metadata.

Registry stores:

credential_ref

not:

api_key

Example:

github.write
credential_ref = vault://github/write

Credentials remain outside the cognitive context unless explicitly required.

⸻

40. Credential Separation

Tool:

github.read

should not automatically receive:

github.admin

Different operations SHOULD use separate credentials or scoped tokens where practical.

This follows least-privilege principles for agent workload identities. (Microsoft Learn)

⸻

41. Data Classification

Tools MUST declare the data classes they can access.

PUBLIC
INTERNAL
PRIVATE
SENSITIVE
SECRET
CRITICAL

Example:

browser.open
PUBLIC

versus:

password_manager.read
SECRET

⸻

42. Privacy Profile

Registry SHOULD record:

data_processed
data_stored
data_transmitted
external_processors
retention
logging_policy

This allows Intelligence Router and Authorization to make privacy-aware decisions.

⸻

43. Network Profile

Tools SHOULD declare:

NO_NETWORK
LOCAL_NETWORK
SPECIFIC_HOSTS
GENERAL_NETWORK
INTERNET

A tool requiring:

internet

must not silently receive:

unrestricted network

⸻

44. Filesystem Profile

Filesystem access SHOULD specify:

read_paths[]
write_paths[]
delete_paths[]
execute_paths[]

Example:

read:
/home/veda/project/**
write:
/home/veda/project/src/**
delete:
none

⸻

45. Execution Sandbox

Tools with code execution SHOULD declare sandbox requirements.

Example:

python.execute

may require:

filesystem scope
network scope
CPU limit
memory limit
runtime limit
process limit

Registry records these requirements but does not replace sandbox enforcement.

⸻

46. Health Model

Each tool SHOULD expose:

HEALTHY
DEGRADED
UNAVAILABLE
UNKNOWN
QUARANTINED

Health metadata:

latency
error_rate
availability
last_success
last_failure
failure_count
version

⸻

47. Reliability

Registry MAY maintain historical reliability:

success_rate
verification_success_rate
timeout_rate
failure_rate
recovery_rate
mean_latency

These metrics can feed RFC-0016 Intelligence Router and RFC-0027 Recovery.

⸻

48. Trust

Trust is distinct from authority.

Trust
≠
Permission

A tool may be:

high trust
low authority

or:

low trust
temporarily allowed under strict sandbox

⸻

49. Tool Trust Score

A trust assessment MAY consider:

identity
provenance
publisher
security assessment
version history
behavior
verification history
incident history
dependency integrity
runtime integrity

Trust MUST NOT automatically grant permission.

⸻

50. Quarantine

A tool may be quarantined when:

security anomaly
unexpected behavior
verification failure
credential compromise
integrity mismatch
malicious output
repeated failure

Quarantine state:

TOOL_QUARANTINED

Invocation:

DENY

unless an explicit recovery procedure allows diagnostic access.

⸻

51. Tool Lifecycle

DISCOVERED
    ↓
ASSESSED
    ↓
REGISTERED
    ↓
VALIDATED
    ↓
ACTIVE
    ↓
DEGRADED
    ↓
QUARANTINED
    ↓
RECOVERED
    ↓
ACTIVE

Alternative:

DEPRECATED
    ↓
DISABLED
    ↓
REVOKED
    ↓
REMOVED

⸻

52. Registration Lifecycle

REQUEST
 ↓
IDENTITY_CHECK
 ↓
MANIFEST_VALIDATE
 ↓
SCHEMA_VALIDATE
 ↓
SECURITY_ASSESS
 ↓
CAPABILITY_MAP
 ↓
RISK_CLASSIFY
 ↓
POLICY_CHECK
 ↓
REGISTER
 ↓
HEALTH_CHECK
 ↓
ACTIVATE

⸻

53. Registration Authority

Agents MUST NOT silently register arbitrary powerful tools for themselves.

Registration requires:

authorized registrar

which may be:

* human
* system administrator
* trusted deployment system
* approved automation

depending on policy.

⸻

54. Self-Registration

Veda MAY propose:

"I need capability X.
Tool Y could provide it."

But:

Proposal
≠
Registration

The tool must pass the registration lifecycle.

⸻

55. Capability Delegation

An agent MUST NOT transfer its authority simply by passing a tool reference to another agent.

Example:

Agent A
has:
filesystem.write

Agent A delegates task to Agent B.

Agent B does NOT automatically inherit:

filesystem.write

Instead:

Agent B
    ↓
Own capability evaluation
    ↓
Own authorization

⸻

56. Capability Lease Integration

RFC-0011 Capability Lease may temporarily activate a capability:

Lease {
    capability
    scope
    holder
    issued_at
    expires_at
    operation_limit
    risk_limit
}

Registry verifies that the capability exists and is active.

Authorization determines whether the lease is valid.

⸻

57. Tool Selection

The Brain or Planner may ask:

Find capability:
"modify Veda source code"

Registry returns candidates:

filesystem.write
git.modify
terminal.execute
github.pull_request.create

Intelligence Router / Planner / Decision Engine may then choose among them.

Registry does not make the final strategic decision.

⸻

58. Capability Substitution

Multiple tools may implement the same capability.

Example:

Capability:
web.search
Implementations:
Google Search
Bing Search
Brave Search
local index

This allows:

failover
privacy routing
cost routing
latency routing
availability routing

⸻

59. Capability Compatibility

Each implementation SHOULD expose:

supported_inputs
supported_outputs
limitations
version
dependencies

Example:

web.search

Implementation A:

text → text

Implementation B:

text → structured citations

Planner may select according to requirements.

⸻

60. Capability Equivalence

Two tools MAY implement the same capability without being semantically identical.

Therefore:

same capability
≠
same behavior

Registry MUST preserve implementation differences.

⸻

61. Capability Composition

Capabilities may compose.

Example:

files.read
+
code.parse
+
code.modify
+
git.commit

creates:

software.change

But composition MUST NOT automatically increase authority.

⸻

62. Tool Dependency Graph

Tools MAY depend on other capabilities.

Example:

github.pr.create
    ↓
network
    ↓
credential
    ↓
github.write

Registry SHOULD represent these dependencies.

⸻

63. Dependency Validation

Before activation:

Tool
 ↓
Dependencies
 ↓
Available?
 ↓
Authorized?
 ↓
Compatible?

If dependency fails:

Tool = unavailable

⸻

64. Runtime Admission

Every invocation MUST pass an admission check.

Tool Call
   ↓
Registry Lookup
   ↓
Tool Active?
   ↓
Version Valid?
   ↓
Operation Exists?
   ↓
Schema Valid?
   ↓
Capability Valid?
   ↓
Security Status Valid?
   ↓
Proceed to Authorization

⸻

65. Registry Is Not Authorization

Critical distinction:

Registry:
"This tool exists."
Authorization:
"This actor may use it."
Lease:
"This actor may use it now, within this scope."
Action:
"Perform it."
Verification:
"Did it actually happen?"

This separation prevents the registry from becoming an accidental superuser.

⸻

66. Runtime Enforcement

The Registry SHOULD be enforced at runtime, not merely used for discovery.

Agent
 ↓
Action Request
 ↓
Registry
 ↓
Authorization
 ↓
Policy
 ↓
Tool Gateway
 ↓
Execution

Current agent-security guidance similarly recommends that registry approval be enforced at invocation time and that unapproved tools be blocked by default. (AWS Documentation)

⸻

67. Tool Gateway

Veda SHOULD eventually use a Tool Gateway:

              ┌───────────────┐
Agent ───────►│ Tool Gateway  │
              └───────┬───────┘
                      │
          ┌───────────┼───────────┐
          ▼           ▼           ▼
       Registry   Authorization  Audit
                      │
                      ▼
                   Tool

The gateway becomes the enforcement point.

⸻

68. Direct Tool Access

Direct agent access to powerful tools SHOULD be prohibited.

Bad:

Agent → shell

Preferred:

Agent
 ↓
Action
 ↓
Authorization
 ↓
Tool Gateway
 ↓
Sandboxed Shell

This keeps capability enforcement outside the model’s reasoning.

⸻

69. Tool Call Envelope

Every invocation SHOULD use a structured envelope:

ToolCall {
    call_id
    actor_id
    agent_id
    task_id
    goal_id
    process_id
    action_id
    capability_id
    tool_id
    tool_version
    operation_id
    lease_id
    scope
    parameters
    expected_outcome
    verification_contract
    risk
    timestamp
    idempotency_key
}

⸻

70. Tool Result Envelope

ToolResult {
    call_id
    status
    output
    evidence_refs[]
    actual_effects[]
    world_delta_ref
    errors[]
    warnings[]
    resource_usage
    execution_duration
    tool_version
    provenance
    timestamp
}

⸻

71. Error Taxonomy

Tool errors SHOULD be normalized:

INVALID_INPUT
UNAUTHORIZED
FORBIDDEN
NOT_FOUND
CONFLICT
TIMEOUT
RATE_LIMIT
UNAVAILABLE
DEPENDENCY_FAILURE
RESOURCE_EXHAUSTED
EXECUTION_FAILURE
INTEGRITY_FAILURE
SECURITY_FAILURE
UNKNOWN

This allows RFC-0027 to select appropriate recovery strategies.

⸻

72. Version Pinning

Critical operations SHOULD pin tool versions.

Example:

tool = deployment.execute
version = 3.4.1

This improves reproducibility.

For lower-risk tools:

compatible_version_range

may be allowed.

⸻

73. Breaking Changes

If a tool changes:

input schema
output semantics
side effects
permission requirements
risk classification

it MUST trigger revalidation.

A simple version number changing without reassessment is not sufficient governance.

⸻

74. Deprecation

A deprecated tool:

DEPRECATED

may remain available temporarily.

New plans SHOULD prefer replacement tools.

Existing workflows MAY continue if policy permits.

⸻

75. Revocation

Revocation is immediate authority-independent tool shutdown.

Reasons:

security incident
credential compromise
malicious behavior
integrity failure
critical bug
policy violation
provider compromise

After revocation:

Tool invocation → DENY

⸻

76. Tool Retirement

Retirement sequence:

DEPRECATE
 ↓
MIGRATE
 ↓
DISABLE
 ↓
REVOKE
 ↓
REMOVE

Historical records remain.

⸻

77. Registry Events

The Registry MUST emit events such as:

ToolDiscoveryRequested
ToolDiscovered
ToolManifestReceived
ToolIdentityVerified
ToolManifestValidated
ToolSecurityAssessmentStarted
ToolSecurityAssessmentCompleted
CapabilityMapped
RiskClassified
ToolRegistrationRequested
ToolRegistered
ToolActivated
ToolHealthChanged
ToolDegraded
ToolUnavailable
ToolQuarantined
ToolRestored
ToolDeprecated
ToolDisabled
ToolRevoked
ToolRemoved
CapabilityCreated
CapabilityUpdated
CapabilityDeprecated
CapabilityRevoked
ToolVersionRegistered
ToolVersionDeprecated
ToolVersionRevoked
ToolDependencyDiscovered
ToolDependencyFailed
ToolInvocationRequested
ToolInvocationAdmitted
ToolInvocationDenied

⸻

78. Audit Requirements

Every registry mutation MUST be auditable.

Record:

who
what
when
why
old_version
new_version
authority
approval
source
integrity

No silent capability expansion.

⸻

79. Security Threats

RFC-0028 MUST account for:

REG-SEC-01
Fake tool registration
REG-SEC-02
Manifest tampering
REG-SEC-03
Capability spoofing
REG-SEC-04
Version substitution
REG-SEC-05
Privilege escalation
REG-SEC-06
Tool discovery poisoning
REG-SEC-07
Malicious MCP server
REG-SEC-08
Credential leakage
REG-SEC-09
Tool impersonation
REG-SEC-10
Registry compromise
REG-SEC-11
Unauthorized registration
REG-SEC-12
Unauthorized capability expansion
REG-SEC-13
Stale permission
REG-SEC-14
Quarantine bypass
REG-SEC-15
Runtime registry bypass
REG-SEC-16
Schema poisoning
REG-SEC-17
Dependency substitution
REG-SEC-18
Confused deputy
REG-SEC-19
Tool chaining privilege escalation
REG-SEC-20
Audit suppression

⸻

80. Tool Chaining Security

This is particularly important.

Suppose:

Tool A
read_private_data
Tool B
send_to_internet

Neither tool individually appears catastrophic.

Together:

Private Data
 ↓
Tool A
 ↓
Tool B
 ↓
External Internet

could create data exfiltration.

Therefore Veda MUST evaluate capability composition, not only individual tool permissions.

⸻

81. Data Flow Constraints

Capabilities SHOULD define:

allowed_inputs
allowed_outputs
allowed_destinations

Example:

SECRET
 ↓
Tool A
 ↓
Tool B

must be blocked if Tool B cannot receive SECRET data.

⸻

82. Capability Firewall

Veda SHOULD eventually implement:

Capability Firewall

which evaluates:

Actor
Capability
Target
Data
Operation
Context
Risk
Policy

before execution.

⸻

83. Registry Consistency

Registry state itself MUST be versioned.

Example:

Registry Version 100

A tool call references:

registry_version = 100

If the tool is revoked before execution:

execution-time check

must detect the change.

⸻

84. Time-of-Check / Time-of-Use

Veda MUST NOT rely only on an earlier permission check.

Example:

10:00
Tool approved
10:05
Tool revoked
10:06
Execution begins

Execution-time validation must fail.

For delayed approvals and tool execution, state and authorization should be revalidated close to execution time rather than trusting stale checks. (arXiv)

⸻

85. Capability Snapshot

Before high-risk execution:

Capability Snapshot

SHOULD contain:

tool version
capability version
policy version
authorization
lease
scope
resource limits
security status

The execution references this snapshot.

⸻

86. Deterministic Reproduction

For important actions, Veda SHOULD be able to reconstruct:

Which tool?
Which version?
Which capability?
Which policy?
Which lease?
Which parameters?
Which environment?
Which actor?
Which credentials class?

This is essential for debugging and audit.

⸻

87. Registry API

The Registry SHOULD expose:

register_tool()
register_capability()
get_tool()
get_capability()
list_tools()
list_capabilities()
discover_tools()
discover_capabilities()
validate_tool()
validate_operation()
check_compatibility()
get_health()
get_trust()
get_risk()
get_permissions()
get_requirements()
quarantine_tool()
restore_tool()
deprecate_tool()
disable_tool()
revoke_tool()
resolve_capability()
resolve_implementation()
get_versions()
get_dependencies()
create_manifest()
validate_manifest()
create_capability_snapshot()
check_runtime_admission()

⸻

88. Registry Query Example

Request:

Capability:
"edit Veda source code"

Registry response:

Capability:
code.modify
Risk:
R2
Implementations:
filesystem.write
git.modify
Requirements:
project_scope
Verification:
git.diff
tests
build
Authorization:
required
Rollback:
git.revert / workspace snapshot

The Brain now knows:

WHAT is possible
HOW it can be done
WHAT it requires
WHAT risk exists
HOW it can be verified
HOW it can be recovered

⸻

89. Registry and Planner

Planner uses Registry to determine:

Can this plan be executed?

Example:

Plan:
Modify code
Run tests
Commit
Push

Registry resolves:

code.modify
test.execute
git.commit
git.push

Planner can then construct dependencies.

⸻

90. Registry and Decision Engine

Decision Engine may compare:

Implementation A
Implementation B

based on:

risk
cost
latency
privacy
reliability
reversibility
verification

Registry supplies metadata.

Decision Engine chooses.

⸻

91. Registry and Verification

Each operation declares:

verification_requirements

Example:

git.commit

requires:

commit_exists
commit_hash_matches
working_tree_state_verified

RFC-0026 performs the actual verification.

⸻

92. Registry and Recovery

Each operation declares:

recovery_profile

Example:

deployment.execute

may specify:

rollback_supported=true
checkpoint_required=true
health_check_required=true

RFC-0027 uses this information.

⸻

93. Registry and Intelligence Router

Intelligence Router may select an intelligence provider based on tool requirements.

Example:

Task:
Analyze private source code

Registry marks:

PRIVATE_DATA

Router selects:

LOCAL_MODEL

rather than sending data to a cloud provider.

⸻

94. Registry and Brain

Brain should not memorize every raw tool.

Instead:

Brain
 ↓
Capability Query
 ↓
Registry
 ↓
Relevant Tools

This keeps cognitive context bounded.

⸻

95. Tool Discovery Is Not Tool Trust

A discovered tool may be:

UNKNOWN

Discovery only means:

"Veda knows this exists."

It does not mean:

"Veda trusts this."

⸻

96. Unknown Tool State

Unknown tools SHOULD be classified:

DISCOVERED_UNKNOWN

and blocked from privileged operations until assessed.

⸻

97. Registry as Control Plane

Architecture:

                    REGISTRY
                       │
       ┌───────────────┼────────────────┐
       ▼               ▼                ▼
   Discovery       Governance       Metadata
       │               │                │
       └───────────────┼────────────────┘
                       ▼
                 Tool Gateway
                       │
                Authorization
                       │
                    Action
                       │
                  Verification

⸻

98. Core Invariants

REG-1

Every callable tool MUST have a registry identity.

REG-2

Every capability MUST have a defined semantic scope.

REG-3

Tool existence MUST NOT imply authorization.

REG-4

Registry MUST NOT grant authority by itself.

REG-5

Powerful tools MUST be subject to runtime enforcement.

REG-6

Unregistered privileged tools MUST be blocked by default.

REG-7

Tool operations MUST have schemas where practical.

REG-8

Tool versions MUST be identifiable.

REG-9

Capability changes MUST be auditable.

REG-10

Revoked tools MUST NOT execute.

REG-11

Quarantined tools MUST be blocked except under explicit diagnostic policy.

REG-12

Capability scope MUST be enforceable.

REG-13

Tool credentials MUST NOT be exposed unnecessarily to cognitive components.

REG-14

Risk classification MUST be attached to operations.

REG-15

Side effects MUST be declared where known.

REG-16

Reversibility MUST be represented.

REG-17

Idempotency MUST be represented.

REG-18

Verification requirements SHOULD be defined.

REG-19

Recovery requirements SHOULD be defined.

REG-20

Tool dependencies MUST be represented where relevant.

REG-21

Registry state MUST be versioned.

REG-22

Execution-time permission MUST be revalidated.

REG-23

Tool discovery MUST NOT automatically imply trust.

REG-24

Tool trust MUST NOT automatically imply authority.

REG-25

Agents MUST NOT silently expand their own capability ceiling.

REG-26

Delegating a task MUST NOT automatically transfer authority.

REG-27

Capability composition MUST respect data-flow constraints.

REG-28

Registry mutations MUST be auditable.

REG-29

Tool identity MUST be bound to provenance.

REG-30

The Registry MUST NOT become a hidden superuser.

⸻

99. Architecture Summary

                    HUMAN / GOAL
                         │
                         ▼
                       BRAIN
                         │
                         ▼
                      PLANNER
                         │
                         ▼
               CAPABILITY REQUEST
                         │
                         ▼
              ┌──────────────────┐
              │ TOOL & CAPABILITY│
              │     REGISTRY     │
              └────────┬─────────┘
                       │
              Relevant tools
                       │
                       ▼
                  DECISION
                       │
                       ▼
                 AUTHORIZATION
                       │
                       ▼
                CAPABILITY LEASE
                       │
                       ▼
                  TOOL GATEWAY
                       │
                       ▼
                   EXECUTION
                       │
                       ▼
                 OBSERVATION
                       │
                       ▼
                 VERIFICATION
                       │
              ┌────────┴────────┐
              ▼                 ▼
           SUCCESS            FAILURE
              │                 │
              │                 ▼
              │              RECOVERY
              │                 │
              └────────┬────────┘
                       ▼
                    WORLD

⸻

100. Final Principle

The purpose of the Tool & Capability Registry is not to make Veda “know lots of tools.”

Its purpose is to make Veda know:

WHAT it can do
HOW it can do it
WHO provides it
WHAT it can access
WHAT authority it requires
WHAT risk it creates
WHAT constraints apply
HOW it can be verified
HOW it can be recovered
WHEN it must not be used

Therefore:

A capability is not a permission. A tool is not an authority. A registry is not an execution engine. The Registry tells Veda what exists and what is possible; Authorization decides what is permitted; the Action Fabric performs it; Verification determines what actually happened.

This separation is fundamental to Veda’s architecture.