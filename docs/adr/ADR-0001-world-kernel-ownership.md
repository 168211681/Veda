ADR-0001: World Kernel Ownership

* Status: Accepted
* Date: 2026-09-16
* Decision Type: Core Architecture
* Scope: Veda World Model, World State, Event Processing, Brain, Tools, Agents
* Supersedes: None
* Superseded by: None

⸻

1. Context

Veda มีหลาย subsystem ที่สามารถรับรู้ วิเคราะห์ คาดการณ์ หรือเสนอการเปลี่ยนแปลงต่อโลกได้ เช่น

* Brain
* Planner
* Decision Engine
* Tool System
* External World Interface
* Verification Engine
* Memory
* Knowledge
* Multi-Agent System
* Simulation Engine
* Learning/Evolution Engine

หากแต่ละ subsystem สามารถแก้ไข World State ได้โดยตรง จะเกิดปัญหา:

1. ไม่สามารถระบุได้ว่าใครเป็นผู้เปลี่ยน State
2. เกิดหลายแหล่งความจริง (Multiple Sources of Truth)
3. Agent สามารถ bypass Authorization ได้
4. Brain สามารถเปลี่ยนโลกโดยไม่ผ่าน Verification
5. Simulation อาจเขียนผลจำลองลง World จริง
6. Memory หรือ Knowledge อาจถูกตีความผิดเป็น Current World State
7. Concurrent agents อาจเขียน State ชนกัน
8. Audit trail ไม่สามารถรับประกันความสมบูรณ์
9. การ replay ประวัติไม่สามารถสร้าง World เดิมได้อย่างน่าเชื่อถือ
10. ระบบ Evolution อาจเปลี่ยน operational state โดยไม่ผ่าน governance

ดังนั้น Veda ต้องมี boundary ที่ชัดเจนระหว่าง:

ระบบที่สามารถ “คิดเกี่ยวกับโลก”

กับ

ระบบที่มีสิทธิ์ “ยืนยันว่าโลกของ Veda เปลี่ยนไปแล้ว”

⸻

2. Decision

Veda จะกำหนด World Kernel เป็นเจ้าของเพียงหนึ่งเดียวของ Authoritative Current World State

ไม่มี subsystem อื่นสามารถ mutate authoritative World State ได้โดยตรง

World Kernel เป็นผู้รับผิดชอบ:

* Entity State
* Relationship State
* World Version
* State Transition
* World Views
* Transaction Boundary
* World Delta Application
* Conflict Detection
* State Reconstruction
* Current State Projection
* Temporal State
* Provenance References
* Verification References

ทุกการเปลี่ยนแปลง authoritative World State ต้องเข้าสู่ World Kernel ผ่านเส้นทางที่กำหนดไว้

Intent
  ↓
Goal
  ↓
Plan
  ↓
Decision
  ↓
Authorization
  ↓
Capability Lease
  ↓
Capability Registry
  ↓
External World Interface
  ↓
Tool / MCP
  ↓
External System
  ↓
Observation
  ↓
Evidence
  ↓
Verification
  ↓
Event
  ↓
Chronicle
  ↓
World Kernel
  ↓
Authoritative World State

⸻

3. Core Principle

Veda กำหนดหลักการ:

Only the World Kernel may commit authoritative World State transitions.

กล่าวอีกแบบ:

Brain may propose. Tools may act. External systems may change. Observations may report. Verification may validate. But only the World Kernel may commit the modeled World State.

⸻

4. World Kernel Responsibilities

World Kernel ต้องเป็น authority สำหรับ Current World State แต่ไม่ใช่ authority สำหรับทุกเรื่องในระบบ

World Kernel เป็นเจ้าของ

World
├── Entities
├── Relationships
├── State
├── Events
├── Versions
├── Temporal State
├── Provenance References
├── World Views
└── State Transitions

World Kernel ไม่เป็นเจ้าของ

Authorization Policy
Brain Reasoning
Model Parameters
Human Authority
External System State
Raw Evidence
Agent Private Memory
Simulation State
Tool Implementation
Knowledge Corpus
Constitution

สิ่งเหล่านี้มี owner ของตัวเอง

⸻

5. World Kernel Boundary

World Kernel ต้องทำหน้าที่เป็น boundary ระหว่าง:

Cognition
    ↓
Decision
    ↓
Execution
    ↓
Observation
    ↓
Verification
    ↓
WORLD KERNEL
    ↓
Authoritative World

ห้ามมีเส้นทาง:

Brain ───────────────→ World State
Tool ────────────────→ World State
Memory ──────────────→ World State
Knowledge ───────────→ World State
Simulation ──────────→ World State
Model ───────────────→ World State
Agent ───────────────→ World State

โดยตรง

⸻

6. World State vs Reality

World Kernel ไม่ได้เป็นเจ้าของ Reality

Veda แยก:

Reality
   ↓
Observation
   ↓
Evidence
   ↓
Verification
   ↓
World State

ดังนั้น:

World ≠ Reality

World เป็นแบบจำลองที่ Veda ใช้แทนสิ่งที่เชื่อว่าเกิดขึ้นใน Reality โดยต้องมี provenance และ epistemic status กำกับ

ตัวอย่าง:

Reality:
ไฟล์บน disk ถูกลบจริง
Observation:
filesystem tool รายงานว่าไฟล์ไม่พบ
Evidence:
filesystem observation #123
Verification:
independent read check = confirmed
World:
file.status = DELETED

World Kernel จึงไม่สามารถ “สร้าง Reality” เพียงเพราะมีการเปลี่ยน World State

⸻

7. Brain Authority Boundary

Brain ไม่มีสิทธิ์ commit World State

Brain สามารถ:

* วิเคราะห์
* reason
* retrieve
* generate hypotheses
* interpret intent
* propose goals
* propose plans
* propose actions
* request simulation
* request tools
* request verification
* generate learning proposals

Brain ไม่สามารถ:

* self-authorize
* directly mutate World
* directly commit World State
* bypass Capability Registry
* bypass Authorization
* declare its own output as verified
* modify Constitution
* grant itself capabilities
* grant itself authority

ดังนั้น:

Brain
  ↓
Proposal
  ↓
Control Plane
  ↓
Authorization
  ↓
Execution
  ↓
Verification
  ↓
World Kernel

ไม่ใช่:

Brain
  ↓
World

⸻

8. External System Boundary

External systems เป็น authority ของ State ภายนอกของตัวเอง

ตัวอย่าง:

Filesystem → owns filesystem state
GitHub → owns repository state
Database → owns database state
Operating System → owns process state
Bank API → owns account state
Physical Device → owns physical device state

Veda ไม่สามารถประกาศว่า external state เปลี่ยนแล้วเพียงเพราะ Veda ส่ง command สำเร็จ

ต้องแยก:

Action
Execution Result
Observation
Verification
External State
World State

ตัวอย่าง:

DELETE file
   ↓
Tool says success
   ↓
Observation
   ↓
Filesystem verification
   ↓
Confirmed absent
   ↓
World Kernel commits:
file.status = DELETED

ดังนั้น:

Execution Success ≠ Outcome Success

⸻

9. World Transition Contract

World State ต้องเปลี่ยนผ่าน transition ที่ตรวจสอบได้

แนวคิดหลัก:

World(t)
+
Verified Event / Authorized State Transition
+
Evidence
+
Policy Constraints
↓
World(t+1)

ทุก transition ต้องมีอย่างน้อย:

transition_id
world_id
previous_version
new_version
actor_id
event_id
causation_id
authorization_ref
verification_ref
timestamp
delta
provenance

World Kernel ต้อง reject transition หาก:

* previous version ไม่ตรง
* authorization ไม่ถูกต้อง
* verification requirement ไม่ครบ
* schema ไม่ถูกต้อง
* delta ขัดกับ invariant
* event ซ้ำโดยไม่เป็น idempotent
* provenance หาย
* actor ไม่มี authority ที่เกี่ยวข้อง
* transition ทำให้ World State invalid

⸻

10. World Versioning

World ต้องเป็น versioned state

ตัวอย่าง:

World v100
   ↓
Event E101
   ↓
World v101
   ↓
Event E102
   ↓
World v102

ห้ามมี:

World
 ↓
แก้ค่าทับ
 ↓
ไม่รู้ว่าใครแก้

World Version ต้องสามารถเชื่อมกลับไปยัง:

Previous World Version
Event
Actor
Action
Evidence
Verification
Authorization

ได้

⸻

11. Read Path

Subsystem ต่าง ๆ สามารถอ่าน World ได้ผ่าน World Query / World View

Canonical read path:

World Query
   ↓
Visibility / Policy
   ↓
World View
   ↓
Context Selection
   ↓
Brain

Brain ไม่ควรอ่าน database ภายในโดยตรงเพื่อข้าม World Kernel boundary

เหตุผลคือ World Kernel ต้องควบคุม:

* visibility
* version
* temporal state
* consistency
* privacy
* provenance
* authorization
* freshness

⸻

12. Write Path

Canonical write path:

Observation
   ↓
Evidence
   ↓
Verification
   ↓
Event
   ↓
Chronicle
   ↓
World Projection
   ↓
World Kernel
   ↓
Current World

สำหรับ external action:

Intent
   ↓
Goal
   ↓
Plan
   ↓
Decision
   ↓
Authorization
   ↓
Lease
   ↓
Capability
   ↓
Tool
   ↓
External System
   ↓
Observation
   ↓
Verification
   ↓
Event
   ↓
Chronicle
   ↓
World Kernel

⸻

13. Event Sourcing Relationship

World Kernel สามารถ reconstruct World State จาก canonical event history ได้

แนวคิด:

Event History
     ↓
Replay
     ↓
World Projection
     ↓
World State

ดังนั้น Event และ World State มีความสัมพันธ์:

Event = historical fact/record
World State = current projection

ไม่ควรนำ Current World State ไปแทน Event History

และไม่ควรนำ Event Log มา query เป็น Current World ทุกกรณีโดยไม่มี projection layer

⸻

14. Simulation Isolation

Simulation ต้องไม่สามารถ mutate authoritative World

Current World
    ↓
World Snapshot
    ↓
Simulation World
    ↓
Simulated Actions
    ↓
Predicted Future

Simulation สามารถสร้าง:

SimWorld v1
SimWorld v2
Counterfactual World
Future World

แต่สิ่งเหล่านี้ไม่ใช่ authoritative World

หากต้องการนำผล simulation มาใช้ ต้องผ่าน:

Simulation Result
   ↓
Decision
   ↓
Authorization
   ↓
Real Execution
   ↓
Observation
   ↓
Verification
   ↓
World Kernel

⸻

15. Multi-Agent Isolation

ในระบบ Multi-Agent:

One Reality
One Authoritative World
Many Agents
Many Views

Agent แต่ละตัวอาจมี:

* Private Memory
* Private Working State
* Private Hypotheses
* Private Goals
* Private Plans

แต่ไม่ควรสร้าง authoritative World ของตัวเองโดยอ้างว่าเป็น Reality เดียวกัน

การเปลี่ยน Shared World ต้องผ่าน World Kernel และ authority model

⸻

16. Concurrency

World Kernel ต้องรองรับ concurrent transitions

ตัวอย่าง:

Agent A → World v100 → proposes v101
Agent B → World v100 → proposes v101

World Kernel ต้องตรวจ:

version conflict
causality
authorization
semantic conflict
resource conflict

แล้วเลือก:

ACCEPT
MERGE
REJECT
HUMAN_REVIEW

ห้าม silent overwrite

⸻

17. Failure Handling

หาก transition ไม่สามารถ commit ได้:

PROPOSED
   ↓
VALIDATING
   ↓
REJECTED

ต้องไม่เกิด partial authoritative state

หากเกิด uncertainty:

World State = UNKNOWN

แทนที่จะเดาค่า:

World State = probably_true

เมื่อหลักฐานใหม่เข้ามา:

UNKNOWN
   ↓
OBSERVED
   ↓
VERIFIED
   ↓
CURRENT STATE

⸻

18. Security Invariants

WK-001

Only World Kernel may commit authoritative World State.

WK-002

Brain cannot directly mutate World State.

WK-003

Tools cannot directly mutate World State.

WK-004

Simulation cannot mutate authoritative World.

WK-005

Memory cannot become World State merely by retrieval.

WK-006

Knowledge cannot become World State merely by inference.

WK-007

Model output cannot become World State without evidence/verification requirements being satisfied.

WK-008

External execution success cannot automatically become verified outcome.

WK-009

World Kernel cannot grant authority to itself.

WK-010

World Kernel cannot modify Constitution.

WK-011

No silent World State overwrite.

WK-012

Every authoritative transition must be traceable to an event.

WK-013

Every sensitive transition must reference authorization.

WK-014

Every transition requiring verification must reference verification evidence.

WK-015

Historical World State must remain reconstructable according to retention policy.

⸻

19. Alternatives Considered

Alternative A: Any subsystem may mutate World

Advantages

* Simple implementation
* Less infrastructure
* Faster initial development

Disadvantages

* Multiple sources of truth
* Impossible to enforce consistent authorization
* Audit becomes unreliable
* Brain can bypass control boundaries
* Concurrent mutation becomes difficult
* Security boundary collapses

Rejected.

⸻

Alternative B: Brain owns World State

Advantages

* Natural for an AI-centric architecture
* Simple mental model
* Fast interaction between cognition and state

Disadvantages

* Brain becomes authority
* Model output can become state without verification
* Prompt/model failures become state corruption
* Impossible to cleanly separate intelligence from authority
* Model replacement becomes dangerous

Rejected.

⸻

Alternative C: Database owns World State

Advantages

* Technically straightforward
* Strong transactional guarantees
* Mature database tooling

Disadvantages

A database is storage infrastructure, not necessarily semantic authority.

It does not inherently understand:

* World semantics
* provenance
* authorization
* verification
* temporal meaning
* world transitions
* agent authority
* simulation boundaries

Rejected as the architectural authority boundary.

A database may implement the World Kernel’s persistence layer.

⸻

Alternative D: World Kernel owns authoritative World State

Advantages

* Single semantic authority
* Clear mutation boundary
* Strong auditability
* Easier verification
* Easier replay
* Easier multi-agent coordination
* Easier simulation isolation
* Easier future evolution
* Clear separation between cognition and authority

Disadvantages

* More architecture
* More implementation work
* World Kernel becomes critical infrastructure
* Requires disciplined APIs
* Requires careful transaction and concurrency design

Accepted.

⸻

20. Consequences

Positive

Veda gains a single authoritative semantic boundary:

World Kernel
     ↓
Authoritative World

This makes it possible to reason about:

* who changed the world
* why it changed
* what authorization existed
* what evidence supported the change
* whether the outcome was verified
* what World version existed before
* what World version exists now
* how to reconstruct historical state

It also prevents the Brain from becoming an accidental god-king with database credentials.

Humanity has suffered enough from badly scoped permissions.

⸻

Negative

World Kernel becomes a critical subsystem.

Poor design here could affect the entire Veda architecture.

Therefore World Kernel requires:

* strong schemas
* transaction semantics
* concurrency control
* versioning
* invariant tests
* security tests
* replay tests
* recovery tests
* deterministic behavior where practical
* observability
* migration strategy

⸻

21. Dependencies

This ADR depends on:

RFC-0001 Constitution
RFC-0002 World Model
RFC-0003 Event Model
RFC-0004 State & World Transition
RFC-0008 Action Model
RFC-0010 Authorization & Policy
RFC-0012 Evidence Model
RFC-0026 Verification Engine
RFC-0027 Rollback & Recovery
RFC-0031 Event/Audit/Trace Fabric
RFC-0032 Veda Chronicle

It establishes architectural constraints for:

RFC-0018 Brain Architecture
RFC-0020 Planner
RFC-0023 Future & Scenario Engine
RFC-0024 Simulation
RFC-0025 Value & Decision
RFC-0028 Tool Registry
RFC-0029 External World Interface
RFC-0035 Experience
RFC-0036 Learning
RFC-0037 Evolution
RFC-0044 Multi-Agent World
RFC-0045 Shared/Private World
RFC-0046 Federation
RFC-0049 Intent Computing
RFC-0050 World Computing

⸻

22. Implementation Requirements

Implementation must provide a dedicated World Kernel interface.

Minimum conceptual API:

get_world()
get_world_version()
query_world()
get_entity()
get_relationship()
get_state()
get_world_view()
propose_transition()
validate_transition()
commit_transition()
create_snapshot()
restore_snapshot()
replay_events()
reconstruct_world()
detect_conflict()
resolve_conflict()

No external component should receive a generic:

set_world_state(...)

API without enforcing the complete transition contract.

⸻

23. Minimum Data Model

Conceptual World object:

World
├── world_id
├── version
├── schema_version
├── created_at
├── updated_at
├── entities
├── relationships
├── state
├── temporal_state
├── active_processes
├── active_goals
├── agents
├── resources
├── policies
├── provenance
└── integrity

Conceptual World Transition:

WorldTransition
├── transition_id
├── world_id
├── previous_version
├── resulting_version
├── actor_id
├── event_id
├── action_id
├── causation_id
├── authorization_ref
├── verification_ref
├── timestamp
├── delta
├── preconditions
├── postconditions
├── provenance
└── integrity

⸻

24. Revisit Conditions

This ADR should be reconsidered only if one of the following becomes true:

1. Veda adopts a fundamentally different World architecture.
2. Multiple authoritative Worlds must coexist within one Veda instance.
3. Distributed World consensus becomes a core requirement.
4. World State ownership must be federated across independent authorities.
5. Current World Kernel boundaries create a demonstrable scalability or correctness failure.
6. A future architecture provides stronger guarantees while preserving the same authority invariants.

If this decision changes, create a new ADR that supersedes this one.

Do not silently edit this decision.

⸻

25. Architectural Rule

The following rule is binding for Veda implementation:

┌──────────────────────────────────────────┐
│                 VEDA                     │
│                                          │
│  Brain → Thinks                          │
│  Planner → Plans                         │
│  Decision → Selects                      │
│  Authorization → Permits                 │
│  Capability → Enables                    │
│  Tool → Executes                         │
│  External System → Changes Reality       │
│  Observation → Reports                   │
│  Verification → Validates                │
│  Chronicle → Records                     │
│                                          │
│  World Kernel → COMMITS WORLD STATE      │
│                                          │
└──────────────────────────────────────────┘

Final Decision

Veda World Kernel is the sole semantic authority responsible for committing authoritative Current World State.

Everything else may observe, reason, propose, execute, verify, record, simulate, or learn, but no other subsystem may silently become the owner of the World.

Status: ACCEPTED
