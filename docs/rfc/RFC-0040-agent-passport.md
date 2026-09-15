RFC-0040 — Agent Passport

Status: Architecture
Layer: 15 — Identity & Trust
Depends On: RFC-0001, RFC-0009, RFC-0010, RFC-0011, RFC-0012, RFC-0013, RFC-0028, RFC-0029, RFC-0032, RFC-0033, RFC-0038, RFC-0039
Used By: RFC-0041, RFC-0044, RFC-0045, RFC-0046, RFC-0047, RFC-0048

⸻

1. Abstract

RFC-0040 กำหนด Agent Passport ของ Veda

Agent Passport คือชุดข้อมูลและหลักฐานที่ใช้บอกระบบอื่นว่า:

Agent นี้คือใคร
อยู่ภายใต้ identity ใด
ถูกสร้างจากอะไร
ควบคุมโดยใคร
มี capabilities อะไร
ได้รับ authority อะไร
มีข้อจำกัดอะไร
มี trust evidence อะไร
มี lineage อย่างไร
และข้อมูลเหล่านี้ตรวจสอบได้อย่างไร

Passport ทำหน้าที่เป็น:

Portable Agent Identity & Accountability Bundle

ไม่ใช่:

Universal Permission Token

ดังนั้น:

Passport ≠ Identity
Passport ≠ Trust
Passport ≠ Authority
Passport ≠ Capability
Passport ≠ Credential
Passport ≠ Policy
Passport ≠ Constitution

Passport เป็น representation + evidence bundle ของ agent

⸻

2. Motivation

เมื่อ Veda เริ่มมีหลาย agent:

Veda
 ├── Research Agent
 ├── Coding Agent
 ├── Browser Agent
 ├── Security Agent
 ├── Planning Agent
 └── Verification Agent

และในอนาคต:

Veda Agent
      ↕
External Agent

ระบบต้องมีวิธีตอบ:

Who are you?
Who controls you?
What are you?
What can you do?
What are you allowed to do?
How can I verify that?
How long is this information valid?

การส่งเพียง:

{
  "name": "ResearchAgent"
}

ไม่มีประโยชน์ทาง security มากนัก

Passport จึงรวบรวม evidence ที่จำเป็น

⸻

3. Core Principle

Passport ต้องแยก:

Identity
Capability
Authority
Trust
Evidence
Provenance

ตัวอย่าง:

Agent Identity:
research-agent-01
Capability:
web.read
Authority:
temporary lease
Trust:
medium
Evidence:
signed attestation
Status:
active

จึงเป็นไปได้ว่า:

Known Agent
+
Valid Passport
+
No Current Authority

และนั่นเป็น state ที่ถูกต้อง

⸻

4. Passport Goals

Agent Passport ต้อง:

1. ระบุ agent
2. อ้างอิง identity
3. แสดง lineage
4. แสดง controlling entity
5. แสดง capabilities
6. แสดง authority references
7. แสดง trust evidence
8. แสดง provenance
9. แสดง version
10. แสดง lifecycle state
11. รองรับ expiration
12. รองรับ revocation
13. รองรับ cryptographic verification
14. รองรับ delegation
15. รองรับ multi-agent systems
16. รองรับ federation
17. รองรับ offline verification เมื่อเป็นไปได้
18. รองรับ audit
19. รองรับ privacy-preserving disclosure
20. ป้องกัน passport forgery

⸻

5. Non-Goals

Passport ไม่กำหนด:

* agent intelligence
* reasoning
* personality
* memory
* model weights
* authorization policy
* trust scoring algorithm
* execution semantics
* world state
* human identity proofing

⸻

6. Passport Structure

โครงสร้างพื้นฐาน:

agent_passport:
  passport_id:
  version:
  agent_identity:
    identity_ref:
    identity_type:
    namespace:
  system_identity:
  instance_identity:
  controller_identity:
  agent_profile:
    name:
    description:
    agent_type:
    purpose:
  runtime:
    environment:
    platform:
    version:
    status:
  capabilities:
    - capability_ref:
  authority:
    - authorization_ref:
    - lease_ref:
  trust:
    trust_refs:
    assurance_level:
  provenance:
    origin:
    parent_refs:
    lineage_refs:
    creation_event:
  artifacts:
    runtime_artifact:
    configuration_artifact:
    model_refs:
    skill_refs:
  attestations:
    - attestation_ref:
  security:
    key_refs:
    security_status:
  validity:
    issued_at:
    expires_at:
  revocation:
    status:
    revocation_ref:
  signatures:
    - signature:
  privacy:
    disclosure_profile:

⸻

7. Passport Identity

Passport ต้องอ้างถึง RFC-0039:

Passport
   ↓
Agent Identity
   ↓
Cryptographic Identity

Passport ห้ามสร้าง identity ขึ้นมาเอง

ดังนั้น:

passport.agent_identity

ต้อง resolve ไปยัง:

RFC-0039 Identity

⸻

8. Passport ID

Passport มี ID ของตัวเอง:

veda:passport:<id>

เพราะ:

Identity ≠ Passport

Identity สามารถมี Passport หลาย version ได้

ตัวอย่าง:

Agent Identity A
Passport v1
Passport v2
Passport v3

⸻

9. Passport Version

Passport ต้อง version ได้

Passport v1
      ↓
Passport v2
      ↓
Passport v3

แต่การเปลี่ยน version ต้องไม่ทำให้ historical passport หาย

Chronicle ต้องสามารถตอบ:

What did this agent present at T1?

⸻

10. Agent Profile

Agent profile เป็น descriptive metadata:

agent_profile:
  name:
  description:
  agent_type:
  purpose:
  owner_ref:

ตัวอย่าง:

Name:
Veda Research Agent
Type:
Research Agent
Purpose:
Evidence-oriented web research

ข้อมูลนี้ไม่ใช่ authority

⸻

11. Agent Type

ประเภท agent อาจเป็น:

GENERAL
RESEARCH
CODING
PLANNING
EXECUTION
VERIFICATION
SECURITY
MONITORING
PERSONAL_ASSISTANT
SPECIALIST
ORCHESTRATOR
WORKER

Agent type เป็น metadata

ไม่ใช่ permission

⸻

12. Runtime Binding

Passport ต้องสามารถบอกว่า agent กำลังทำงานอยู่ที่ไหน:

runtime:
  instance_ref:
  process_ref:
  host_ref:
  environment:
  software_version:

แต่ไม่ควรเปิดเผยรายละเอียด infrastructure เกินจำเป็น

⸻

13. Controller Binding

Passport สามารถระบุ controller:

Agent
   ↓
Controller
   ↓
Human / Organization / Parent Agent

แต่ต้องมี evidence

ไม่ใช่:

controller = "Kaonashi"

แล้วถือว่าจริง

⸻

14. Parent Agent

Agent สามารถถูกสร้างโดย agent อื่น:

Parent Agent
      ↓
Child Agent

Passport ต้องรองรับ:

lineage:
  parent_agent:
  creation_context:
  delegation_ref:
  creation_authorization:

เพื่อให้ตรวจสอบ chain of custody ได้

⸻

15. Agent Lineage

ตัวอย่าง:

Human
  ↓
Veda Root
  ↓
Orchestrator
  ↓
Coding Agent
  ↓
Test Agent

Passport ของ Test Agent ต้องสามารถอ้างถึง:

parent
ancestor
creation event
delegation
authority chain

⸻

16. Capability Declaration

Passport สามารถแสดง capability ที่ agent รองรับ

ตัวอย่าง:

Capabilities:
- filesystem.read
- filesystem.write
- git.read
- git.commit
- web.read

แต่:

Capability Supported
      ≠
Capability Currently Authorized

จึงต้องแยก:

Declared Capability

กับ:

Active Capability Lease

⸻

17. Authority Declaration

Passport อาจแสดง authorization reference:

authority:
  authorization_ref:
  lease_ref:
  scope:
  issued_by:
  expires_at:

แต่ Passport ไม่ควรฝังสิทธิ์แบบ:

"this agent may do anything"

authority ต้อง resolve ไปยัง RFC-0010/RFC-0011

⸻

18. Capability vs Authority

ตัวอย่าง:

Agent supports:
git.commit

ไม่ได้หมายความว่า:

Agent may commit now.

อาจมี:

Capability:
git.commit
Current Authority:
NONE

หรือ:

Capability:
git.commit
Current Lease:
repository=X
branch=feature
expires=10 minutes

⸻

19. Trust Evidence

Passport สามารถรวม:

trust:
  assurance_level:
  trust_refs:
  attestation_refs:
  verification_refs:
  history_refs:

แต่ Passport ไม่ควรประกาศ:

trust = 100%

โดยไม่มี basis

Trust จะถูกประเมินโดย RFC-0041

⸻

20. Attestation

Attestation สามารถระบุ:

Agent identity verified
Agent artifact verified
Controller verified
Capability declaration verified
Runtime verified
Policy version verified

แต่ละ assertion ต้องมี:

issuer
subject
claim
evidence
timestamp
expiration
signature

⸻

21. Evidence Bundle

Passport สามารถ reference evidence:

evidence:
  identity:
  artifact:
  controller:
  runtime:
  authorization:
  security:

Evidence ไม่จำเป็นต้องถูกฝังทั้งหมดใน Passport

ควรใช้ references:

Passport
   ↓
Evidence Reference
   ↓
Evidence Store / Chronicle

เพื่อป้องกัน Passport ใหญ่เกินไป

⸻

22. Provenance

Passport ต้องสามารถตอบ:

Who created this agent?
From which artifact?
Which model?
Which configuration?
Which evolution?
Which parent?
Under which authorization?

ตัวอย่าง:

provenance:
  source_artifact:
  source_version:
  parent_identity:
  creation_event:
  creation_authorization:
  evolution_refs:

⸻

23. Artifact Binding

Agent behavior อาจขึ้นกับ:

Runtime
Model
Prompt
Skill
Tool
Configuration
Policy

Passport จึงควร reference artifact identities

Agent Passport
    ├── Runtime Artifact
    ├── Model Artifact
    ├── Skill Artifacts
    ├── Configuration Artifact
    └── Policy Version

⸻

24. Model Binding

ถ้า agent ใช้ model:

Research Agent
    ↓
Model A

Passport อาจแสดง:

models:
  - model_identity:
    version:
    provider:
    purpose:

แต่ model identity ไม่ควรถูกตีความว่า model เป็น agent

Model ≠ Agent

⸻

25. Skill Binding

Agent อาจมี skills:

Research Skill
Coding Skill
Browser Skill
Git Skill

แต่ skill:

Skill ≠ Capability
Skill ≠ Authority

Skill เป็น procedural artifact

Capability เป็น operational ability

Authority เป็น permission

⸻

26. Security Status

Passport ต้องมี security status:

NORMAL
DEGRADED
SUSPICIOUS
QUARANTINED
COMPROMISED
REVOKED

แต่สถานะต้องมาจาก evidence/diagnostics

Agent ไม่ควรประกาศ:

security_status = NORMAL

เองแล้วจบเรื่อง

⸻

27. Passport Lifecycle

DRAFT
   ↓
ISSUED
   ↓
ACTIVE
   ↓
UPDATED
   ↓
RENEWED

Alternative:

SUSPENDED
QUARANTINED
REVOKED
EXPIRED
RETIRED

⸻

28. Passport Issuance

Passport issuance:

Agent Identity
      ↓
Validate Identity
      ↓
Validate Agent
      ↓
Validate Artifacts
      ↓
Resolve Controller
      ↓
Resolve Capabilities
      ↓
Resolve Authority
      ↓
Collect Evidence
      ↓
Create Passport
      ↓
Sign Passport
      ↓
Record Chronicle Event

⸻

29. Passport Renewal

เมื่อ Passport หมดอายุ:

Old Passport
      ↓
Revalidation
      ↓
New Evidence
      ↓
New Passport

ไม่ควรแก้ Passport เดิมแบบเงียบๆ

⸻

30. Passport Revocation

Passport สามารถถูก revoke เมื่อ:

* identity revoked
* agent compromised
* artifact compromised
* controller relationship invalid
* authority revoked
* security policy violation
* provenance invalid
* credential compromise

Revocation ต้องมี reason และ evidence

⸻

31. Passport vs Identity Revocation

สำคัญมาก:

Passport revoked
      ≠
Identity revoked

ตัวอย่าง:

Passport v1 compromised
      ↓
Revoke Passport v1
      ↓
Identity remains active
      ↓
Issue Passport v2

แต่:

Identity revoked
      ↓
All dependent passports become invalid

ตาม policy

⸻

32. Short-Lived Agents

สำหรับ worker ที่มีอายุสั้น:

Task
 ↓
Worker Agent
 ↓
Task Complete
 ↓
Retire

Passport สามารถมี expiration สั้น

เช่น:

expires_at = task_completion + grace_period

ข้อดี:

* ลด stale credentials
* ลด revocation burden
* จำกัด blast radius
* ลด long-lived identity exposure

แนวคิดเรื่อง short-lived agent credentials และ revocable agent principals ก็ปรากฏในงานหารือเรื่อง agentic delegation ปัจจุบัน 

⸻

33. Passport Presentation

เมื่อ Veda ติดต่อระบบอื่น:

Veda
   ↓
Present Passport
   ↓
Remote System
   ↓
Resolve Identity
   ↓
Verify Signature
   ↓
Check Status
   ↓
Evaluate Trust
   ↓
Evaluate Authorization

Remote system ไม่ควรเชื่อ Passport เพียงเพราะได้รับมา

⸻

34. Selective Disclosure

ไม่จำเป็นต้องส่ง Passport ทั้งหมด

ตัวอย่าง:

Need:
Agent Identity
Capability
Expiration
Attestation

ไม่จำเป็นต้องเปิด:

Internal Memory
Private Infrastructure
Internal Goals
Sensitive Knowledge
Private Logs

Passport presentation จึงควรรองรับ disclosure profiles:

MINIMAL
STANDARD
OPERATIONAL
AUDIT
FULL

⸻

35. Minimal Passport

สำหรับ low-risk interaction:

passport:
  identity:
  agent_type:
  status:
  validity:
  signature:

⸻

36. Operational Passport

สำหรับ tool/service interaction:

passport:
  identity:
  instance:
  capabilities:
  authority_refs:
  attestations:
  validity:
  security_status:
  signature:

⸻

37. Audit Passport

สำหรับ forensic/audit:

passport:
  identity:
  instance:
  process:
  controller:
  lineage:
  artifacts:
  authority:
  provenance:
  attestations:
  security:
  evolution_refs:
  validity:
  signatures:

⸻

38. Passport Signature

Passport ต้องสามารถ signed ได้

Passport
    ↓
Canonical Representation
    ↓
Hash
    ↓
Signature
    ↓
Identity Key

Verifier ต้องสามารถตรวจ:

Signature
Key Status
Passport Integrity
Expiration
Revocation
Issuer

⸻

39. Canonicalization

เพื่อป้องกัน signature ambiguity:

Passport ต้องมี canonical serialization

เช่น:

JSON Canonicalization

หรือ format ที่ Veda กำหนด

หลัก:

Same semantic passport
→
Same canonical representation
→
Same digest

⸻

40. Passport Replay

Passport ที่ valid วันนี้อาจไม่ valid พรุ่งนี้

Verifier ต้องตรวจ:

issued_at
expires_at
revocation
status
nonce/context

สำหรับ consequential interactions

⸻

41. Passport Binding to Context

Passport บางประเภทควรถูก bind กับ:

Audience
Purpose
Session
Transaction
Environment

เช่น:

binding:
  audience:
  purpose:
  session_ref:
  transaction_ref:

เพื่อป้องกัน:

Passport issued for GitHub
      ↓
Replay against Banking API

⸻

42. Passport Delegation Chain

ตัวอย่าง:

Human
  ↓
Veda Root
  ↓
Research Agent
  ↓
Web Worker

แต่ละ hop ต้องสามารถตรวจได้ว่า:

Who delegated?
To whom?
For what scope?
Until when?
Under which policy?

⸻

43. Chain-of-Custody

Passport lineage:

Root
 ↓
Agent A
 ↓
Agent B
 ↓
Agent C

ต้องสามารถ reconstruct ได้

ถ้า chain ขาด:

UNKNOWN

ไม่ควรเดาว่า parent คือใคร

⸻

44. Parent Accountability

เมื่อ child agent ทำ action:

Child Agent
    ↓
Action

ต้องสามารถระบุ:

Child Identity
Parent Identity
Delegation
Authority Chain

เพื่อป้องกัน:

Parent:
"I don't know that agent."

หลังจาก agent ลูกไปทำเรื่องเละเทะ

⸻

45. Agent Creation

การสร้าง agent:

Agent Creation Request
      ↓
Identity Creation
      ↓
Artifact Selection
      ↓
Capability Declaration
      ↓
Authority Definition
      ↓
Controller Binding
      ↓
Passport Issuance
      ↓
Verification

Agent ไม่ควรสามารถสร้าง agent ที่มี authority มากกว่าตัวเอง

⸻

46. Agent Spawn Constraint

หาก:

Agent A

สร้าง:

Agent B

ต้อง enforce:

Authority(B)
⊆
Authority(A)

เว้นแต่มี explicit authorization จาก higher authority

นี่เป็น critical invariant สำหรับ recursive agents

⸻

47. Capability Inheritance

Default:

Parent Capability
      ↓
NOT automatically inherited

หากต้องการ inheritance:

Parent
  ↓
Delegation
  ↓
Scoped Capability
  ↓
Child

ตัวอย่าง:

Parent:
filesystem.write
Child:
filesystem.write
scope=/project-x
expires=30m

ไม่ใช่:

Child:
filesystem.write
scope=/
forever

⸻

48. Passport and Trust

Passport ให้ evidence

Trust Engine ประเมิน evidence

Passport
   ↓
Evidence
   ↓
RFC-0041 Trust Engine
   ↓
Trust Assessment

Passport ไม่ควรมี hardcoded trust score ที่กลายเป็นความจริงถาวร

⸻

49. Passport and World Model

Agent Passport เป็น entity representation ใน World Model:

World
 └── Agent
       ├── Identity
       ├── Passport
       ├── Capabilities
       ├── Trust
       ├── Relationships
       └── State

Passport เป็น representation

ไม่ใช่ตัว agent จริง

⸻

50. Passport and Chronicle

ทุก passport lifecycle event:

Issued
Updated
Renewed
Suspended
Revoked
Expired
Retired

ต้องถูกบันทึกใน Chronicle

เพื่อให้สามารถตอบ:

Which passport did Agent A present at 14:32?

⸻

51. Passport and Evolution Ledger

ถ้า agent เปลี่ยน:

Model
Skill
Runtime
Configuration
Architecture
Policy

และการเปลี่ยนนั้นทำให้ passport semantics เปลี่ยน ต้องสร้าง evolution record

เช่น:

Agent A
Passport v3
Model:
X
↓ Evolution
Passport v4
Model:
Y

Evolution Ledger ต้องเชื่อม:

Old Passport
New Passport
Evolution Record
Artifact
Benchmark
Verification

⸻

52. Passport Compatibility

Remote system ต้องสามารถตรวจว่า Passport version รองรับหรือไม่

Passport Version
Protocol Version
Schema Version
Crypto Version

Compatibility result:

SUPPORTED
SUPPORTED_WITH_LIMITATIONS
UNSUPPORTED
UNKNOWN

⸻

53. Passport Expiration

Passport ต้องมี expiration policy

เช่น:

Static identity passport
→ long-lived
Runtime passport
→ short-lived
Delegated worker passport
→ very short-lived

อายุ Passport ควรสัมพันธ์กับ risk

⸻

54. Offline Verification

บาง environment อาจไม่มี network

Passport จึงควรรองรับ offline verification:

Passport
+
Public Key
+
Signature
+
Embedded/Referenced Status Evidence

แต่ offline verification ต้องระบุ:

status freshness

เพราะ verifier อาจไม่รู้ว่า identity ถูก revoke ไปแล้วหลังจาก checkpoint

⸻

55. Freshness

Passport status ต้องมี freshness

ตัวอย่าง:

status_evidence:
  observed_at:
  valid_until:
  source:

ดังนั้น:

Valid Passport
+
Stale Revocation State
=
Insufficient Assurance

สำหรับ high-risk action

⸻

56. Passport Security Threats

PAS-SEC-01 Forgery

ปลอม Passport

Mitigation: cryptographic signature

PAS-SEC-02 Stolen Passport

นำ Passport จริงไปใช้ผิด context

Mitigation: audience/purpose/session binding

PAS-SEC-03 Replay

ใช้ Passport เก่า

Mitigation: expiration + freshness + nonce

PAS-SEC-04 Capability Inflation

ประกาศ capability เกินจริง

Mitigation: capability registry + attestation

PAS-SEC-05 Authority Inflation

Passport อ้าง authority ที่ไม่มีจริง

Mitigation: resolve authorization reference

PAS-SEC-06 Lineage Forgery

สร้าง parent ปลอม

Mitigation: signed lineage

PAS-SEC-07 Controller Forgery

อ้างว่าอยู่ภายใต้ human/organization ที่ไม่ได้ควบคุม

Mitigation: controller attestation

PAS-SEC-08 Artifact Substitution

Passport อ้าง artifact หนึ่งแต่ runtime ใช้อีก artifact

Mitigation: artifact identity + runtime verification

PAS-SEC-09 Stale Status

Passport เคย valid แต่ถูก revoke แล้ว

Mitigation: freshness checking

PAS-SEC-10 Confused Deputy

Agent ถูกใช้เป็นตัวกลางเพื่อทำ action ที่ผู้เรียกไม่มี authority

Mitigation: preserve caller identity + delegation chain

PAS-SEC-11 Passport Escalation

Child agent ได้ authority มากกว่า parent

Mitigation: authority subset invariant

PAS-SEC-12 Privacy Leakage

Passport เปิดเผย internal infrastructure

Mitigation: selective disclosure

⸻

57. Passport APIs

issue_passport()
get_passport()
resolve_passport()
verify_passport()
renew_passport()
update_passport()
suspend_passport()
revoke_passport()
expire_passport()
retire_passport()
present_passport()
verify_presentation()
create_attestation()
verify_attestation()
get_lineage()
verify_lineage()
get_capabilities()
get_authority_refs()
check_freshness()
check_revocation()
create_child_agent_passport()
verify_delegation_chain()
create_disclosure()
verify_disclosure()

⸻

58. Passport Verification API

verify_passport(
    passport,
    context,
    required_assurance
)

ผลลัพธ์:

passport_verification:
  valid:
  identity_verified:
  signature_verified:
  issuer_verified:
  controller_verified:
  lineage_verified:
  artifact_verified:
  authority_verified:
  revocation_status:
  freshness:
  assurance_level:
  warnings:
  evidence_refs:

⸻

59. Passport State Machine

                 ┌─────────┐
                 │  DRAFT  │
                 └────┬────┘
                      ↓
                 ┌─────────┐
                 │ ISSUED  │
                 └────┬────┘
                      ↓
                 ┌─────────┐
                 │ ACTIVE  │
                 └──┬───┬──┘
                    │   │
              update│   │suspend
                    ↓   ↓
                 UPDATED SUSPENDED
                    │      │
                    ↓      ↓
                 ACTIVE  QUARANTINED
                              │
                              ↓
                           REVOKED
ACTIVE
  │
  ├────────→ EXPIRED
  │
  └────────→ RETIRED

⸻

60. Passport Events

PassportCreated
PassportIssued
PassportActivated
PassportPresented
PassportVerified
PassportRejected
PassportUpdated
PassportRenewed
PassportSuspended
PassportQuarantined
PassportRevoked
PassportExpired
PassportRetired
PassportAttestationAdded
PassportAttestationVerified
PassportAttestationRejected
PassportLineageCreated
PassportLineageVerified
PassportLineageConflictDetected
PassportCapabilityDeclared
PassportAuthorityReferenced
PassportDisclosureCreated
PassportDisclosureVerified
PassportFreshnessChecked
PassportRevocationChecked
PassportReplayDetected
PassportForgeryDetected

⸻

61. Passport Invariants

PAS-1

ทุก passport ต้องมี unique passport ID

PAS-2

Passport ต้องอ้างถึง valid identity

PAS-3

Passport ≠ Identity

PAS-4

Passport ≠ Authority

PAS-5

Passport ≠ Trust

PAS-6

Passport ต้องมี version

PAS-7

Passport consequential use ต้องสามารถตรวจ signature ได้

PAS-8

Passport ต้องมี validity period เมื่อเหมาะสม

PAS-9

Passport status ต้องสามารถตรวจสอบ revocation ได้

PAS-10

Passport ต้องมี provenance

PAS-11

Passport ต้องสามารถอ้าง controller ได้เมื่อจำเป็น

PAS-12

Controller claim ต้องมี evidence

PAS-13

Parent-child relationship ต้องตรวจสอบได้

PAS-14

Child agent ห้ามได้รับ authority เกิน parent โดยอัตโนมัติ

PAS-15

Capability declaration ≠ authorization

PAS-16

Authority reference ต้อง resolve ไปยัง authorization source

PAS-17

Trust evidence ≠ trust conclusion

PAS-18

Passport ไม่สามารถ grant authority ด้วยตัวเอง

PAS-19

Passport revocation ต้องไม่ลบ identity history

PAS-20

Identity revocation ต้อง invalidate dependent passport ตาม policy

PAS-21

Passport update ต้องสร้าง historical record

PAS-22

Passport presentation ต้อง preserve actor identity

PAS-23

Delegation chain ต้องสามารถ reconstruct ได้

PAS-24

Broken lineage ต้องแสดงเป็น UNKNOWN

PAS-25

Stale status ต้องลด assurance

PAS-26

High-risk operations ต้องใช้ freshness ตาม policy

PAS-27

Private credentials ห้ามถูกเปิดเผยผ่าน Passport

PAS-28

Selective disclosure ต้องไม่ทำลาย critical verification semantics

PAS-29

Passport ที่ถูก revoke ต้องไม่ถูกนำกลับมา active โดย agent เอง

PAS-30

Passport ไม่สามารถใช้เป็นเหตุผลในการเพิ่ม authority ให้ agent

⸻

62. Reference Architecture

                  HUMAN / CONTROLLER
                          │
                          ▼
                    ROOT IDENTITY
                          │
                          ▼
                    AGENT IDENTITY
                          │
                          ▼
                  ┌───────────────┐
                  │ AGENT PASSPORT│
                  └───────┬───────┘
                          │
        ┌─────────────────┼─────────────────┐
        ▼                 ▼                 ▼
     Identity         Provenance         Attestation
        │                 │                 │
        └─────────────────┼─────────────────┘
                          ▼
                    Trust Engine
                          │
                          ▼
                  Capability Registry
                          │
                          ▼
                    Authorization
                          │
                          ▼
                       Action
                          │
                          ▼
                    Verification
                          │
                          ▼
                      Chronicle

⸻

63. Example

Veda ต้องการสร้าง Research Agent:

User
 ↓
Create Research Agent
 ↓
Identity Created
 ↓
Agent Artifact Verified
 ↓
Controller Bound
 ↓
Capabilities Declared
 ↓
Authority Granted
 ↓
Passport Issued

Passport:

agent_passport:
  agent_identity: veda:agent:research-001
  agent_profile:
    name: Research Agent
    type: RESEARCH
  capabilities:
    - web.read
    - document.read
  authority:
    - lease: web-read-project-x
  security:
    status: NORMAL
  validity:
    expires_at: ...
  lineage:
    parent: veda:agent:orchestrator
  signatures:
    - ...

เมื่อ Research Agent ส่ง request:

Research Agent
      ↓
Passport
      ↓
Remote Service
      ↓
Identity Verification
      ↓
Passport Verification
      ↓
Trust Evaluation
      ↓
Authorization Evaluation
      ↓
Request

Remote service ไม่ควรสรุปว่า:

"Passport valid → allow everything"

แต่ต้อง:

Identity valid
+
Passport valid
+
Trust sufficient
+
Capability relevant
+
Authority valid
+
Policy permits
=
Request may proceed

⸻

64. Relationship to RFC-0041

RFC-0040 ให้:

Identity Evidence
Provenance
Attestation
Capability Declaration
Authority References

RFC-0041 จะตอบ:

Should Veda trust this agent?
How much?
For what purpose?
Under what conditions?

ดังนั้น:

Passport
   ↓
Trust Engine

ไม่ใช่:

Passport
   ↓
Automatic Trust

⸻

65. Relationship to RFC-0044

Multi-Agent World ต้องเก็บ:

Agent
Identity
Passport
Relationship
Authority
Trust
World Scope

ดังนั้น RFC-0044 จะใช้ Passport เป็นหนึ่งใน representation ของ agent แต่ไม่ให้ Passport กลายเป็น global truth

⸻

66. Relationship to RFC-0046

Federation จะใช้ Passport เพื่อ:

Agent A
   ↓
Present Passport
   ↓
Agent B
   ↓
Verify
   ↓
Trust Negotiation
   ↓
Capability / Delegation

Federation จะกำหนด protocol สำหรับ interaction ระหว่าง trust domains

⸻

67. Relationship to External Standards

ระบบ Agent Identity ภายนอกกำลังพัฒนาแนวทางที่ผูก cryptographically verifiable credentials เข้ากับ agent identity, controlling entity, authorization scope, revocation และ cross-organizational trust negotiation

Veda จึงควรออกแบบ Passport ให้สามารถ map/adapt ไปยังมาตรฐานภายนอกได้ในอนาคต แทนการฝัง implementation ของมาตรฐานใดมาตรฐานหนึ่งลงใน core architecture

หลัก:

Veda Passport
      ↓
Adapter
      ↓
External Credential / Identity Protocol

ไม่ใช่:

Veda Core
      ↓
Hard dependency on one external standard

⸻

68. Final Principle

An Agent Passport proves what an agent presents about itself. It does not grant the agent permission to act.

และ architecture ต้องรักษา chain:

Identity
    ↓
Passport
    ↓
Evidence
    ↓
Trust
    ↓
Capability
    ↓
Authorization
    ↓
Action
    ↓
Verification
    ↓
Chronicle

แต่ละชั้นต้องมีหน้าที่ของตัวเอง

ห้ามรวมทุกอย่างเข้าด้วยกันเป็น:

"trusted = true"

เพราะนั่นคือวิธีสร้างระบบที่ดูฉลาดจนกระทั่งวันหนึ่งมันทำสิ่งที่ไม่มีใครอนุญาต แล้วทุกคนก็นั่งไล่ log กันเหมือนนักโบราณคดี

Passport บอกว่า agent เป็นใครและนำหลักฐานอะไรมาแสดง
Trust Engine ตัดสินน้ำหนักของหลักฐาน
Authorization ตัดสินสิทธิ์
External World ตัดสินผลลัพธ์จริง