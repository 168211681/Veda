RFC-0029 — External World Interface

Status: Draft
Layer: 11 — Action Fabric
Depends On: RFC-0001, RFC-0002, RFC-0003, RFC-0004, RFC-0008, RFC-0009, RFC-0010, RFC-0011, RFC-0021, RFC-0026, RFC-0027, RFC-0028
Next: RFC-0030 — MCP Integration

⸻

1. Abstract

RFC-0029 defines the External World Interface of Veda.

Its purpose is to establish the formal boundary through which Veda:

* observes the external world
* sends actions to the external world
* receives external results
* detects external state changes
* maps external state into the Veda World Model
* maps Veda actions into external effects
* handles external uncertainty
* verifies external outcomes

The fundamental principle is:

Veda does not directly control the World Model. Veda interacts with the external world through explicit interfaces, observes the resulting state, verifies it, and only then updates its internal representation of reality.

⸻

2. The Core Boundary

The architecture is:

Veda Internal World
        │
        ▼
External World Interface
        │
        ▼
External System

And information flows in both directions:

WORLD
  │
  │ Observation
  ▼
External World Interface
  │
  ▼
Veda World Model

and:

Veda Action
  │
  ▼
External World Interface
  │
  ▼
External System
  │
  ▼
External Effect

The interface is therefore a boundary, not merely an API wrapper.

⸻

3. Why This Layer Exists

Without a dedicated boundary, the architecture can accidentally become:

LLM
 ↓
Tool
 ↓
World

This is dangerous because the model can confuse:

intent

with:

effect

For example:

Veda:
"Delete file."
Tool:
"Command executed."
World:
File still exists.

The system must not update the World Model merely because the tool returned successfully.

Instead:

Action
 ↓
Execution
 ↓
Observation
 ↓
Evidence
 ↓
Verification
 ↓
World Update

This separation is consistent with modern agent architectures that place the runtime between model proposals and real tool execution. (Gturitto)

⸻

4. Goals

RFC-0029 provides:

1. External observation
2. External action
3. State synchronization
4. Interface normalization
5. External identity mapping
6. External event ingestion
7. Effect tracking
8. Result handling
9. External uncertainty handling
10. External failure handling
11. External verification
12. World reconciliation
13. Interface lifecycle management
14. External system health
15. Boundary security
16. External transaction tracking
17. External state versioning

⸻

5. Non-Goals

RFC-0029 does not:

* define tool discovery
* define capability authorization
* replace the Tool Registry
* replace MCP
* replace the World Model
* determine final policy
* determine whether a goal is valuable
* decide which action should be selected
* declare an external result true without evidence

Those responsibilities belong elsewhere.

⸻

6. Fundamental Distinctions

Veda MUST distinguish:

External System
External Interface
External Observation
External Action
External Execution
External Effect
External State
External Evidence
External Verification

These are separate concepts.

⸻

7. External System

An External System is any system outside the authoritative Veda runtime whose state may affect or be affected by Veda.

Examples:

Operating System
Filesystem
Terminal
Browser
Internet
GitHub
Database
Cloud Service
Email Service
Payment Provider
Smartphone
Camera
Microphone
IoT Device
Robot
Human
Physical Environment

⸻

8. External Interface

An External Interface is the controlled mechanism through which Veda interacts with an external system.

Examples:

HTTP API
CLI
Filesystem API
Browser Automation
SSH
Database Driver
MCP
OS API
Bluetooth
USB
Serial
Sensor
Camera
Microphone
Human Approval Interface

⸻

9. Interface Adapter

Each external system SHOULD have an adapter.

Veda
  │
  ▼
External Interface
  │
  ▼
Adapter
  │
  ▼
External System

The adapter translates:

Veda semantics

into:

external semantics

and vice versa.

⸻

10. Adapter Responsibility

The adapter owns:

* protocol translation
* authentication integration
* serialization
* deserialization
* external identifiers
* transport
* retries where safe
* external errors
* external state queries
* interface-specific verification

The adapter MUST NOT silently modify Veda policy.

⸻

11. External World Boundary

The boundary consists of:

┌──────────────────────────────┐
│          VEDA                │
│                              │
│ World Model                  │
│ Brain                        │
│ Planner                      │
│ Decision                     │
│ Authorization               │
└──────────────┬───────────────┘
               │
        EXTERNAL WORLD
          INTERFACE
               │
┌──────────────▼───────────────┐
│       EXTERNAL WORLD         │
│                              │
│ OS / API / DB / Web / Device │
│ Human / Physical Environment │
└──────────────────────────────┘

No external system is automatically considered authoritative for the entire Veda World.

⸻

12. External World as Source of Reality

For state belonging to an external system:

External System

is generally the authoritative source for its own state.

Example:

GitHub

is authoritative for:

repository state
issue state
pull request state

Veda’s World Model contains:

representation

of that state.

Therefore:

Veda World Model
≠
External Reality

It is a synchronized representation.

⸻

13. State Ownership

Every external entity SHOULD have an ownership declaration.

Example:

Entity:
github.repository.veda
Authority:
GitHub
Local representation:
Veda World Model

This prevents Veda from treating its own stale state as authoritative.

⸻

14. External Entity Mapping

Veda needs to map:

Internal Entity
        ↕
External Entity

Example:

Veda Entity:
repo_veda
External Entity:
github.com/168211681/Veda

Mapping:

EntityMapping {
    mapping_id
    internal_entity_id
    external_system_id
    external_entity_id
    identity_method
    confidence
    valid_from
    valid_until
    provenance
}

⸻

15. External Identity

External identities MUST be preserved.

Examples:

GitHub:
repository_id
Database:
primary_key
Filesystem:
absolute path + filesystem identity
Cloud:
resource ARN
Device:
serial number
Human:
approved identity reference

Veda MUST NOT assume that names are globally unique.

⸻

16. Observation

Observation is information received from the external world.

Example:

GET /repository

returns:

private = false

This becomes:

Observation

not automatically:

Truth

The observation must carry provenance and timestamp.

⸻

17. Observation Object

Observation {
    observation_id
    version
    source_system
    interface_id
    operation_id
    subject
    observed_state
    observed_at
    received_at
    source_identity
    evidence_refs[]
    confidence
    freshness
    integrity
    status
    provenance
}

⸻

18. Observation Time

Veda MUST distinguish:

event_time
observation_time
transport_time
ingestion_time
processing_time

An API response received at:

12:10

may describe a state that existed at:

12:09

or earlier.

RFC-0021 governs temporal interpretation.

⸻

19. Freshness

Every observation SHOULD have a freshness policy.

Example:

filesystem:
seconds
stock price:
milliseconds/seconds
weather:
minutes
user preference:
days/months
historical fact:
possibly years

A stale observation MUST NOT automatically be treated as current state.

⸻

20. External State

An External State represents a known state of an external entity at a defined time.

ExternalState {
    state_id
    entity_id
    state
    observed_at
    valid_at
    source
    evidence
    version
    integrity
    freshness
}

⸻

21. External Action

An External Action represents an intended change to an external system.

Example:

github.issue.close

or:

filesystem.write

The action is still internal until the external system accepts and executes it.

⸻

22. External Execution

External execution means the external interface accepted the request and attempted execution.

This is not necessarily the same as:

external effect

Example:

HTTP 200

may mean:

request accepted

without proving:

database transaction committed

⸻

23. External Effect

An External Effect is an actual change in the external world.

Examples:

file created
database row changed
GitHub issue closed
email delivered
payment settled
device moved
deployment became active

The system MUST distinguish:

request
execution
effect

⸻

24. External Effect State Machine

PROPOSED
   ↓
AUTHORIZED
   ↓
DISPATCHED
   ↓
ACCEPTED
   ↓
EXECUTING
   ↓
EFFECT_PENDING
   ↓
EFFECT_CONFIRMED

Alternative states:

REJECTED
FAILED
TIMEOUT
UNKNOWN
PARTIALLY_EFFECTED
CANCELLED
COMPENSATED

⸻

25. The Dangerous UNKNOWN State

Example:

Veda sends API request
        ↓
connection lost
        ↓
no response

Possible reality:

A. Request never arrived
B. Request arrived and failed
C. Request executed
D. Request executed partially
E. Request executed but response was lost

Veda MUST represent:

UNKNOWN

until external state is verified.

⸻

26. No Blind Retry

If external state is UNKNOWN:

DO NOT automatically retry

when duplication could cause harm.

Instead:

Query State
 ↓
Verify
 ↓
Recover

This is particularly important for:

payments
orders
messages
deletions
deployments
account changes
physical actions

⸻

27. Read Interface

Read interfaces SHOULD be:

idempotent
side-effect free
time-aware
typed
traceable

Examples:

get_file
get_repository
get_issue
get_database_row
get_device_state

⸻

28. Write Interface

Write interfaces MUST explicitly declare:

side effects
risk
scope
idempotency
reversibility
expected outcome
verification

Example:

update_issue

must specify:

target
operation
new_state
expected_state
verification

⸻

29. External Interface Contract

Every interface SHOULD expose:

identity
version
operations
schemas
authentication
authorization requirements
side effects
failure semantics
timeout semantics
idempotency
state model
verification methods
health
limits

⸻

30. Interface Object

ExternalInterface {
    interface_id
    version
    system_id
    adapter_id
    protocol
    endpoint
    operations[]
    input_schema
    output_schema
    auth_profile
    capability_refs[]
    side_effect_profile
    risk_profile
    idempotency_profile
    reversibility_profile
    state_model
    verification_methods
    timeout
    retry_policy
    rate_limits
    health
    security_profile
    provenance
}

⸻

31. Interface Health

Possible states:

HEALTHY
DEGRADED
UNAVAILABLE
UNKNOWN
MAINTENANCE
QUARANTINED

Health SHOULD include:

latency
error_rate
availability
last_success
last_failure

⸻

32. Interface Versioning

Interface versions MUST be tracked separately from tool versions.

Tool:
github.adapter
Interface:
GitHub REST API v3

Changes to an external API can invalidate tool assumptions.

⸻

33. Contract Validation

Before execution:

Veda expectation
      ↓
Interface contract
      ↓
External system contract
      ↓
Compatible?

If incompatible:

BLOCK

rather than allowing the tool to improvise.

⸻

34. Schema Translation

Adapters MAY translate:

Veda schema

to:

External schema

Example:

Veda:
issue.status = CLOSED
GitHub:
state = "closed"

The translation MUST be explicit.

⸻

35. Semantic Translation

Translation must handle semantics, not only syntax.

Example:

"delete"

could mean:

soft delete
hard delete
archive
revoke
disable

The adapter MUST identify which external operation actually occurs.

⸻

36. External Event Ingestion

External systems MAY generate events independently of Veda.

Examples:

GitHub webhook
database trigger
filesystem watcher
device sensor
email
calendar event
cloud event
human action

These events enter:

External Interface
 ↓
Event Normalization
 ↓
RFC-0003 Event Model
 ↓
World Update

subject to verification rules.

⸻

37. External Events Are Not Automatically Trusted

A webhook may be:

spoofed
duplicated
delayed
out of order
replayed
tampered

Therefore:

External Event
 ↓
Identity Verification
 ↓
Integrity Verification
 ↓
Freshness Check
 ↓
Deduplication
 ↓
World Processing

⸻

38. Event Identity

Every external event SHOULD have:

external_event_id
source
source_version
event_type
event_time
received_time
sequence
integrity

If the source lacks an ID, Veda MAY derive a content hash.

⸻

39. Deduplication

External events may be delivered more than once.

Veda MUST support:

event_id
idempotency_key
content_hash
source_sequence

to detect duplicates.

⸻

40. Ordering

External events may arrive out of order.

Example:

Event A:
file_created
Event B:
file_deleted

but B arrives first.

RFC-0029 MUST preserve event metadata and defer ordering decisions to RFC-0003/RFC-0021 where required.

⸻

41. External State Synchronization

Veda may synchronize through:

polling
webhooks
streams
subscriptions
change feeds
direct reads
periodic reconciliation

No single synchronization method is universally authoritative.

⸻

42. Polling

Polling:

GET state
 ↓
compare
 ↓
detect change

is simple but can produce:

stale windows
high cost
missed intermediate states

Veda MUST record synchronization limitations.

⸻

43. Webhooks

Webhooks provide:

event-driven updates

but may suffer from:

loss
duplication
delay
spoofing
replay

Therefore webhook data should be treated as event evidence until appropriately validated.

⸻

44. Reconciliation

When local and external state disagree:

Veda State
      ↕
External State

Veda enters:

RECONCILIATION_REQUIRED

Possible results:

LOCAL_STALE
EXTERNAL_STALE
LOCAL_CORRUPTED
EXTERNAL_CORRUPTED
CONCURRENT_CHANGE
UNKNOWN
CONFLICT

⸻

45. External Authority Rule

For externally owned state:

External Authority
        >
Local Cache

unless policy explicitly defines otherwise.

Example:

GitHub says PR merged.
Veda says PR open.

Veda should not overwrite GitHub’s state with its local belief.

Instead:

observe
verify
reconcile
update local model

⸻

46. Local Authority Rule

Veda remains authoritative for internal-only state.

Example:

Veda attention queue
Veda memory metadata
Veda internal task state
Veda policy state

External systems should not silently mutate these.

⸻

47. Shared Authority

Some states may have multiple legitimate authorities.

Example:

User preference

may be represented in:

iPhone
Veda
Cloud

Such systems require explicit conflict rules.

RFC-0014 handles contradictions and conflicts.

⸻

48. External Transaction

For transactional external systems:

ExternalTransaction {
    transaction_id
    external_system
    operation
    state
    started_at
    accepted_at
    committed_at
    idempotency_key
    external_reference
    expected_effect
    actual_effect
    verification
}

⸻

49. Transaction States

CREATED
SUBMITTED
ACCEPTED
PROCESSING
COMMITTED
FAILED
CANCELLED
REVERSED
UNKNOWN

⸻

50. External Commit Boundary

The system MUST identify where:

external effect becomes durable

This is the external commit boundary.

Example:

API request
 ↓
Validation
 ↓
Processing
 ↓
COMMIT
 ↓
Durable state

The interface SHOULD expose this distinction where possible.

⸻

51. Commit Receipt

A successful external call MAY produce a receipt.

But:

Receipt
≠
universal proof of outcome

A receipt proves what the interface reported, not necessarily the complete external-world consequence.

This distinction is important because external execution evidence and actual downstream world state can diverge. (Agoragentic)

⸻

52. External State Verification

After consequential operations:

Action
 ↓
External Execution
 ↓
Read External State
 ↓
Compare
 ↓
Verify

Example:

Create GitHub issue
 ↓
API accepted
 ↓
GET issue
 ↓
Issue exists
 ↓
Title matches
 ↓
Verified

⸻

53. Postconditions

Every consequential operation SHOULD define postconditions.

Example:

Operation:
create_file
Expected:
file.exists = true
content_hash = X
permissions = expected

The External Interface retrieves actual state.

RFC-0026 verifies it.

⸻

54. External Effect Classes

Veda SHOULD classify effects:

NO_EFFECT
LOCAL_EFFECT
REMOTE_EFFECT
PERSISTENT_EFFECT
IRREVERSIBLE_EFFECT
FINANCIAL_EFFECT
SECURITY_EFFECT
PHYSICAL_EFFECT
SOCIAL_EFFECT

Higher-risk effects require stronger controls.

⸻

55. External Action Risk

Risk is based on:

impact
probability
irreversibility
uncertainty
scope
externality

Example:

read file

versus:

delete database

should not pass through identical controls.

⸻

56. Human Interface

Humans are also part of the External World.

Examples:

User approval
Voice command
Touch input
Physical button
Phone notification
Wearable

The Human Interface MUST preserve:

identity
intent
authorization
approval
timestamp
context

⸻

57. Human Approval

A human approval is itself an external event.

Approval Request
 ↓
Human
 ↓
Approve / Reject
 ↓
Evidence
 ↓
Authorization

The system MUST verify:

who approved
what was approved
when
under which context

⸻

58. Physical World

Physical devices MUST be treated as higher-risk external systems.

Examples:

robot
car
door lock
camera
motor
power switch
IoT device
laboratory equipment

Physical actions SHOULD require:

strong identity
bounded capability
risk policy
precondition verification
execution monitoring
postcondition verification
emergency stop

⸻

59. Sensor Interfaces

Sensors produce observations.

Examples:

camera
microphone
temperature
GPS
accelerometer
light
motion

Sensor data SHOULD include:

sensor identity
calibration
timestamp
location/context
sampling rate
quality
uncertainty

⸻

60. Sensor Data Is Not Truth

A camera may observe:

object detected

This is not automatically:

object identity = X

Sensor interpretation belongs to the cognitive/evidence pipeline.

⸻

61. Environment Drift

External systems change independently.

Examples:

API changed
website changed
filesystem changed
device firmware updated
network changed
user modified data
another agent modified state

Veda MUST detect drift.

⸻

62. Interface Drift

If external behavior differs from the registered contract:

Expected:
HTTP 200 + schema A
Actual:
HTTP 200 + schema B

Veda MUST classify:

INTERFACE_DRIFT

and potentially quarantine the adapter.

⸻

63. Contract Violation

If an external system violates expected semantics:

Contract Violation

the interface MUST NOT silently normalize the discrepancy into expected behavior.

Instead:

observe
record
classify
verify
recover

⸻

64. External Security Boundary

Credentials, secrets and sensitive data MUST cross the boundary only under explicit policy.

Veda
 ↓
Data Classification
 ↓
Policy
 ↓
Interface
 ↓
External System

The Brain SHOULD NOT receive secrets merely because a tool requires them.

⸻

65. Secret Injection

Credentials SHOULD be injected at the execution boundary.

Bad:

Model context:
API_KEY=...

Preferred:

Model:
"call github.write"
Gateway:
inject credential
Tool:
execute

⸻

66. Network Boundary

Network-capable interfaces SHOULD specify:

destination
protocol
ports
DNS
TLS requirements
proxy
data classification

The system should not infer:

internet access = unlimited

⸻

67. Browser Boundary

Browser interaction should expose:

URL
origin
page identity
DOM/accessibility state
user session
cookies scope
download scope
upload scope

High-risk actions such as:

purchase
send message
change account
upload sensitive data

require explicit authorization and verification.

⸻

68. Filesystem Boundary

Filesystem interfaces SHOULD define:

root
allowed paths
read
write
delete
execute
symlink policy
device policy

Path traversal MUST be prevented.

⸻

69. Terminal Boundary

Terminal interfaces SHOULD distinguish:

read-only commands
safe commands
mutating commands
destructive commands
privileged commands

Example:

ls

is not equivalent to:

rm -rf

Both being strings typed into a shell is not a meaningful security model.

⸻

70. Database Boundary

Database interfaces SHOULD expose:

read
insert
update
delete
schema change
admin

as separate capabilities.

Production database mutation SHOULD require:

transaction
scope
verification
rollback/compensation strategy

where supported.

⸻

71. Cloud Boundary

Cloud resources SHOULD be represented by:

provider
account
region
resource_id
resource_type
permissions
state

Veda MUST distinguish:

resource requested
resource created
resource available
resource healthy

⸻

72. External World Snapshot

Before high-risk actions, Veda SHOULD capture:

World Snapshot

containing:

relevant external state
timestamps
versions
dependencies
authorization
interface version

This supports:

simulation
verification
rollback
recovery
audit

⸻

73. External State Delta

Instead of copying the entire world:

External World Delta {
    source
    entity
    previous_state
    observed_state
    changed_fields
    version
    timestamp
    evidence
}

This integrates with RFC-0043 World Delta Protocol later.

⸻

74. External State Cache

Veda MAY cache external state.

But every cached state MUST include:

observed_at
source
version
freshness
confidence

No anonymous cache entries.

⸻

75. Cache Invalidation

Cache SHOULD be invalidated when:

external event received
version changes
TTL expires
mutation occurs
conflict detected
verification fails

⸻

76. External Interface Failure

Failures include:

TIMEOUT
NETWORK_FAILURE
AUTH_FAILURE
RATE_LIMIT
SCHEMA_FAILURE
PROTOCOL_FAILURE
REMOTE_ERROR
REMOTE_UNKNOWN
PARTIAL_COMMIT
CONNECTION_LOSS

These feed RFC-0027.

⸻

77. Partial External Effect

Example:

Upload 100 files

results:

70 uploaded
30 failed

The interface MUST represent:

PARTIALLY_EFFECTED

not simply:

FAILED

The recovery engine then decides:

retry 30
rollback 70
keep 70
reconcile
human review

⸻

78. External Concurrency

External state may change while Veda acts.

Example:

Veda reads:
issue = open
User closes issue
Veda attempts:
update issue

The interface SHOULD support:

version checks
ETags
optimistic concurrency
conditional writes
state revalidation

where supported.

⸻

79. Conditional Execution

Example:

Update issue
IF version == 42

If actual version:

43

the action fails safely.

This prevents stale-state writes.

⸻

80. External Locking

Where supported, interfaces MAY use:

locks
leases
transactions
compare-and-swap
optimistic concurrency

But Veda MUST understand the semantics rather than assuming all external systems provide atomicity.

⸻

81. Interface Cancellation

Every long-running operation SHOULD support:

cancel()

where the external system supports cancellation.

However:

cancel request
≠
effect reversed

After cancellation:

verify external state

is still required.

⸻

82. Timeout Semantics

Timeout does NOT automatically mean failure.

It means:

Veda stopped waiting.

The external operation may still be running.

Therefore:

TIMEOUT
→
QUERY STATE

before retrying consequential operations.

⸻

83. External Wait

Some effects are asynchronous.

Example:

deployment submitted
 ↓
processing
 ↓
health checks
 ↓
active

The interface MUST support:

WAITING

rather than forcing an immediate success/failure classification.

⸻

84. Eventual Consistency

External systems may be eventually consistent.

Example:

Write
 ↓
API returns success
 ↓
Read replica still old

Verification policy MUST account for consistency windows.

⸻

85. Verification Window

Operations MAY define:

verification_delay
verification_timeout
poll_interval
acceptable_states

Example:

deployment
wait = 5 sec
timeout = 10 min

⸻

86. External Effect Receipt

The interface SHOULD return:

EffectReceipt {
    receipt_id
    external_system
    operation
    accepted_at
    external_reference
    reported_status
    reported_effect
    evidence_refs[]
    integrity
}

The receipt enters RFC-0026 as evidence.

⸻

87. External Interface API

The interface SHOULD expose:

connect()
disconnect()
describe()
health()
observe()
observe_state()
observe_entity()
prepare()
validate()
dispatch()
execute()
cancel()
status()
wait()
subscribe()
unsubscribe()
reconcile()
verify()
get_external_identity()
map_entity()
get_transaction()
get_effect()
get_snapshot()
get_delta()

⸻

88. Boundary Pipeline

The standard pipeline is:

ACTION REQUEST
      ↓
IDENTITY
      ↓
CAPABILITY
      ↓
AUTHORIZATION
      ↓
INTERFACE VALIDATION
      ↓
PRECONDITION CHECK
      ↓
EXTERNAL DISPATCH
      ↓
EXTERNAL EXECUTION
      ↓
OBSERVATION
      ↓
EVIDENCE
      ↓
VERIFICATION
      ↓
WORLD UPDATE

⸻

89. External Read Pipeline

REQUEST
 ↓
AUTHORIZATION
 ↓
INTERFACE
 ↓
EXTERNAL READ
 ↓
RAW RESPONSE
 ↓
VALIDATION
 ↓
NORMALIZATION
 ↓
EVIDENCE
 ↓
WORLD MODEL

⸻

90. External Write Pipeline

ACTION
 ↓
SIMULATION
 ↓
AUTHORIZATION
 ↓
INTERFACE VALIDATION
 ↓
PRECONDITION
 ↓
DISPATCH
 ↓
EXTERNAL EFFECT
 ↓
OBSERVATION
 ↓
VERIFICATION
 ↓
WORLD UPDATE

⸻

91. External World Failure Pipeline

ACTION
 ↓
EXTERNAL FAILURE
 ↓
CLASSIFY
 ↓
STATE UNKNOWN?
 ├── YES → OBSERVE EXTERNAL STATE
 │            ↓
 │         VERIFY
 │
 └── NO → RECOVERY STRATEGY
             ↓
          RFC-0027

⸻

92. External Interface Security Threats

The interface MUST defend against:

EXT-SEC-01
Spoofed external system
EXT-SEC-02
Man-in-the-middle
EXT-SEC-03
Replay
EXT-SEC-04
Credential theft
EXT-SEC-05
Schema poisoning
EXT-SEC-06
Response forgery
EXT-SEC-07
Webhook forgery
EXT-SEC-08
Stale-state injection
EXT-SEC-09
Confused deputy
EXT-SEC-10
Path traversal
EXT-SEC-11
Command injection
EXT-SEC-12
Data exfiltration
EXT-SEC-13
Privilege escalation
EXT-SEC-14
Interface drift
EXT-SEC-15
Partial commit ambiguity
EXT-SEC-16
Duplicate execution
EXT-SEC-17
External system compromise
EXT-SEC-18
Event replay
EXT-SEC-19
State poisoning
EXT-SEC-20
Boundary bypass

⸻

93. Boundary Bypass

The most important security rule:

No consequential external action
may bypass the External World Interface.

Bad:

Agent
 ↓
shell
 ↓
world

Preferred:

Agent
 ↓
Action
 ↓
Authorization
 ↓
External World Interface
 ↓
Adapter
 ↓
World

If a tool has a direct path around the boundary, the architecture has a hole.

⸻

94. External Interface Observability

Every interaction SHOULD produce:

trace_id
span_id
action_id
tool_id
interface_id
external_system_id
operation_id
timestamp
result
evidence

This feeds RFC-0031 Event/Audit/Trace Fabric.

⸻

95. External Interface and Chronicle

The Chronicle will eventually preserve:

Intent
 ↓
Action
 ↓
Authorization
 ↓
External Request
 ↓
External Response
 ↓
External Observation
 ↓
Verification
 ↓
World Update

This creates a complete external-effect history.

⸻

96. External Interface and Recovery

RFC-0027 uses the interface to:

query unknown state
cancel operation
retry
compensate
reconcile
restore
verify

The interface MUST therefore expose enough information for safe recovery.

⸻

97. External Interface and Simulation

RFC-0024 may use the same interface abstraction in simulation.

Real Interface
       ↑
       │
Interface Contract
       │
       ↓
Simulation Adapter

This allows:

same action semantics
different world

without accidentally executing the action against reality.

⸻

98. Real vs Simulated World

Every interface invocation MUST identify:

world_mode

Possible values:

REAL
SIMULATION
SANDBOX
DRY_RUN
REPLAY
TEST

A REAL capability MUST NOT silently execute in SIMULATION.

A SIMULATION action MUST NOT accidentally reach REAL.

⸻

99. World Isolation

Simulation credentials, endpoints and resources SHOULD be physically separated where practical.

Do not rely solely on:

"the model knows this is a simulation."

That is not an isolation mechanism.

⸻

100. Human-World Boundary

Veda’s final boundary may be:

Veda
 ↓
Human
 ↓
Physical World

For high-risk physical or social effects:

Human approval

may become the final commit boundary.

⸻

101. External Authority Hierarchy

For each state:

Source of Authority
      ↓
External Evidence
      ↓
Veda Representation
      ↓
Veda Prediction

Higher layers MUST NOT silently override lower authoritative state.

⸻

102. External Truth Model

Veda MUST distinguish:

Observed
Reported
Accepted
Committed
Verified

Example:

API reports payment accepted

does not automatically mean:

payment settled

The system may require a separate external observation.

⸻

103. Interface Reliability

Reliability SHOULD be tracked separately for:

transport
execution
result reporting
state consistency
verification

A tool can have:

99.9% successful HTTP requests

but poor:

outcome verification

The latter matters more for autonomous operation.

⸻

104. Interface Contract Evolution

When external APIs evolve:

detect drift
 ↓
freeze affected operations
 ↓
validate new contract
 ↓
update adapter
 ↓
test
 ↓
reactivate

High-risk interfaces SHOULD fail closed during incompatible contract changes.

⸻

105. External World Interface Invariants

EXT-1

All consequential external actions MUST pass through a governed interface.

EXT-2

External execution MUST be distinguished from external effect.

EXT-3

External effect MUST be distinguished from external verification.

EXT-4

External state MUST have provenance.

EXT-5

External observations MUST have timestamps.

EXT-6

Stale observations MUST be identifiable.

EXT-7

Unknown external state MUST remain UNKNOWN until verified.

EXT-8

Timeout MUST NOT automatically imply external failure.

EXT-9

Timeout MUST NOT automatically justify retry.

EXT-10

External systems MUST have stable identities.

EXT-11

External entities MUST have explicit mappings.

EXT-12

External authority MUST be distinguishable from local representation.

EXT-13

External events MUST support deduplication.

EXT-14

External events MUST support integrity validation.

EXT-15

External event ordering MUST be explicit.

EXT-16

External state conflicts MUST be represented.

EXT-17

Adapters MUST NOT silently change Veda policy.

EXT-18

External schemas MUST be validated.

EXT-19

Interface versions MUST be tracked.

EXT-20

Interface drift MUST be detectable.

EXT-21

External writes SHOULD define postconditions.

EXT-22

Consequential effects SHOULD be independently observable.

EXT-23

External credentials MUST remain outside unnecessary cognitive context.

EXT-24

Real and simulated interfaces MUST be distinguishable.

EXT-25

Simulation MUST NOT accidentally reach the real world.

EXT-26

External cancellation MUST NOT be assumed to reverse effects.

EXT-27

Partial effects MUST be represented explicitly.

EXT-28

External concurrency MUST be accounted for.

EXT-29

Boundary bypass MUST be prohibited.

EXT-30

No external effect may be considered verified solely because Veda intended it.

⸻

106. Architecture

                         VEDA
                          │
                  ┌───────▼───────┐
                  │     BRAIN     │
                  └───────┬───────┘
                          │
                       ACTION
                          │
                  ┌───────▼───────┐
                  │ AUTHORIZATION │
                  └───────┬───────┘
                          │
                  ┌───────▼────────┐
                  │ EXTERNAL WORLD │
                  │   INTERFACE    │
                  └───────┬────────┘
                          │
                     ADAPTER
                          │
        ┌─────────────────┼─────────────────┐
        ▼                 ▼                 ▼
      OS/API            WEB/DB          DEVICES
        │                 │                 │
        └─────────────────┼─────────────────┘
                          │
                    EXTERNAL WORLD
                          │
                     OBSERVATION
                          │
                          ▼
                       EVIDENCE
                          │
                          ▼
                     VERIFICATION
                          │
                          ▼
                    WORLD MODEL

⸻

107. Final Principle

The External World Interface is the boundary where Veda stops merely reasoning about the world and starts interacting with it.

Therefore:

The model may propose. The planner may plan. The decision engine may select. Authorization may permit. The External World Interface may transmit. But only the external world can determine what actually happened.

The architecture must therefore remain:

Think
 ↓
Plan
 ↓
Decide
 ↓
Authorize
 ↓
Act
 ↓
Observe
 ↓
Verify
 ↓
Believe only what evidence supports
 ↓
Update World

The critical rule is:

Veda must never confuse a successful request with a successful change in reality.