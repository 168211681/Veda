ADR-0003: Evidence, Knowledge, Memory, and Experience Boundary

* Status: Accepted
* Date: 2026-09-16
* Decision Type: Core Data Architecture
* Scope: Evidence, Knowledge, Memory, Experience, World State
* Supersedes: None
* Superseded by: None
* Related: ADR-0001, ADR-0002

⸻

1. Context

Veda เป็นระบบ AI ที่ต้องสามารถ:

* จำสิ่งที่ผู้ใช้เคยทำ
* เรียนรู้จากประสบการณ์
* เก็บหนังสือและเอกสาร
* สร้างความรู้
* ตรวจสอบข้อเท็จจริง
* เข้าใจสถานะปัจจุบันของโลก
* วิเคราะห์ความล้มเหลว
* นำประสบการณ์เก่ากลับมาใช้
* เปลี่ยนประสบการณ์เป็นบทเรียน
* สร้างความรู้ใหม่จากหลักฐาน

ปัญหาคือข้อมูลเหล่านี้มีลักษณะต่างกันโดยพื้นฐาน

ตัวอย่าง:

"ไฟล์ test.txt ถูกสร้างเวลา 10:00"

อาจเป็น:

* Event
* Observation
* Evidence
* World State

ขึ้นอยู่กับ context และ lifecycle

ขณะที่:

"ครั้งก่อนการ deploy แบบนี้ล้มเหลวเพราะ dependency ไม่ตรง version"

เป็น Experience

และ:

"ก่อน deploy ต้องตรวจ dependency lockfile"

เป็น Lesson / Procedural Knowledge

ส่วน:

"ผู้ใช้ชอบให้ backup ก่อนแก้ไฟล์"

เป็น Preference Memory

หากระบบใช้ storage หรือ schema เดียวกันทั้งหมด จะเกิด category error

เช่น:

Memory → treated as Truth
Knowledge → treated as Current World
Experience → treated as Fact
Embedding → treated as Source
Model Output → treated as Evidence

ดังนั้น Veda ต้องกำหนด semantic boundaries อย่างชัดเจน

⸻

2. Decision

Veda จะกำหนดให้สิ่งต่อไปนี้เป็น คนละประเภททางสถาปัตยกรรม:

Reality
   ↓
Observation
   ↓
Evidence
   ↓
Claim
   ↓
Knowledge
Event Trajectory
   ↓
Experience
   ↓
Reflection
   ↓
Lesson
   ↓
Memory / Knowledge / Skill

และแยก Current World State ออกอีกสาย:

Verified Evidence
   ↓
World Transition
   ↓
World State

สรุป:

┌──────────────────────┐
│       Reality        │
└──────────┬───────────┘
           ↓
      Observation
           ↓
        Evidence
           ↓
        Claim
           ↓
       Knowledge

และ:

Events / Actions / Outcomes
           ↓
       Experience
           ↓
       Reflection
           ↓
         Lesson
           ↓
   ┌───────┼────────┐
   ↓       ↓        ↓
 Memory  Knowledge  Skill

ขณะที่:

Evidence + Verified Transition
            ↓
        World State

⸻

3. Core Principle

Veda กำหนด:

Evidence supports claims. Knowledge represents evaluated claims. Experience represents trajectories and outcomes. Memory represents retained agent-specific information. World State represents the current modeled state of the World.

ดังนั้น:

Evidence ≠ Knowledge
Knowledge ≠ Memory
Memory ≠ Experience
Experience ≠ World State
World State ≠ Knowledge

⸻

4. Evidence

Definition

Evidence คือข้อมูลที่ใช้สนับสนุนหรือประเมิน Observation หรือ Claim

Evidence ไม่ได้แปลว่า Truth โดยอัตโนมัติ

ตัวอย่าง:

Web page
Database record
Filesystem observation
API response
Book passage
User statement
Sensor reading
Tool output
Screenshot
Log
Document

สามารถเป็น Evidence ได้

แต่ต้องมี provenance และ epistemic status

⸻

5. Evidence Object

ขั้นต่ำ:

Evidence
├── evidence_id
├── source_id
├── source_type
├── collected_at
├── observed_at
├── content
├── content_hash
├── provenance
├── scope
├── freshness
├── integrity
├── confidence
├── epistemic_status
├── extraction_method
└── validation_status

Evidence ต้องสามารถตอบได้:

มันมาจากไหน?
ได้มาเมื่อไร?
ได้มาอย่างไร?
ใคร/อะไรเป็นผู้ให้?
ถูกเปลี่ยนแปลงหรือไม่?
อยู่ใน scope ไหน?
ยังสดใหม่หรือไม่?
ผ่านการตรวจสอบระดับใด?

⸻

6. Knowledge

Knowledge คือ structured representation ของ Claim หรือความสัมพันธ์ระหว่าง Claims ที่ผ่านการประเมินโดยอิง Evidence

Conceptual chain:

Reality
   ↓
Observation
   ↓
Evidence
   ↓
Claim
   ↓
Evaluation
   ↓
Knowledge

Knowledge ต้องไม่ถูกสร้างจาก:

LLM says so

เพียงอย่างเดียว

Model output อาจเป็น:

Hypothesis
Candidate Claim
Interpretation

ก่อนจะกลายเป็น Knowledge ต้องผ่าน evidence/provenance policy ที่เหมาะสมกับระดับความเสี่ยง

⸻

7. Knowledge Object

ขั้นต่ำ:

Knowledge
├── knowledge_id
├── claim
├── claim_type
├── evidence_refs
├── provenance
├── scope
├── valid_from
├── valid_to
├── confidence
├── epistemic_status
├── verification_status
├── relationships
├── contradiction_refs
├── supersedes
└── created_at

⸻

8. Knowledge Is Not World State

ตัวอย่าง:

Knowledge:
"โดยทั่วไปไฟล์ configuration จะอยู่ใน /etc"

ไม่ได้หมายความว่า:

Current World:
"/etc/configuration exists"

Knowledge เป็น generalized claim

World State เป็น current modeled state

ดังนั้น:

Knowledge → may inform World interpretation
Knowledge ↛ automatically become World State

⸻

9. Memory

Memory คือข้อมูลที่ Agent เลือกหรือได้รับการกำหนดให้เก็บไว้เพื่อใช้ในอนาคต

Memory มีความสัมพันธ์กับ Agent

ตัวอย่าง:

ผู้ใช้ชอบภาษาไทย
ผู้ใช้ใช้ชื่อ project ว่า Veda
ครั้งก่อน task นี้ทำด้วยวิธี X
ผู้ใช้ต้องการให้ log ทุก action

Memory สามารถมาจาก:

* Experience
* Conversation
* User instruction
* Preference
* Policy
* Knowledge
* Observation

แต่การที่ข้อมูลถูกเก็บเป็น Memory ไม่ได้เพิ่มความจริงให้ข้อมูลนั้น

⸻

10. Memory Is Agent-Relative

Knowledge สามารถเป็น shared knowledge

Memory สามารถเป็น:

Agent A memory
Agent B memory
User memory
System memory
Private memory
Shared memory

ดังนั้น:

Memory
=
Retained Information
+
Owner
+
Scope
+
Retention Policy

⸻

11. Memory Types

Veda ใช้ประเภทหลัก:

Working Memory
Episodic Memory
Semantic Memory
Procedural Memory
Preference Memory
Policy Memory
Failure Memory
Prospective Memory

แต่ประเภทเหล่านี้เป็น memory semantics

ไม่ใช่แหล่ง Truth

ตัวอย่าง:

Failure Memory:
"ครั้งก่อน command นี้ล้มเหลว"

ไม่ได้หมายความว่า command นี้จะล้มเหลวทุกครั้ง

⸻

12. Experience

Experience คือ structured representation ของ trajectory ที่ Agent หรือระบบผ่านไป

Experience ต้องประกอบด้วย:

Context
→ Actions
→ Observations
→ Outcome
→ Consequences

ตัวอย่าง:

Goal:
ติดตั้ง package
Actions:
install dependency
Observation:
build failed
Outcome:
FAILED
Consequence:
application unavailable
Reflection:
version mismatch
Lesson:
ตรวจ lockfile ก่อน install

ดังนั้น:

Experience ≠ Event

Event คือ historical record

Experience คือ semantic interpretation ของ trajectory

⸻

13. Experience Object

ขั้นต่ำ:

Experience
├── experience_id
├── actor_id
├── goal_ref
├── process_ref
├── event_refs
├── context
├── actions
├── observations
├── outcome
├── consequences
├── success_status
├── failure_class
├── uncertainty
├── lessons
├── reflection_ref
└── provenance

⸻

14. Experience Is Not Truth

Experience สามารถผิดได้

ตัวอย่าง:

Agent:
"Deploy failed because server was overloaded."

นี่เป็น interpretation

ไม่ใช่ causal truth

ต้องแยก:

Observed:
CPU = 99%
Hypothesis:
CPU caused deployment failure
Verified Cause:
unknown

Experience จึงสามารถเก็บ hypothesis ได้ แต่ต้องติดป้าย epistemic status

⸻

15. Lesson

Lesson คือ generalized learning ที่สกัดจาก Experience

ตัวอย่าง:

Experience:
Deploy failed
Reflection:
Dependency mismatch
Lesson:
ตรวจ dependency lockfile ก่อน deployment

Lesson สามารถนำไปสร้าง:

* Procedural Memory
* Knowledge
* Skill
* Heuristic
* Policy proposal

แต่ไม่ควรเขียนทับ Experience เดิม

⸻

16. World State

World State เป็น representation ของสถานะปัจจุบันของ World ที่ Veda ยอมรับตาม World Kernel semantics

ตัวอย่าง:

World:
file:/test.txt
status = DELETED

ต้องมี provenance/temporal semantics ที่อธิบายว่า state นี้มาจากอะไร

World State ไม่ใช่:

* Memory
* Knowledge
* Experience
* Embedding
* LLM belief

⸻

17. The Five-Way Boundary

Veda ต้องรักษา boundary:

┌─────────────┬──────────────────────────────────┐
│ Type        │ Meaning                          │
├─────────────┼──────────────────────────────────┤
│ Evidence    │ Support for observation/claim    │
│ Knowledge   │ Evaluated structured claims      │
│ Experience  │ Trajectory + outcome + context   │
│ Memory      │ Retained agent-specific info     │
│ World State │ Current modeled world            │
└─────────────┴──────────────────────────────────┘

ไม่มีประเภทใดสามารถถูก cast เป็นอีกประเภทโดยอัตโนมัติ

⸻

18. Conversion Rules

ข้อมูลสามารถเปลี่ยน semantic class ได้ แต่ต้องมี explicit process

ตัวอย่าง:

Evidence
   ↓ evaluation
Knowledge

หรือ:

Event trajectory
   ↓ reflection
Experience

หรือ:

Experience
   ↓ learning
Lesson

หรือ:

Lesson
   ↓ retention
Procedural Memory

หรือ:

Verified Evidence
   ↓ authorized transition
World State

แต่ห้าม:

Embedding
   ↓
Truth

หรือ:

Memory
   ↓
World State

หรือ:

LLM Output
   ↓
Evidence

โดยอัตโนมัติ

⸻

19. Provenance Preservation

เมื่อข้อมูลถูกแปลง semantic class ต้องรักษา provenance

ตัวอย่าง:

Evidence E100
   ↓
Claim C100
   ↓
Knowledge K100

K100 ต้องอ้างกลับได้ว่า:

K100
 → C100
 → E100
 → Source

สำหรับ Experience:

Experience X100
 → Events
 → Actions
 → Observations
 → Evidence

สำหรับ Memory:

Memory M100
 → Source Experience
 → Source Knowledge
 → Source Event

ดังนั้นข้อมูลไม่ควรกลายเป็น orphaned semantic object

⸻

20. Contradictions

เมื่อ Knowledge ใหม่ขัดกับ Knowledge เดิม:

ห้าม:

DELETE old knowledge

โดยเงียบ ๆ

ให้สร้าง:

Knowledge K100
Knowledge K101
      ↓
Contradiction

แล้วใช้:

* evidence ranking
* temporal scope
* source quality
* verification
* confidence
* human review

เพื่อ resolve หรือ maintain uncertainty

⸻

21. Memory Contradiction

Memory ก็สามารถขัดกันได้

ตัวอย่าง:

Memory M1:
User prefers Python
Memory M2:
User now prefers Rust

ไม่ควรลบ M1 ทิ้งทันที

ควรใช้:

temporal validity
source
confidence
supersession
scope

เพื่อให้ระบบรู้ว่า:

M1 was valid historically
M2 may be current

⸻

22. Knowledge vs Memory Retrieval

Retrieval result ต้องระบุ semantic type

ตัวอย่าง:

Search Result
├── Knowledge K100
├── Memory M100
├── Experience X100
└── Evidence E100

ห้ามส่งทุกอย่างเข้า Brain ในฐานะ:

"context"

โดยไม่บอกประเภท

เพราะ Brain ต้องรู้ว่า:

นี่คือ fact?
นี่คือ memory?
นี่คือ hypothesis?
นี่คือ experience?
นี่คือ source?

⸻

23. Retrieval Does Not Change Epistemic Status

การค้นเจอข้อมูลบ่อยไม่ได้ทำให้ข้อมูลจริงขึ้น

เช่น:

Vector similarity = 0.94

ไม่ได้หมายความว่า:

Truth confidence = 94%

Embedding similarity ใช้สำหรับ retrieval

ไม่ใช่ truth score

⸻

24. Books and Documents

หนังสือและเอกสารเป็น Artifacts / Sources

ไม่ควรถูกเก็บเป็น Memory เพียงเพราะ Veda อ่านแล้ว

Canonical hierarchy:

Book
 ↓
Document
 ↓
Chapter
 ↓
Section
 ↓
Concept
 ↓
Claim
 ↓
Evidence
 ↓
Relationship

ตัวหนังสือดั้งเดิมต้องสามารถรักษา provenance กลับไปยัง artifact ได้

ตัวอย่าง:

Book:
The Art of Computer Programming
Claim:
...
Evidence:
Book → Chapter → Section → Page

ดังนั้น Knowledge สามารถอ้างหนังสือได้โดยไม่ทำลาย source artifact

⸻

25. Source vs Knowledge

Source:

"เอกสารกล่าวว่า X"

Knowledge:

"X เป็น claim ที่ Veda ประเมินจาก source ..."

สองสิ่งนี้ไม่เหมือนกัน

Source ต้องสามารถถูกตรวจกลับได้

Knowledge สามารถถูก supersede หรือ invalidate ได้

⸻

26. Model Output

Model output เป็น:

Inference Artifact

จนกว่าจะผ่านกระบวนการที่เหมาะสม

ตัวอย่าง:

LLM:
"I think dependency X caused the failure."
Status:
HYPOTHESIS

ไม่ใช่:

Verified Cause

เว้นแต่มี evidence และ verification ที่เหมาะสม

⸻

27. Unknown

Unknown เป็น valid epistemic state

Veda ต้องอนุญาต:

UNKNOWN

แทนการบังคับ:

TRUE
FALSE

ตัวอย่าง:

Cause of failure = UNKNOWN

ดีกว่า:

Cause of failure = database

เพียงเพราะ model เดาได้อย่างมั่นใจ

⸻

28. Freshness

Knowledge และ Memory ต้องมี temporal semantics

ข้อมูลบางอย่าง:

Permanent-ish

บางอย่าง:

Time-sensitive

เช่น:

Current software version
Current API behavior
Current user preference
Current server state
Current price

ต้องมี freshness/validity

Memory ไม่ได้กลายเป็นปัจจุบันเพียงเพราะมันถูกเก็บไว้นาน

⸻

29. Security

Semantic type ต้องมี access control

ตัวอย่าง:

Public Knowledge
Shared Knowledge
Private Memory
Restricted Evidence
Secret Artifact

Memory ที่ private ไม่ควรกลายเป็น Knowledge ที่ shared โดยอัตโนมัติ

Books ที่มี license restrictions ก็ต้องรักษา access/provenance constraints

⸻

30. Anti-Poisoning Boundary

Veda ต้องป้องกัน:

Malicious Source
    ↓
False Evidence
    ↓
False Knowledge
    ↓
False Memory
    ↓
Bad Decision
    ↓
Bad Action

ดังนั้น provenance ต้องถูกเก็บตลอดสาย

และการสร้าง Knowledge ที่มีผลกระทบสูงต้องสามารถ trigger:

revalidation
source review
contradiction detection
human review

ตาม risk policy

⸻

31. Storage Architecture

Logical stores:

Artifact Store
    ↓
Evidence Store
    ↓
Knowledge Store
    ↓
Memory Store
    ↓
Experience Store

แต่ physical implementation สามารถใช้ database เดียวใน MVP

ตัวอย่าง:

SQLite/PostgreSQL
├── artifacts
├── evidence
├── claims
├── knowledge
├── memories
├── experiences
└── relationships

Logical ownership ต้องไม่หายไปเพียงเพราะใช้ database เดียว

⸻

32. Vector Index

Vector database/index เป็น:

Retrieval Infrastructure

ไม่ใช่:

Truth Store

ดังนั้น:

Knowledge Store
      ↓
Embedding
      ↓
Vector Index

ไม่ใช่:

Vector Index
      ↓
Knowledge

Vector index สามารถ rebuild ได้

Source semantic objects ต้องยังอยู่

⸻

33. Lifecycle

Evidence

COLLECTED
→ VALIDATED
→ USED
→ STALE / INVALIDATED
→ RETAINED / ARCHIVED

Knowledge

PROPOSED
→ EVALUATED
→ ACCEPTED
→ ACTIVE
→ SUPERSEDED / INVALIDATED

Experience

CAPTURED
→ ANALYZED
→ REFLECTED
→ GENERALIZED
→ RETAINED
→ ARCHIVED

Memory

CREATED
→ ACTIVE
→ UPDATED / SUPERSEDED
→ DECAYED
→ ARCHIVED / FORGOTTEN

⸻

34. Forgetting

Memory สามารถถูก:

* decayed
* compressed
* summarized
* archived
* forgotten

ได้ตาม policy

แต่ forgetting Memory ไม่ควรลบ historical Evidence หรือ Chronicle โดยอัตโนมัติ

ดังนั้น:

Memory forgetting
≠
Historical deletion

⸻

35. Compression

Veda สามารถ compress Experience:

100 events
   ↓
Experience Summary

แต่ summary ต้องอ้าง provenance

ไม่ใช่แทนที่ historical source

ตัวอย่าง:

Summary S100
 → Event E1...E100

⸻

36. Consequences

Positive

Veda จะสามารถ:

* แยก fact ออกจาก memory
* แยก source ออกจาก inference
* แยก experience ออกจาก event
* ตรวจ provenance
* handle contradictions
* support knowledge updates
* support memory decay
* maintain historical integrity
* build reliable learning
* support books/library properly
* prevent retrieval from becoming truth

และทำให้ Brain สามารถ reasoning ด้วย epistemic status ที่ชัดเจน

⸻

Negative

ระบบจะมี object types มากขึ้น

ต้องดูแล:

* schema
* provenance
* conversion
* lifecycle
* access control
* contradiction
* temporal validity
* freshness
* migration

และ Brain ต้องรับ context ที่มี metadata มากขึ้น

แต่ complexity นี้เป็น necessary complexity

การรวมทุกอย่างเป็น memory ดูง่ายในวันแรก และกลายเป็นหนี้สถาปัตยกรรมในวันที่ 500

⸻

37. Alternatives Considered

Alternative A: One Unified Memory

Memory
 ├── facts
 ├── conversations
 ├── experiences
 ├── documents
 └── preferences

Rejected.

ไม่สามารถแยก persistence semantics, authority, provenance และ lifecycle ได้ชัดเจน

⸻

Alternative B: Knowledge = Memory

Rejected.

Knowledge และ Memory มี ownership และ lifecycle ต่างกัน

⸻

Alternative C: Everything = World State

Rejected.

World State ต้องเป็น current modeled state ไม่ใช่ corpus ของข้อมูลทั้งหมดที่ Veda เคยพบ

⸻

Alternative D: Everything = Vector Database

Rejected.

Vector retrieval เป็น retrieval mechanism ไม่ใช่ semantic authority

⸻

Alternative E: Explicit Semantic Separation

Evidence
Knowledge
Experience
Memory
World State

Accepted.

⸻

38. Invariants

EKME-001

Evidence is not automatically Truth.

EKME-002

Knowledge must preserve provenance.

EKME-003

Memory is not Truth.

EKME-004

Experience is not automatically Knowledge.

EKME-005

World State is not Memory.

EKME-006

Knowledge is not Current World State.

EKME-007

Embedding similarity is not truth confidence.

EKME-008

Model output is not automatically Evidence.

EKME-009

Unknown is a valid epistemic state.

EKME-010

Semantic conversion must be explicit.

EKME-011

Semantic conversion must preserve provenance.

EKME-012

Historical Evidence must not be deleted merely because Memory is forgotten.

EKME-013

Contradictory Knowledge must not be silently overwritten.

EKME-014

Memory must support temporal validity where required.

EKME-015

Books and source artifacts remain distinguishable from derived Knowledge.

EKME-016

Retrieval infrastructure is not semantic authority.

EKME-017

Private Memory cannot become Shared Knowledge without an explicit sharing policy.

⸻

39. Dependencies

Depends on:

RFC-0001 Constitution
RFC-0002 World Model
RFC-0003 Event Model
RFC-0012 Evidence Model
RFC-0013 Knowledge Model
RFC-0017 Memory Model
RFC-0035 Experience Model
ADR-0001 World Kernel Ownership
ADR-0002 Event Fabric and Chronicle Separation

Constrains:

RFC-0018 Brain Architecture
RFC-0019 Attention Engine
RFC-0020 Planner
RFC-0022 Causal Model
RFC-0023 Future & Scenario Engine
RFC-0024 Simulation
RFC-0026 Verification
RFC-0036 Reflection & Learning
RFC-0037 Evolution
RFC-0047 Neural Package Format

⸻

40. Implementation Requirements

Veda implementation ต้องมี distinct interfaces:

EvidenceStore
KnowledgeStore
ExperienceStore
MemoryStore
ArtifactStore

และ conversion services:

EvidenceEvaluator
KnowledgeBuilder
ExperienceBuilder
ReflectionEngine
LearningEngine
MemoryConsolidator

Brain ต้องได้รับ typed context:

ContextItem
├── type
├── source_ref
├── epistemic_status
├── confidence
├── temporal_scope
├── provenance
└── sensitivity

ไม่ใช่ส่ง string รวมก้อนเดียว

⸻

41. Revisit Conditions

ADR นี้ควรถูกทบทวนหาก:

1. Veda เปลี่ยน semantic model ของ cognition อย่างมีนัยสำคัญ
2. พบว่าประเภทใดสามารถรวมกันได้โดยไม่สูญเสีย authority/provenance semantics
3. Distributed knowledge architecture ต้องใช้ semantics ใหม่
4. Multi-agent knowledge federation ต้องเพิ่มประเภทข้อมูลใหม่
5. Memory architecture มี persistence semantics ที่แตกต่างจากที่กำหนด

หากเปลี่ยน decision ต้องสร้าง ADR ใหม่เพื่อ supersede ADR-0003

⸻

42. Final Decision

Veda จะไม่ใช้แนวคิด:

"ทุกอย่างคือ Memory"

แต่จะใช้:

REALITY
   ↓
OBSERVATION
   ↓
EVIDENCE
   ↓
CLAIM
   ↓
KNOWLEDGE
EVENT TRAJECTORY
   ↓
EXPERIENCE
   ↓
REFLECTION
   ↓
LESSON
   ↓
MEMORY / KNOWLEDGE / SKILL
VERIFIED EVIDENCE
   ↓
WORLD TRANSITION
   ↓
WORLD STATE

และยึดหลัก:

Memory remembers. Knowledge represents evaluated claims. Evidence supports. Experience records lived trajectories. World State represents the current modeled World.

ไม่มีสิ่งใดสามารถข้าม semantic boundary โดยอัตโนมัติ

Status: ACCEPTED
