---
id: ADR-0009
title: Protocol Layering
status: Accepted
owner: Phupha
created: 2026-09-16
updated: 2026-09-16
review_cycle: Quarterly
architecture_stage: ADR_FREEZE

supersedes: null
superseded_by: null
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

ADR-0009: Protocol Layering

Status: Accepted
Date: 2026-09-16
Decision Type: Core Architecture / Protocol / Security Boundary

⸻

1. Context

Veda มีหลายกลไกที่เกี่ยวข้องกับการสื่อสารระหว่างระบบ ได้แก่

* Transport
* API
* Event Fabric
* MCP
* NCP
* World Delta
* Federation
* Common Object Envelope
* Capability / Authorization
* External World Interface

หากไม่มีการกำหนด layer อย่างชัดเจน แต่ละ protocol มีแนวโน้มที่จะค่อย ๆ ดูดหน้าที่ของ layer อื่นเข้ามา

ตัวอย่างเช่น:

MCP
 ├── transport
 ├── authentication
 ├── authorization
 ├── tool execution
 ├── world state
 └── agent communication

หรือ

NCP
 ├── context
 ├── execution
 ├── authority
 ├── memory
 └── world mutation

ผลลัพธ์คือ protocol กลายเป็น architecture โดยไม่ตั้งใจ

Veda ต้องป้องกันปัญหานี้ตั้งแต่ระดับสถาปัตยกรรม

⸻

2. Decision

Veda จะใช้ Layered Protocol Architecture

โดยแต่ละ layer มีหน้าที่เฉพาะ และไม่สามารถรับผิดชอบ semantic หรือ authority ของ layer อื่นโดยพลการ

┌─────────────────────────────────────────────┐
│ L8  Application / Experience               │
│     UI / CLI / API / Voice / Agent Apps    │
├─────────────────────────────────────────────┤
│ L7  Federation                             │
│     Cross-World Communication              │
├─────────────────────────────────────────────┤
│ L6  Capability Integration                 │
│     MCP / Tool Adapters                    │
├─────────────────────────────────────────────┤
│ L5  Cognitive Context                      │
│     NCP                                    │
├─────────────────────────────────────────────┤
│ L4  Domain Semantics                       │
│     World Delta / Action / Evidence /      │
│     Knowledge / Capability Schemas         │
├─────────────────────────────────────────────┤
│ L3  Message / Event                        │
│     Event Fabric / Request / Response      │
├─────────────────────────────────────────────┤
│ L2  Serialization / Envelope               │
│     JSON / CBOR / Protobuf / Envelope      │
├─────────────────────────────────────────────┤
│ L1  Transport                              │
│     HTTP / QUIC / WebSocket / IPC / etc.  │
├─────────────────────────────────────────────┤
│ L0  Runtime / Process                      │
│     OS / Process / Local Runtime           │
└─────────────────────────────────────────────┘

⸻

3. Layer Responsibilities

L0: Runtime

รับผิดชอบการทำงานจริงของ process และ runtime

ตัวอย่าง:

* Operating System
* Process
* Thread
* Local IPC
* Runtime
* Container

L0 ไม่เข้าใจ Veda semantics

⸻

L1: Transport

รับผิดชอบการส่งข้อมูลจากจุดหนึ่งไปอีกจุดหนึ่ง

ตัวอย่าง:

* HTTP
* HTTP/2
* HTTP/3
* QUIC
* WebSocket
* Unix Socket
* Local IPC

Transport ไม่ควรรู้ว่า message คือ

WorldDelta
Action
Knowledge
Capability

มันรู้เพียงว่า:

ส่ง bytes / message จาก A → B

⸻

4. L2: Serialization / Envelope

รับผิดชอบ representation และ metadata ที่จำเป็นต่อการส่ง object

ตัวอย่าง:

JSON
CBOR
Protobuf
MessagePack

และ Veda Common Object Envelope จาก ADR-0006

ตัวอย่าง:

{
  "id": "...",
  "type": "veda.action",
  "schema_version": "1.0",
  "world_id": "...",
  "actor_id": "...",
  "correlation_id": "...",
  "causation_id": "...",
  "provenance": {},
  "epistemic_status": "...",
  "content_hash": "...",
  "payload": {}
}

Serialization ไม่เป็นผู้ตัดสินว่า object นั้นมี authority หรือไม่

⸻

5. L3: Message / Event Layer

รับผิดชอบการส่ง message และ event

ประกอบด้วย:

* Request / Response
* Publish / Subscribe
* Event Fabric
* Correlation
* Causation
* Delivery semantics
* Retry
* Idempotency metadata

ตัวอย่าง:

ActionRequested
        ↓
Event Fabric
        ↓
ActionAuthorized
        ↓
Event Fabric
        ↓
ActionExecuted
        ↓
Event Fabric

Event Fabric ไม่ใช่ Chronicle

Event Fabric
    = delivery / routing
Chronicle
    = durable historical record

ตาม ADR-0002

⸻

6. L4: Domain Semantics

Layer นี้กำหนดว่า message นั้น “หมายถึงอะไร”

ตัวอย่าง:

Action
Goal
Intent
Capability
Authorization
Evidence
Knowledge
World Delta
Observation
Experience

World Delta อยู่ใน layer นี้

ไม่ใช่ transport

ตัวอย่าง:

WorldDelta {
    world_id
    base_version
    changes[]
    provenance
}

สามารถส่ง World Delta ผ่าน:

HTTP
MCP
NCP
Event Fabric
IPC
Federation

ได้ทั้งหมด

ดังนั้น:

World Delta ≠ HTTP
World Delta ≠ MCP
World Delta ≠ NCP

World Delta คือ semantic object

⸻

7. L5: Cognitive Context Layer

Veda จะใช้ NCP (Neural Context Protocol) เป็น protocol สำหรับแลกเปลี่ยน cognitive/world context

NCP อาจประกอบด้วย:

Identity Context
World Context
Goal Context
Intent Context
Memory Context
Knowledge Context
Temporal Context
Constraint Context
Uncertainty Context
Attention Context
Reasoning Context

ตัวอย่าง:

Veda A
   │
   │ NCP
   ▼
Veda B

NCP สามารถส่ง:

"นี่คือโลกที่ฉันเห็น"
"นี่คือเป้าหมาย"
"นี่คือข้อจำกัด"
"นี่คือ evidence ที่เกี่ยวข้อง"
"นี่คือ uncertainty"

แต่ NCP ไม่มีสิทธิ์ execute action โดยตัวมันเอง

สำคัญมาก:

NCP Context
    ≠
Authorization

และ

NCP Context
    ≠
Capability

⸻

8. L6: Capability Integration Layer

MCP อยู่ใน layer นี้ในฐานะ integration protocol / adapter mechanism

MCP สามารถเชื่อม Veda กับ:

* Tools
* Resources
* Prompts
* External systems
* MCP servers

MCP สเปกปัจจุบันมีทั้ง tool/resource/prompt concepts และ authorization mechanisms อยู่ใน protocol ecosystem แต่สำหรับ Veda สิ่งเหล่านี้ยังต้องอยู่ภายใต้ authorization และ Execution Control Plane ของ Veda ไม่ใช่กลายเป็น authority เอง (Model Context Protocol Blog)

Canonical path:

Veda Brain
    ↓
Execution Control Plane
    ↓
Authorization
    ↓
Capability Lease
    ↓
Capability Registry
    ↓
MCP Adapter
    ↓
MCP Server
    ↓
External Tool

ไม่อนุญาต:

Brain
  ↓
MCP
  ↓
Tool

โดยไม่มี Control Plane

⸻

9. L7: Federation

Federation ใช้เมื่อ Veda instance หนึ่งต้องสื่อสารกับ:

Veda instance อื่น
Agent อื่น
World อื่น
Organization อื่น
Remote Knowledge System
Remote Capability System

Federation สามารถใช้ protocol หลายตัวด้านล่างได้

เช่น:

Federation
   ↓
NCP
   ↓
HTTP

หรือ

Federation
   ↓
World Delta
   ↓
Event Fabric
   ↓
QUIC

Federation ไม่มี authority เหนือ local World โดยอัตโนมัติ

Remote Veda สามารถเสนอ:

Request
Proposal
World Delta
Knowledge
Capability Request

แต่ local Veda ต้องตัดสินตาม local policy

Remote Authority
       ≠
Local Authority

⸻

10. L8: Application / Experience

Layer สูงสุดประกอบด้วย:

* CLI
* Web UI
* Desktop UI
* iPhone
* Voice Interface
* API
* Agent Applications
* Developer Tools

Application สามารถร้องขอ:

Create file
Read book
Research topic
Run simulation
Execute workflow

แต่ไม่ควร bypass architecture

ตัวอย่าง:

UI
 ↓
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
Control Plane
 ↓
Capability
 ↓
Tool

⸻

11. Canonical Protocol Paths

11.1 Tool Execution

Application
    ↓
Intent
    ↓
Goal
    ↓
Planner
    ↓
Decision
    ↓
Authorization
    ↓
Capability Lease
    ↓
Execution Control Plane
    ↓
MCP Adapter
    ↓
Transport
    ↓
External Tool
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

11.2 Cognitive Context Exchange

Brain A
   ↓
NCP
   ↓
Transport
   ↓
NCP
   ↓
Brain B

แต่การได้รับ context ไม่ได้หมายความว่าได้รับ authority

⸻

11.3 World Delta

World Kernel
      ↓
World Delta
      ↓
Federation / API / Event Fabric
      ↓
Transport
      ↓
Remote World Kernel
      ↓
Validation
      ↓
Authorization
      ↓
Conflict Resolution
      ↓
Commit

World Delta เป็น semantic representation

ไม่ใช่ transport protocol

⸻

11.4 Event

Domain
   ↓
Domain Event
   ↓
Event Fabric
   ↓
Transport
   ↓
Event Consumer
   ↓
Chronicle
   ↓
Projection

Event Fabric ไม่สามารถทำให้ event กลายเป็น truth ได้

⸻

12. Protocol vs Domain vs Authority

Veda จะรักษาสามสิ่งนี้แยกจากกันอย่างเด็ดขาด

Protocol
"What is being communicated?"
Domain
"What does it mean?"
Authority
"Who is allowed to do what?"

ตัวอย่าง:

MCP
= communication/integration
World Delta
= semantic state change
Authorization
= authority decision

ดังนั้น:

MCP ≠ Authority
NCP ≠ Authority
World Delta ≠ Authority
Transport ≠ Authority
Event ≠ Authority

⸻

13. Security Model

13.1 Authentication

สามารถเกิดที่:

Transport
Session
Protocol
Federation

แต่ authentication ไม่เท่ากับ authorization

Authenticated Actor
        ≠
Authorized Actor

⸻

13.2 Authorization

Authorization ต้องถูกประเมินโดย Veda Control Plane ตาม ADR-0005

ไม่อนุญาตให้:

MCP server
NCP packet
World Delta
Tool description
Remote agent
Model output

เป็นผู้สร้าง authority ให้ตัวเอง

⸻

14. Capability Attenuation

เมื่อ authority เดินทางผ่านหลาย layer หรือ federation

authority ต้องไม่เพิ่มขึ้น

ตัวอย่าง:

User
 ↓
Veda
 ↓
Agent
 ↓
Capability Lease
 ↓
MCP
 ↓
Tool

ทุกขั้นสามารถ:

preserve
restrict
attenuate

authority ได้

แต่ไม่สามารถ:

escalate

authority ได้

⸻

15. Versioning

Veda จะไม่ใช้ version เดียวครอบทุกอย่าง

แยกอย่างน้อย:

Protocol Version
Schema Version
Object Version
World Version
Implementation Version

ตัวอย่าง:

protocol_version = 2
schema_version   = 1.4
object_version   = 83
world_version    = 10482
implementation   = 0.7.0

ห้ามตีความว่า:

protocol v2
=
schema v2
=
world v2

เพราะมันไม่ใช่สิ่งเดียวกัน

⸻

16. Error Model

Veda จะแยก error ตาม layer

Transport Error
    ↓
Protocol Error
    ↓
Serialization Error
    ↓
Semantic Validation Error
    ↓
Authentication Error
    ↓
Authorization Error
    ↓
Capability Error
    ↓
Execution Error
    ↓
Verification Error
    ↓
Domain Conflict

ตัวอย่าง:

HTTP timeout

ไม่ควรถูกตีความเป็น:

Tool failed

และ:

Tool returned success

ไม่ควรถูกตีความเป็น:

World outcome verified

⸻

17. Timeout / Cancellation

Deadline และ cancellation ต้องสามารถ propagate ผ่าน layer ได้

ตัวอย่าง:

User Deadline
      ↓
Intent
      ↓
Plan
      ↓
Execution
      ↓
MCP
      ↓
Tool

แต่แต่ละ layer มี responsibility ของตัวเอง

Timeout ของ transport ไม่ได้หมายความว่า external operation หยุดแล้วเสมอไป

ดังนั้น Veda ต้องรองรับ:

UNKNOWN EFFECT STATE

และต้องเข้าสู่ verification/recovery flow

⸻

18. Replay / Idempotency

Retryable protocol operations ต้องมี:

action_id
request_id
idempotency_key
correlation_id
causation_id

เพื่อป้องกัน:

duplicate execution
duplicate event
duplicate world mutation

โดยเฉพาะ operation เช่น:

transfer money
delete file
send message
deploy code
create resource

⸻

19. Anti-Replay

Authorization และ capability lease ต้องมีขอบเขต:

subject
capability
target
operation
time
expiry
audience
risk
operation count

ดังนั้น message ที่ถูก replay หลัง lease หมดอายุจะไม่สามารถ execute ได้

⸻

20. Protocol Downgrade Protection

Veda ต้องไม่ downgrade protocol โดยอัตโนมัติ หาก downgrade ทำให้ security หรือ semantic guarantees ลดลง

ตัวอย่าง:

Protocol v3
      ↓
Protocol v1

ต้องตรวจ policy ก่อน

ห้าม:

"v3 ใช้ไม่ได้ งั้นใช้ v1 ไปก่อน"

เพราะนั่นเป็นสูตรสำเร็จของระบบที่วันหนึ่งจะโดนโจมตีแล้วทุกคนทำหน้าเหมือนไม่รู้ว่าเกิดอะไรขึ้น

⸻

21. Observability

ทุก protocol layer ต้องรักษา trace context

อย่างน้อย:

trace_id
correlation_id
causation_id
parent_id
actor_id
world_id

ตัวอย่าง:

User Request
 trace_id = ABC
    ↓
Brain
 trace_id = ABC
    ↓
Control Plane
 trace_id = ABC
    ↓
MCP
 trace_id = ABC
    ↓
Tool
 trace_id = ABC

ทำให้ Veda สามารถตอบได้ว่า:

ใครเริ่ม
อะไรถูกตีความ
ใครตัดสิน
ใครอนุญาต
ใช้ capability อะไร
เรียก tool ไหน
เกิดอะไรขึ้น
verify อย่างไร
World เปลี่ยนอย่างไร

⸻

22. Protocol Boundaries

สิ่งต่อไปนี้เป็นข้อห้ามระดับ architecture

MCP ห้าม

กำหนด World truth
สร้าง Authorization
แก้ Constitution
เพิ่ม Capability ให้ตัวเอง
Bypass Control Plane

NCP ห้าม

Execute Tool
Grant Authority
Commit World State
Modify Policy

World Delta ห้าม

Authorize Action
Execute Tool
Bypass Verification

Event Fabric ห้าม

เป็น Source of Truth
ตัดสิน Authority
แก้ World โดยตรง

Transport ห้าม

ตีความ Domain Semantics
สร้าง Authority

Federation ห้าม

บังคับ Local World
ข้าม Local Policy
เพิ่ม Remote Authority ให้ตัวเอง

⸻

23. MVP Implementation

ใน MVP ไม่จำเป็นต้องสร้าง distributed protocol stack จริงทั้งหมด

สามารถใช้:

Single Process
Single Runtime
In-Process Calls
Single Database

ได้

แต่ logical boundaries ต้องยังคงอยู่

ตัวอย่าง:

interface Transport
interface Protocol
interface DomainCodec
interface EventBus
interface CapabilityAdapter
interface FederationAdapter

ใน MVP:

InProcessTransport

สามารถ implement Transport ได้

และ:

InProcessEventBus

สามารถ implement Event Fabric ได้

โดย architecture ยังพร้อมแยกออกเป็น network services ภายหลัง

⸻

24. Alternatives Considered

Alternative A: Universal Protocol

สร้าง protocol เดียวที่ทำทุกอย่าง

Veda Protocol
 ├── transport
 ├── context
 ├── tools
 ├── world
 ├── federation
 └── authority

Rejected

เหตุผล:

* coupling สูง
* versioning ยาก
* security boundary ไม่ชัด
* protocol กลายเป็น architecture
* migration ยาก

⸻

Alternative B: MCP เป็น Universal Veda Protocol

ใช้ MCP ครอบ:

Tools
World
Memory
Context
Federation
Execution

Rejected

MCP เหมาะกับ integration ระหว่าง AI applications กับ tools/resources/prompts แต่ Veda มี domain model และ authority architecture ที่กว้างกว่านั้น (MCP TypeScript SDK)

Veda จะใช้ MCP เป็น adapter/integration layer

ไม่ใช่ constitutional protocol ของ Veda

⸻

Alternative C: NCP เป็น Universal Bus

ให้ NCP ขนทุกอย่าง

Rejected

เพราะ NCP มีหน้าที่ด้าน cognitive context

ไม่ควรกลายเป็น:

Transport
Event Bus
Execution Protocol
Authorization Protocol
Federation Protocol

ทั้งหมดในตัวเดียว

⸻

Alternative D: REST Only

ใช้ HTTP/REST สำหรับทุกอย่าง

Rejected

REST สามารถเป็น transport/application mechanism ได้ แต่ไม่ควรกลายเป็น semantic architecture ของ Veda

⸻

Alternative E: Ad-hoc Protocols

ให้แต่ละ subsystem สร้าง protocol ของตัวเอง

Rejected

จะเกิด protocol fragmentation และทำให้ audit/security/debugging ยากขึ้น

⸻

25. Consequences

Positive

* Protocol boundaries ชัด
* ลด coupling
* MCP เปลี่ยนได้โดยไม่กระทบ World Kernel
* NCP สามารถ evolve แยกจาก execution
* Transport สามารถเปลี่ยนได้
* Federation สามารถเพิ่มภายหลัง
* Security boundaries ตรวจสอบง่าย
* Distributed deployment ทำได้ในอนาคต
* Protocol versioning ชัด
* Debugging และ observability ดีขึ้น

Negative

* จำนวน abstraction เพิ่ม
* implementation ซับซ้อนขึ้น
* ต้องรักษา contract หลายระดับ
* มี overhead ในการออกแบบ protocol
* ต้องมี discipline ไม่ให้ layer ข้ามหน้าที่

⸻

26. Architectural Invariants

PRO-001

Protocol does not grant authority.

PRO-002

Transport does not define domain semantics.

PRO-003

Serialization does not define authority.

PRO-004

Event Fabric does not equal Chronicle.

PRO-005

World Delta is semantic data, not transport.

PRO-006

NCP does not execute tools.

PRO-007

MCP does not define Veda authority.

PRO-008

Federation does not override local authority.

PRO-009

No protocol may bypass the Execution Control Plane.

PRO-010

Lower layers cannot invent upper-layer semantics.

PRO-011

Authorization is evaluated independently from transport.

PRO-012

Remote requests are subject to local policy.

PRO-013

Protocol version is distinct from schema version.

PRO-014

Schema version is distinct from object version.

PRO-015

Object version is distinct from World version.

PRO-016

Correlation identity must survive protocol boundaries.

PRO-017

Retryable operations require idempotency protection.

PRO-018

Replay protection is required for authority-bearing operations.

PRO-019

Protocol downgrade cannot silently reduce security guarantees.

PRO-020

Authentication does not imply authorization.

PRO-021

Capability does not imply permission.

PRO-022

Remote capability cannot exceed delegated authority.

PRO-023

NCP context cannot grant permission.

PRO-024

MCP tool metadata cannot grant permission.

PRO-025

World Delta cannot commit itself.

PRO-026

Event delivery cannot become World truth without Chronicle/Projection processing.

PRO-027

Transport failure does not imply external side-effect failure.

PRO-028

Execution success does not imply verified outcome.

PRO-029

Secrets must not be propagated through generic context objects without explicit policy.

PRO-030

Human override remains available for policy-defined actions.

⸻

27. Dependencies

This ADR depends on:

RFC-0003 Event Model
RFC-0009 Capability Model
RFC-0010 Authorization & Policy
RFC-0011 Capability Lease
RFC-0028 Tool & Capability Registry
RFC-0029 External World Interface
RFC-0030 MCP Integration
RFC-0031 Event/Audit/Trace Fabric
RFC-0042 NCP
RFC-0043 World Delta Protocol
RFC-0046 Federation Protocol
ADR-0002 Event / Chronicle Boundary
ADR-0005 Execution Control Plane
ADR-0006 Common Object Envelope
ADR-0007 Storage Architecture

⸻

28. Final Decision

Veda จะไม่สร้าง “Universal Protocol”

แต่จะสร้าง Protocol Stack

โดยหลักการ:

Transport
    ↓
Serialization / Envelope
    ↓
Message / Event
    ↓
Domain Semantics
    ↓
Cognitive Context / Capability Integration
    ↓
Federation
    ↓
Application

พร้อมกับ authority boundary ที่แยกออกจาก protocol:

Protocol
    ↓
Communication
Domain
    ↓
Meaning
Control Plane
    ↓
Authority
World Kernel
    ↓
Authoritative World State

ดังนั้นแกนกลางของ Veda จะไม่ขึ้นกับ MCP, NCP, HTTP หรือ protocol ใด protocol หนึ่ง

Protocol เป็นเพียงวิธีที่ระบบคุยกัน

ไม่ใช่สิ่งที่ทำให้ Veda มีสิทธิ์ทำอะไร

⸻

29. Revisit Conditions

ADR นี้ควรถูกทบทวนเมื่อ:

1. Veda เริ่มมี distributed deployment จริง
2. มี remote Veda federation จริง
3. NCP มี specification ที่ใช้งานจริง
4. MCP มี requirement ที่ขัดกับ Veda Control Plane
5. Protocol overhead กลายเป็น performance bottleneck
6. จำเป็นต้องรองรับ offline/intermittent networks
7. มี hardware/edge protocol ที่ต้อง integrate
8. มี security model ใหม่ที่ทำให้ layering ปัจจุบันไม่เพียงพอ

จนกว่าจะเกิดเงื่อนไขเหล่านี้:

ADR-0009 ถือเป็น architectural constraint
