---
id: ADR-0007
title: Storage Architecture
status: Accepted
owner: Phupha
created: 2026-09-16
updated: 2026-09-16
review_cycle: Quarterly
architecture_stage: ADR_FREEZE
---

## Decision Drivers

| Driver | Priority |
|---|---|
| Architectural consistency | P0 |
| Security boundary | P0 |
| Auditability | P1 |

## Non-Goals

This ADR does not define implementation-specific code or package layout.

## Risks

| Risk | Mitigation |
|---|---|
| Future implementation drift | SPEC documents |
| Semantic ambiguity | ADR Governance |

# Patch Instructions

Append this header and governance sections to `ADR-0007.md`.

ADR-0007: Storage Architecture

* Status: Accepted
* Date: 2026-09-16
* Decision Type: Core Architecture / Data Persistence
* Scope: Chronicle, World State, Books, Artifacts, Knowledge, Memory, Retrieval, Cache
* Supersedes: None
* Superseded by: None
* Related: ADR-0001 through ADR-0006

⸻

1. Context

Veda ต้องเก็บข้อมูลหลายชนิดที่มี semantics แตกต่างกันอย่างสิ้นเชิง:

Events
World State
Evidence
Knowledge
Books
Documents
Memory
Artifacts
Indexes
Caches
Logs

ปัญหาหลักไม่ใช่เพียง:

“ใช้ database อะไร?”

แต่คือ:

ข้อมูลแต่ละชนิดใครเป็นเจ้าของ และอะไรคือ Source of Truth?

หากระบบใช้ database เดียวเป็น source of truth สำหรับทุกอย่าง จะเกิดปัญหา:

Event
    ↓
World State
    ↓
Knowledge
    ↓
Memory
    ↓
Vector Index
    ↓
Cache

แล้ววันหนึ่ง developer แก้ vector index เพื่อแก้ bug และระบบก็เชื่อว่า hallucination ที่ถูก embed ไว้เมื่อวานเป็นความจริงใหม่ของโลก

Veda จึงต้องแยก storage ตาม semantic ownership

⸻

2. Decision

Veda จะใช้ Polyglot Logical Storage Architecture

โดยแบ่ง storage เป็น logical domains:

┌───────────────────────────────────────────┐
│                 VEDA                      │
├───────────────────────────────────────────┤
│  Chronicle / Event Store                  │
│  → Historical Source of Truth              │
├───────────────────────────────────────────┤
│  World Store / World Kernel                │
│  → Current World Projection                │
├───────────────────────────────────────────┤
│  Artifact / Book Store                     │
│  → Source Artifacts                        │
├───────────────────────────────────────────┤
│  Knowledge Store                           │
│  → Evaluated Claims / Knowledge Graph      │
├───────────────────────────────────────────┤
│  Memory Store                              │
│  → Agent-specific Memory                   │
├───────────────────────────────────────────┤
│  Retrieval Index                           │
│  → Search / Vector / Full-text             │
├───────────────────────────────────────────┤
│  Working Memory / Cache                    │
│  → Temporary Runtime State                 │
└───────────────────────────────────────────┘

แต่ละ storage มี ownership และ authority ของตัวเอง

⸻

3. Core Principle

Every persistent data domain must have one clearly defined semantic Source of Truth.

และ:

Derived indexes, projections, caches, embeddings, and snapshots are never authoritative merely because they are convenient to query.

⸻

4. Storage Ownership Matrix

Domain	Storage	Source of Truth
Historical Events	Chronicle/Event Store	Event History
Current World	World Store	Verified World Projection
Books	Artifact Store	Original Artifact
Documents	Artifact Store	Original Document
Evidence	Evidence Store / Chronicle refs	Evidence Record
Knowledge	Knowledge Store	Knowledge Record
Memory	Memory Store	Memory Record
Vector Search	Retrieval Index	No
Full-text Index	Retrieval Index	No
Cache	Cache	No
UI Projection	Materialized View	No
Snapshot	Snapshot Store	No
Runtime Working Memory	Working Memory	No
Operational Logs	Observability Store	Operational telemetry

⸻

5. Chronicle / Event Store

Chronicle เป็นเจ้าของ:

Historical Events

ตัวอย่าง:

IntentCreated
ActionAuthorized
LeaseIssued
ToolExecuted
ObservationRecorded
VerificationCompleted
WorldTransitionCommitted

Chronicle ต้อง:

* append-oriented
* durable
* traceable
* replayable
* version-aware
* provenance-aware
* integrity-protected

Historical record ต้องไม่ถูกแก้ไขแบบ silent mutation

⸻

6. Chronicle Is Historical Source of Truth

สำหรับ historical events:

Chronicle
    =
Source of Truth

ไม่ใช่:

World Store

และไม่ใช่:

Vector Database

และไม่ใช่:

Application Log

⸻

7. World Store

World Store เป็นเจ้าของ:

Current Authoritative World Projection

ตัวอย่าง:

Entity
Relationship
Current State
Current Status
Current Resource State
Current World View

World Store ไม่ได้เป็น owner ของ historical truth

ดังนั้น:

World Store
    ≠
Chronicle

⸻

8. World State Derivation

Canonical path:

Event
 ↓
Chronicle
 ↓
Projection Processor
 ↓
World Store
 ↓
Current World

ดังนั้น:

Chronicle
    → Historical Truth
World Store
    → Current World Projection

นี่ทำให้สามารถ:

* replay history
* rebuild projections
* recover state
* construct point-in-time views
* detect projection corruption

ได้

Event sourcing โดยทั่วไปใช้ event history เพื่อ derive state และ materialized views เป็น read-optimized projections ไม่ใช่ replacement ของ underlying history (AWS Documentation)

⸻

9. Snapshot

World snapshot สามารถสร้างได้เพื่อ performance:

Events 1..1,000,000
       ↓
Snapshot @ 999,000
       ↓
Replay 999,001..1,000,000

Snapshot เป็น:

Optimization

ไม่ใช่:

Source of Truth

ถ้า snapshot เสีย:

Delete Snapshot
↓
Replay Chronicle
↓
Rebuild

⸻

10. Artifact / Book Store

Books และ source documents เป็น Artifacts

ตัวอย่าง:

Book
PDF
EPUB
Markdown
Web Archive
Research Paper
Specification
Documentation
Source Code Archive
Dataset
Image
Audio
Video

Artifact Store เป็นเจ้าของ:

Original Source Artifact

ไม่ใช่ Memory Store

ไม่ใช่ Knowledge Store

ไม่ใช่ Vector Index

⸻

11. Books Are Not Memory

หนังสือ:

Book

ไม่ควรถูกจัดเก็บเป็น:

Memory

เพียงเพราะ Veda อ่านมันแล้ว

Canonical pipeline:

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
Knowledge

Memory เป็นสิ่งที่ Veda เลือก retain จาก interaction/experience

ดังนั้น:

Book ≠ Memory

⸻

12. Artifact Integrity

Artifact ต้องมี metadata เช่น:

artifact_id
content_hash
media_type
size
source
created_at
ingested_at
license
publisher
provenance

Content hash ใช้สำหรับ:

* deduplication
* integrity
* version tracking
* provenance
* reproducibility

Artifact content ต้องไม่ถูกแก้ไขโดยการเปลี่ยน source record เงียบ ๆ

Version ใหม่:

Artifact v1
Artifact v2

ไม่ใช่:

Artifact v1
↓
overwrite

⸻

13. Knowledge Store

Knowledge Store เป็นเจ้าของ:

Evaluated Knowledge

ตัวอย่าง:

Claim
Evidence
Confidence
Validity
Relationships
Contradictions
Derivations
Causal Hypothesis

Knowledge ไม่ใช่ source artifact

ดังนั้น:

Knowledge
    references
Artifact / Evidence

แต่ไม่กลืน artifact ต้นฉบับเข้ามาแทน

⸻

14. Evidence Storage

Evidence ต้องสามารถ trace กลับไป:

Source
Observation
Artifact
Collection
Transformation

ตัวอย่าง:

Artifact
 ↓
Extracted Passage
 ↓
Evidence
 ↓
Claim
 ↓
Knowledge

ถ้า Knowledge ถูกลบ:

Evidence

ไม่ควรถูกลบตามโดยอัตโนมัติ หาก evidence ยังมี retention requirement

⸻

15. Memory Store

Memory Store เป็นเจ้าของ agent-relative memory:

Working
Episodic
Semantic
Procedural
Preference
Policy
Failure
Prospective

Memory มี:

owner
visibility
retention
formation
confidence
provenance
access policy

Memory สามารถ reference:

Event
Experience
Knowledge
Artifact
Evidence

แต่ไม่เป็นเจ้าของข้อมูลเหล่านั้น

⸻

16. Memory Does Not Define World

ตัวอย่าง:

Memory:
"ฉันจำได้ว่าไฟล์อยู่ที่ /tmp/a.txt"

แต่ World Store อาจบอก:

/tmp/a.txt = DELETED

ดังนั้น:

Memory ≠ World State

Memory เป็น agent-relative representation

World State เป็น current modeled world

⸻

17. Retrieval Index

Retrieval Index ใช้สำหรับ:

Vector Search
Full-text Search
Semantic Search
Hybrid Retrieval
Similarity
Ranking

แต่:

Retrieval Index ≠ Source of Truth

หาก index เสีย:

Drop Index
↓
Rebuild

ข้อมูลต้นฉบับต้องยังอยู่

⸻

18. Embeddings

Embedding เป็น derived representation:

Document
 ↓
Chunk
 ↓
Embedding

Embedding ไม่ใช่ knowledge

และไม่ใช่ memory

ดังนั้น:

Embedding ≠ Knowledge
Embedding ≠ Truth
Embedding ≠ Source

ถ้า model embedding เปลี่ยน:

Old Embeddings
↓
Re-embed
↓
New Index

โดยไม่ต้องเปลี่ยน source artifact

⸻

19. Full-Text Index

Full-text index เป็น derived data:

Artifact
Knowledge
Memory
Events

สามารถสร้าง index ใหม่ได้

ดังนั้น:

Index Loss

ไม่ควรทำให้:

Source Data Loss

⸻

20. Working Memory / Cache

Working Memory และ cache ใช้สำหรับ runtime performance:

Current Context
Temporary Retrieval
Intermediate Reasoning State
Session Context
Computed Results

มี TTL หรือ lifecycle ตาม policy

หากหาย:

System must recover

ไม่ควรถือว่า:

Cache loss = historical loss

⸻

21. Operational Logs

Operational logs เป็นอีก category:

stdout
stderr
metrics
traces
debug logs
performance telemetry

Operational logs:

≠ Chronicle

เพราะ log อาจ:

* sampling
* rotate
* aggregate
* redact
* expire

Chronicle มี semantic responsibility ต่างออกไป

⸻

22. Storage Boundary

Canonical architecture:

                         ┌──────────────┐
                         │  Chronicle   │
                         │   History    │
                         └──────┬───────┘
                                │
                                ▼
                         ┌──────────────┐
                         │ World Kernel │
                         └──────┬───────┘
                                │
                                ▼
                         ┌──────────────┐
                         │ World Store  │
                         └──────────────┘
     ┌───────────────┐
     │ Artifact Store│
     │ Books/Docs    │
     └───────┬───────┘
             │
       ┌─────┴─────┐
       ▼           ▼
 Evidence      Knowledge
       │           │
       └─────┬─────┘
             ▼
       Retrieval Index
Experience
    ↓
Memory Store
Runtime Context
    ↓
Working Memory / Cache

⸻

23. Read Path

Canonical read:

Application / Brain
        ↓
World Query / Knowledge Query / Memory Query
        ↓
Policy / Visibility
        ↓
Authoritative Store
        ↓
Optional Retrieval Index
        ↓
Context Selection
        ↓
Brain

Retrieval index ใช้ค้นหา candidate

จากนั้นต้อง fetch authoritative record

⸻

24. Retrieval Is Discovery, Not Authority

ตัวอย่าง:

Vector Search คืน:

Knowledge #123
score = 0.94

ไม่ได้หมายความว่า:

Knowledge #123 = TRUE

ระบบต้อง:

Retrieve candidate
↓
Fetch authoritative record
↓
Check provenance
↓
Check validity
↓
Check contradiction
↓
Use in reasoning

⸻

25. Write Path

Canonical write:

External Observation
        ↓
Evidence
        ↓
Chronicle
        ↓
World Projection

Action:

Action
 ↓
Execution
 ↓
Observation
 ↓
Verification
 ↓
Event
 ↓
Chronicle
 ↓
World Projection

Knowledge:

Evidence
 ↓
Evaluation
 ↓
Knowledge Store

Memory:

Experience / Knowledge / Policy
 ↓
Memory Formation
 ↓
Memory Store

⸻

26. Transaction Boundary

การเปลี่ยนแปลงที่มีความหมายต่อ World ต้องมี transactional semantics ที่ชัดเจน

ตัวอย่าง:

Action
 ↓
Verification
 ↓
Event append
 ↓
World projection update

ห้ามเกิด:

World updated
แต่ Chronicle ไม่มี event

หรือ:

Chronicle committed
แต่ World projection อ้างว่า event อื่นเกิดขึ้น

หาก architecture ใช้ asynchronous projection ต้องมี:

projection_version
event_version
lag
reconciliation

⸻

27. Eventual Consistency

บาง read model สามารถเป็น eventually consistent:

Chronicle
   ↓
Projection
   ↓
Search Index

แต่ต้องประกาศ semantics ชัดเจน

ตัวอย่าง:

Chronicle:
committed
World projection:
processing
Search index:
stale

ผู้เรียกต้องสามารถรู้ได้ว่า data อยู่สถานะใด

⸻

28. Strong Consistency Boundary

ข้อมูลที่ใช้สำหรับ:

* authorization
* capability lease
* security policy
* World commit
* human approval
* transaction identity

ต้องอ่านจาก authoritative source ที่เหมาะสม

ไม่ควรใช้:

Vector Index
Cache
Stale Projection

เป็น authority สำหรับ security decision

⸻

29. Source-of-Truth Rule

ทุก domain ต้องตอบได้:

Who owns this data?
Where is its source of truth?
What is derived?
How is it rebuilt?
What happens if the index disappears?

ตัวอย่าง:

Event

Source:
Chronicle
Derived:
Projections
Indexes

World State

Source:
Verified World Projection / World Store
Historical basis:
Chronicle

Book

Source:
Artifact Store
Derived:
Chunks
Embeddings
Knowledge

Knowledge

Source:
Knowledge Store
Supporting:
Evidence
Artifact

Memory

Source:
Memory Store
Supporting:
Experience / Knowledge / Events

⸻

30. No Multiple Sources of Truth

ห้าม architecture มี:

World Store A
World Store B
Vector DB
Cache
Memory

แล้วไม่มีใครรู้ว่าอะไรถูก

ต้องกำหนด:

ONE SEMANTIC OWNER

ต่อ domain

ระบบสามารถมี replicas ได้:

Primary
Replica
Backup
Cache
Projection

แต่ replica ไม่กลายเป็น semantic owner เพียงเพราะมีข้อมูลเหมือนกัน

⸻

31. Backup

Backup ไม่ใช่ source of truth

Backup มีหน้าที่:

Recovery
Disaster Recovery
Historical Preservation

ตัวอย่าง:

Chronicle
 ↓
Backup

Backup ไม่ควรถูกใช้เป็น live authoritative source เว้นแต่ recovery protocol จะ promote อย่างชัดเจน

⸻

32. Disaster Recovery

Veda ต้องสามารถ:

Restore Chronicle
↓
Validate Integrity
↓
Replay
↓
Rebuild World Projection
↓
Rebuild Knowledge Index
↓
Rebuild Retrieval Index

เป้าหมาย:

Derived storage should be disposable and reconstructible whenever practical.

⸻

33. Data Loss Classes

Storage failure ต้องจำแนก:

Class A:
Cache Loss
Class B:
Index Loss
Class C:
Projection Loss
Class D:
Memory Loss
Class E:
Knowledge Loss
Class F:
Artifact Loss
Class G:
Chronicle Loss

ผลกระทบไม่เท่ากัน

โดยทั่วไป:

Chronicle Loss

เป็น critical historical integrity failure

ในขณะที่:

Vector Index Loss

เป็น rebuildable degradation

⸻

34. Books and Knowledge Pipeline

Books architecture:

Book
 ↓
Artifact Store
 ↓
Document Parser
 ↓
Document Structure
 ↓
Chunk
 ↓
Evidence
 ↓
Claim
 ↓
Knowledge Evaluation
 ↓
Knowledge Store
 ↓
Retrieval Index

แต่ต้นฉบับ:

Book

ยังคงอยู่ใน Artifact Store

นี่คือสิ่งที่ทำให้ Veda มี “library” จริง ไม่ใช่แค่กอง embeddings ที่ไม่มีใครรู้ว่ามาจากไหน

⸻

35. Versioning

Storage ต้องรองรับ versioning:

Artifact Version
Knowledge Version
Schema Version
World Version
Projection Version
Index Version

Version ต้องไม่ถูกใช้ปนกัน

ตัวอย่าง:

schema_version
world_version
object_version
projection_version

เป็นคนละ semantics

⸻

36. Deletion

Deletion policy ต้องแตกต่างตาม domain

ตัวอย่าง:

Cache

DELETE freely

Retrieval Index

DELETE + REBUILD

World Projection

REBUILD FROM HISTORY

Chronicle

RETENTION POLICY REQUIRED

Artifact

POLICY / LICENSE / RETENTION

Memory

FORGET according to policy

Memory forgetting ไม่ควรทำให้ historical Chronicle ถูกลบโดยอัตโนมัติ

⸻

37. Privacy

ข้อมูลหนึ่งชิ้นอาจมีหลาย copies:

Artifact
 ↓
Chunk
 ↓
Embedding
 ↓
Knowledge
 ↓
Memory
 ↓
Cache

ดังนั้น privacy/deletion policy ต้องพิจารณา:

Source
Derived Data
Indexes
Caches
Backups
Exports
Federated Copies

การลบ source ไม่ได้แปลว่า derived copies หายทันที

ต้องมี deletion propagation policy

⸻

38. Storage Technology Independence

ADR นี้ไม่ล็อกว่า Veda ต้องใช้:

PostgreSQL
SQLite
DuckDB
RocksDB
S3
MinIO
Neo4j
Qdrant
Weaviate

เพราะ decision นี้เป็น semantic storage architecture

ไม่ใช่ product selection

Technology selection จะอยู่ใน Implementation Specification / ADR แยก หากมี architectural significance

⸻

39. MVP Storage

MVP ไม่จำเป็นต้องมี database 7 ตัว

สามารถใช้:

SQLite / PostgreSQL
+
Filesystem/Object Storage
+
FTS

แต่ logical boundaries ต้องชัดเจนตั้งแต่วันแรก

ตัวอย่าง:

veda.db
├── chronicle_events
├── world_entities
├── world_relationships
├── evidence
├── knowledge
├── memory
└── metadata
artifacts/
├── books/
├── documents/
└── datasets/

Retrieval index อาจอยู่ใน database เดียวกันใน MVP

แต่ contract ต้องระบุว่า:

Index ≠ Source of Truth

⸻

40. Recommended Initial Layout

data/
├── chronicle/
├── world/
├── artifacts/
│   ├── books/
│   ├── documents/
│   ├── code/
│   └── datasets/
├── knowledge/
├── memory/
├── indexes/
├── cache/
├── snapshots/
└── backups/

Logical ownership:

chronicle/
    → historical truth
world/
    → current world projection
artifacts/
    → source material
knowledge/
    → evaluated knowledge
memory/
    → agent memory
indexes/
    → retrieval
cache/
    → temporary
snapshots/
    → performance/recovery aid
backups/
    → disaster recovery

⸻

41. Invariants

STA-001

Every persistent semantic domain has one defined Source of Truth.

STA-002

Chronicle owns historical event records.

STA-003

World Store owns current World projection.

STA-004

World Store does not replace historical Chronicle.

STA-005

Artifacts remain independent from derived Knowledge.

STA-006

Books are not stored as Memory merely because Veda read them.

STA-007

Knowledge does not replace source Evidence.

STA-008

Memory does not define authoritative World state.

STA-009

Retrieval indexes are never authoritative.

STA-010

Embeddings are derived data.

STA-011

Caches are never authoritative.

STA-012

Snapshots are rebuildable derivatives.

STA-013

Operational logs are not Chronicle.

STA-014

Historical records cannot be silently overwritten.

STA-015

Derived projections must be rebuildable where declared reconstructible.

STA-016

Security decisions must not rely solely on stale or non-authoritative indexes.

STA-017

Projection lag must be observable where eventual consistency exists.

STA-018

World commits must remain traceable to historical events.

STA-019

Data deletion must account for derived copies where policy requires.

STA-020

Backups do not become semantic authority automatically.

STA-021

Storage technology may change without changing domain ownership semantics.

STA-022

Schema version and object version are distinct concepts.

STA-023

World version and projection version are distinct concepts.

STA-024

Loss of a derived index must not imply loss of authoritative source data.

STA-025

A storage component must not silently become a second semantic Source of Truth.

⸻

42. Alternatives Considered

Alternative A: One Database for Everything

veda.db
└── everything

Advantages

* simple
* cheap
* easy backup
* easy development

Disadvantages

* semantic boundaries become unclear
* indexes may become accidental authority
* books/memory/knowledge get mixed
* difficult scaling
* difficult ownership
* difficult recovery semantics

Rejected as logical architecture.

A single physical database is still allowed for MVP.

⸻

Alternative B: Microservice Database per Domain

Chronicle DB
World DB
Knowledge DB
Memory DB
Artifact DB
Index DB
...

Advantages

* strong isolation
* independent scaling
* explicit ownership

Disadvantages

* huge operational complexity
* unnecessary for solo developer
* distributed transactions
* network latency
* deployment burden

Rejected for MVP physical deployment.

Logical separation remains mandatory.

⸻

Alternative C: Event Store as Source of Truth for Everything

Everything
 ↓
Events

Advantages

* strong history
* replayability
* excellent auditability

Disadvantages

* artifacts are not naturally event data
* books do not need event sourcing
* large binary objects should not be treated as event streams
* memory/knowledge semantics become awkward
* query complexity

Rejected.

Event sourcing applies where historical event reconstruction provides real value, not indiscriminately to every byte in the system. Event sourcing also introduces meaningful complexity around schema evolution, concurrency, and projections, so it should be applied deliberately. (Microsoft Learn)

⸻

Alternative D: Current State as Source of Truth

World Store
 ↓
overwrite old state

Advantages

* simple
* fast
* familiar

Disadvantages

* weak historical reconstruction
* weak auditability
* difficult forensic analysis
* difficult replay
* difficult correction analysis

Rejected for authoritative World history.

⸻

43. Consequences

Positive

Veda gets:

* explicit Source of Truth
* clean semantic ownership
* rebuildable indexes
* durable history
* real book/library architecture
* independent knowledge layer
* independent memory layer
* safer retrieval
* better disaster recovery
* easier future federation

⸻

Negative

System becomes more complex than CRUD:

Source
Projection
Index
Cache
Snapshot
Backup

ต้องเข้าใจว่าแต่ละชั้นมีหน้าที่อะไร

แต่ความซับซ้อนนี้เป็นผลจาก requirement ที่ Veda ต้อง:

remember
reason
audit
reconstruct
learn
verify
evolve

ไม่ใช่แค่เก็บ row แล้วเอาออกมาโชว์

⸻

44. Dependencies

Depends on:

RFC-0002 World Model
RFC-0003 Event Model
RFC-0004 State & World Transition
RFC-0012 Evidence Model
RFC-0013 Knowledge Model
RFC-0017 Memory Model
RFC-0021 Temporal Model
RFC-0031 Event/Audit/Trace Fabric
RFC-0032 Veda Chronicle
RFC-0047 Neural Package Format
ADR-0001 World Kernel
ADR-0002 Event / Chronicle Boundary
ADR-0003 Knowledge / Memory / Experience Boundary
ADR-0006 Common Object Envelope

Constrains:

RFC-0018 Brain Architecture
RFC-0020 Planner
RFC-0023 Future Engine
RFC-0024 Simulation
RFC-0035 Experience
RFC-0036 Learning
RFC-0043 World Delta
RFC-0044 Multi-Agent World
RFC-0045 Shared / Private World

⸻

45. Revisit Conditions

ADR นี้ควรถูกทบทวนหาก:

1. World Kernel ไม่สามารถ rebuild projection จาก Chronicle ได้ตามที่ architecture กำหนด
2. Veda ต้องรองรับ storage scale ที่เปลี่ยน semantic boundaries
3. Federation ต้องการ distributed Source-of-Truth semantics
4. Privacy requirements ทำให้ current storage ownership ไม่เพียงพอ
5. Artifact/Knowledge/Memory model ถูก redesign ในระดับ fundamental
6. Performance evidence แสดงว่า current projection architecture เป็น bottleneck ที่แก้ด้วย optimization ไม่ได้

การเปลี่ยน fundamental storage ownership ต้องสร้าง ADR ใหม่เพื่อ supersede ADR-0007

⸻

46. Final Decision

Veda จะใช้:

                    ┌──────────────────┐
                    │    CHRONICLE     │
                    │ Historical Truth │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │  WORLD KERNEL    │
                    │ Projection Logic │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │   WORLD STORE    │
                    │ Current World    │
                    └──────────────────┘
 ┌──────────────────┐
 │ ARTIFACT STORE   │
 │ Books / Docs     │
 └────────┬─────────┘
          │
          ├──────────────→ Evidence
          │                    │
          │                    ▼
          │               Knowledge
          │                    │
          └────────────────────┤
                               ▼
                        Retrieval Index
 Experience ───────────→ Memory Store
 Runtime Context ──────→ Working Memory / Cache

Canonical rules:

Chronicle is the historical source of truth.

World Store is the current World projection.

Artifacts are the source for books and documents.

Knowledge is evaluated representation derived from evidence and sources.

Memory is agent-relative retained information.

Retrieval indexes, embeddings, snapshots, projections, and caches are derived data unless explicitly designated otherwise by a future ADR.

One semantic domain must not silently acquire multiple Sources of Truth.

Status: ACCEPTED
