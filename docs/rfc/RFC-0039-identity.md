RFC-0039 — Veda Identity

Status: Architecture
Layer: 15 — Identity & Trust
Depends On: RFC-0001, RFC-0002, RFC-0003, RFC-0009, RFC-0010, RFC-0028, RFC-0030, RFC-0031, RFC-0032, RFC-0033, RFC-0038
Used By: RFC-0040, RFC-0041, RFC-0044, RFC-0046, RFC-0047, RFC-0048

⸻

1. Abstract

RFC-0039 กำหนดระบบ Identity ของ Veda

Identity คือกลไกที่ทำให้ Veda สามารถระบุได้ว่า:

* ใครหรืออะไรเป็นผู้กระทำ
* instance ใดกำลังทำงาน
* process ใดเป็นผู้ร้องขอ
* tool ใดเป็นผู้ดำเนินการ
* model ใดเป็นผู้ให้ผลลัพธ์
* artifact ใดเป็นต้นทาง
* credential ใดถูกใช้
* assertion ใดสามารถตรวจสอบได้
* การกระทำใดสามารถผูกกลับไปยัง actor ที่แน่นอนได้

Identity เป็นรากฐานของ:

Authentication
Authorization
Audit
Traceability
Trust
Delegation
Federation
Accountability
Recovery
Security

แต่ Identity ไม่ใช่ Authority

หลักสำคัญ:

Identity tells Veda WHO is acting.
Authority tells Veda WHAT that actor may do.
Trust tells Veda HOW MUCH the actor's claims should be relied upon.

ดังนั้น:

Identity ≠ Authority
Identity ≠ Trust
Identity ≠ Capability
Identity ≠ Permission
Identity ≠ Version
Identity ≠ Personality
Identity ≠ Consciousness
Identity ≠ Truth
Identity ≠ Human

⸻

2. Motivation

ระบบที่สามารถทำงานแทนมนุษย์ได้จำเป็นต้องตอบคำถาม:

“ใครเป็นคนทำสิ่งนี้?”

โดยไม่สามารถพึ่งเพียงชื่อที่ระบบตั้งขึ้นเองได้

ตัวอย่างที่ไม่เพียงพอ:

actor = "Veda"

เพราะไม่สามารถพิสูจน์ได้ว่า:

* Veda instance ไหน
* software version ไหน
* process ไหน
* credential ไหน
* machine ไหน
* agent ไหน
* key ไหน
* action ไหน

ดังนั้น Veda ต้องมี identity hierarchy

Veda Root Identity
        │
        ├── System Identity
        │
        ├── Instance Identity
        │       ├── Session Identity
        │       └── Process Identity
        │
        ├── Agent Identity
        │
        ├── Tool Identity
        │
        ├── Model Identity
        │
        └── Artifact Identity

Identity ต้องสามารถตรวจสอบย้อนหลังได้ผ่าน Chronicle และ Evolution Ledger

Identity
   ↓
Authentication
   ↓
Authorization
   ↓
Action
   ↓
Verification
   ↓
Chronicle

⸻

3. Design Goals

RFC นี้มีเป้าหมาย:

1. ระบุ actor ได้อย่างไม่กำกวม
2. ตรวจสอบ identity ด้วย cryptographic evidence
3. รองรับ identity lifecycle
4. รองรับ key rotation
5. รองรับ identity revocation
6. รองรับ system/instance/session/process identities
7. รองรับ multi-agent identity
8. รองรับ external identity mapping
9. ป้องกัน identity spoofing
10. ป้องกัน identity confusion
11. แยก identity ออกจาก authority
12. รองรับ audit และ forensic reconstruction
13. รองรับ federation
14. รองรับ agent cloning/forking อย่างปลอดภัย
15. รองรับ recovery หลัง key compromise
16. ทำให้ consequential action สามารถ trace กลับไปยัง actor ได้

⸻

4. Non-Goals

RFC นี้ไม่กำหนด:

* AI consciousness
* personality
* intelligence
* reasoning
* authorization policy
* trust scoring โดยละเอียด
* capability permissions
* human identity proofing
* model quality
* agent behavior policy
* federation protocol โดยละเอียด

สิ่งเหล่านี้อยู่ใน RFC อื่น

⸻

5. Core Principle

Identity ต้องตอบ:

WHO?

ไม่ใช่:

WHAT MAY THEY DO?

ดังนั้น flow ที่ถูกต้องคือ:

Identity
    ↓
Authentication
    ↓
Capability
    ↓
Authorization
    ↓
Action

ไม่ใช่:

Identity
    ↓
"ฉันคือ Veda"
    ↓
"ดังนั้นฉันมีสิทธิ์"

Veda ห้ามใช้ identity ของตัวเองเป็นเหตุผลในการเพิ่ม authority ให้ตัวเอง

⸻

6. Identity Object

identity:
  identity_id:
  identity_type:
  namespace:
  display_name:
  public_keys:
    - key_id:
      algorithm:
      public_key:
      fingerprint:
      valid_from:
      valid_until:
      status:
  issuer:
  controller_refs:
  parent_identity_refs:
  status:
  created_at:
  activated_at:
  suspended_at:
  revoked_at:
  retired_at:
  attestation_refs:
  trust_refs:
  instance_refs:
  lineage_refs:
  external_identity_refs:
  provenance:
  integrity:
  metadata:

⸻

7. Identity Types

Veda ต้องรองรับ identity หลายระดับ

7.1 Root Identity

เป็น identity ระดับสูงสุดของ Veda installation/system

ตัวอย่าง:

veda:root:<id>

Root identity ต้องถูกสร้างภายใต้ human-controlled initialization process

Root identity ไม่สามารถ:

* เปลี่ยน Constitution เอง
* grant authority ให้ตัวเอง
* bypass authorization
* ปลด human controller
* แก้ Chronicle ย้อนหลัง

⸻

8. System Identity

ระบุ software system ทั้งชุด

veda:system:<id>

System identity มีอายุยาวกว่า runtime instance

ตัวอย่าง:

Veda System
Version 0.1

System identity ไม่ควรเปลี่ยนเพียงเพราะ software version เปลี่ยน

ดังนั้น:

Identity ≠ Version

⸻

9. Instance Identity

ระบุ runtime instance

veda:instance:<id>

ตัวอย่าง:

Veda System
    ├── Instance A
    ├── Instance B
    └── Instance C

แต่ละ instance ต้องมี identity แยกกัน

เพื่อให้สามารถตรวจสอบได้ว่า:

Instance A performed Action X

แทนที่จะบันทึกเพียง:

Veda performed Action X

⸻

10. Session Identity

Session identity ใช้ระบุ execution/session context

veda:session:<id>

เช่น:

User Request
    ↓
Session
    ↓
Task
    ↓
Process
    ↓
Action

Session identity มีอายุสั้นกว่า instance identity

⸻

11. Process Identity

Process identity ใช้ระบุ runtime process หรือ worker

veda:process:<id>

ตัวอย่าง:

Brain Process
Planner Process
Browser Worker
Research Worker
Verification Worker

ช่วยให้ audit สามารถตอบได้ว่า:

process ไหนเป็นผู้สร้าง action proposal?

⸻

12. Agent Identity

Agent identity ใช้สำหรับ agent ที่เป็น autonomous cognitive actor

veda:agent:<id>

Agent identity ต้องสามารถเชื่อมโยงกับ:

* parent system
* instance
* capability scope
* agent passport
* trust state
* world scope

รายละเอียด agent identity จะขยายใน RFC-0040

⸻

13. Tool Identity

Tool แต่ละตัวต้องมี identity

veda:tool:<id>

ตัวอย่าง:

terminal
browser
filesystem
github
database
docker

Tool identity ต้องสามารถตรวจสอบ:

tool
version
provider
artifact
configuration
capabilities
trust

⸻

14. Model Identity

Model provider/model ต้องมี identity เช่นกัน

veda:model:<provider>:<model>:<version>

ตัวอย่าง:

veda:model:provider-x:model-y:v3

เพื่อให้สามารถตอบย้อนหลังได้ว่า:

Which model generated this proposal?

และ:

Which model generated this decision input?

Model identity ไม่ได้หมายความว่า model เป็น authority

⸻

15. Artifact Identity

Artifact ที่มีผลต่อ behavior ต้องมี identity

เช่น:

* executable
* package
* skill
* model
* configuration
* policy
* knowledge package
* memory package

รูปแบบ:

veda:artifact:<content_hash>

Content-addressed identity ควรใช้เมื่อเหมาะสม เพื่อให้ artifact identity ผูกกับเนื้อหาที่ตรวจสอบได้

⸻

16. Cryptographic Identity

Identity ที่มีผลต่อ security ต้องมี cryptographic binding

ขั้นต่ำ:

Identity
    ↓
Public Key
    ↓
Fingerprint

และ private key ต้องไม่ถูกนำเข้า cognitive context

Brain
  X
  ↓
Private Key
Execution Boundary
  ↓
Credential Store / Secure Key Store

AI model ไม่ควรเห็น private credentials โดยไม่จำเป็น

⸻

17. Key Model

Identity สามารถมีหลาย key:

Identity
 ├── Authentication Key
 ├── Signing Key
 ├── Encryption Key
 └── Recovery Key

แต่ละ key ต้องมี:

* key_id
* algorithm
* public key
* fingerprint
* creation time
* activation time
* expiration
* status
* purpose
* provenance

⸻

18. Key Rotation

Key ไม่ควรมีอายุถาวร

เมื่อ rotate:

Old Key
   ↓
Rotation Event
   ↓
New Key

ห้ามลบ historical association

ดังนั้น Chronicle ต้องสามารถตอบ:

Which key signed this event at that time?

ได้

⸻

19. Identity Lifecycle

Identity lifecycle:

CREATED
   ↓
PROVISIONED
   ↓
ACTIVE
   ↓
ROTATING
   ↓
ACTIVE

Failure states:

SUSPENDED
QUARANTINED
REVOKED
RETIRED

State meaning

CREATED

Identity ถูกสร้างขึ้นแต่ยังใช้งานไม่ได้

PROVISIONED

Identity มี cryptographic material และ metadata พร้อม

ACTIVE

สามารถ authenticate ได้

ROTATING

กำลังเปลี่ยน key หรือ identity material

SUSPENDED

หยุดชั่วคราว

QUARANTINED

สงสัย compromise หรือ anomalous behavior

REVOKED

ไม่สามารถกลับมาใช้งานได้

RETIRED

หมดอายุการใช้งานโดยไม่มี security compromise

⸻

20. Identity Authentication

Authentication ต้องตอบ:

Does this actor control the identity it claims?

ตัวอย่าง:

Actor
  ↓
Identity Claim
  ↓
Challenge
  ↓
Cryptographic Proof
  ↓
Authentication Result

Authentication result:

AUTHENTICATED
FAILED
EXPIRED
REVOKED
UNKNOWN

Authentication ≠ Authorization

⸻

21. Identity Assertion

Veda สามารถสร้าง assertion เช่น:

assertion:
  assertion_id:
  subject_identity:
  issuer_identity:
  claims:
    identity:
    role:
    instance:
    artifact:
    capability_reference:
  issued_at:
  expires_at:
  evidence_refs:
  signature:
  status:

Assertion ต้องมี expiration เมื่อเหมาะสม

ไม่ควรมี assertion ที่:

valid forever

โดยไม่มีเหตุผล

⸻

22. Attestation

Attestation ใช้ยืนยันข้อมูลเกี่ยวกับ identity หรือ runtime state

ตัวอย่าง:

"This process belongs to Veda Instance X."
"This artifact corresponds to hash Y."
"This key is controlled by Identity Z."

Attestation ต้องระบุ:

who asserts
what is asserted
when
under what evidence
for how long

⸻

23. Identity vs Trust

Identity บอก:

Who?

Trust บอก:

How much should this identity's claims be relied upon?

ตัวอย่าง:

Identity:
Agent-A
Trust:
Medium
Authority:
Low
Capability:
Filesystem.Read

จึงเป็นไปได้ว่า:

Known identity
+
Low trust
+
No authority

นี่เป็น state ที่ถูกต้อง

⸻

24. Identity vs Authority

Identity ไม่สามารถ grant authority ให้ตัวเอง

ตัวอย่างต้องห้าม:

I am Veda.
Therefore I am administrator.

flow ที่ถูกต้อง:

Identity
    ↓
Authentication
    ↓
Capability Request
    ↓
Policy Evaluation
    ↓
Authorization
    ↓
Capability Lease
    ↓
Action

RFC-0010 และ RFC-0011 เป็น authority boundary

⸻

25. Human Controller

Veda ต้องมีความสัมพันธ์กับ human controller

controller:
  identity_ref:
  relationship:
  authority_scope:
  verification:
  status:

ตัวอย่าง:

Human Controller
      ↓
Veda Root Identity
      ↓
Veda System
      ↓
Veda Instances

Human controller ไม่ควรถูกแทนด้วยข้อความธรรมดาใน configuration

ต้องมี identity binding ที่ตรวจสอบได้

⸻

26. Identity Delegation

Identity delegation ต้องแยกจาก capability delegation

Identity Delegation
        ≠
Capability Delegation

การบอกว่า:

Agent B acts on behalf of Agent A

ไม่ได้แปลว่า:

Agent B inherits all capabilities of Agent A

Delegation ต้องระบุ:

* delegator
* delegatee
* scope
* purpose
* capabilities
* expiration
* constraints
* revocation
* audit references

⸻

27. External Identity Mapping

Veda จะมี external identities เช่น:

GitHub account
Cloud account
Database user
Device identity
API credential
Operating system user

แต่:

External Identity ≠ Veda Identity

ตัวอย่าง:

Veda Identity
      │
      └── maps to
             │
             └── GitHub Account

Mapping ต้องมี:

* external provider
* external identity
* verification evidence
* scope
* validity
* expiration
* mapping status

⸻

28. Identity Collision

ระบบต้องป้องกัน identity collision

Identity ID ต้อง:

* unique
* canonical
* collision-resistant
* stable
* version-independent

Display name ไม่ใช่ identity

ดังนั้น:

name = "Veda"

ไม่เพียงพอ

แต่:

identity_id = cryptographically unique identifier

ใช้เป็น primary identity reference

⸻

29. Identity Cloning

การ clone Veda ต้องไม่ทำให้เกิด:

Two machines
      ↓
Same cryptographic identity

เพราะจะทำลาย accountability

ดังนั้น:

Original Veda
     │
     ├── Clone A
     │      ↓
     │   New Identity
     │
     └── Clone B
            ↓
         New Identity

แต่ lineage ต้องถูกเก็บ:

Parent Identity
      ↓
Derived Identity

⸻

30. Forking

ถ้า Veda ถูก fork:

Veda A
  ↓
Fork
  ↓
Veda B

Veda B ต้องมี identity ใหม่

แต่ต้องเก็บ:

fork_parent
fork_time
source_version
source_artifact
authorization
lineage

ไม่ควรใช้ identity เดิมเพื่อแสร้งว่าเป็น instance เดิม

⸻

31. Identity Recovery

หาก private key สูญหาย:

Key Lost
   ↓
Identity Recovery
   ↓
Human / Recovery Authority
   ↓
New Key
   ↓
Identity Rebinding

หาก key ถูก compromise:

Compromise Detected
       ↓
Quarantine
       ↓
Revoke Key
       ↓
Assess Blast Radius
       ↓
Recover Identity
       ↓
Issue New Key
       ↓
Verify

ทุกขั้นตอนต้องถูกบันทึกใน Chronicle

⸻

32. Identity Quarantine

หากพบ:

* impossible behavior
* duplicate key use
* unexpected location/device
* unauthorized capability use
* signature anomaly
* identity cloning
* credential leakage

Identity สามารถเข้าสู่:

QUARANTINED

ในสถานะนี้:

Normal Actions → BLOCKED
Read-only Diagnostics → MAY BE ALLOWED
Recovery → RESTRICTED
Human Review → REQUIRED

⸻

33. Identity Revocation

Revocation ต้องมี:

identity_id
reason
issuer
effective_time
evidence
scope
replacement_identity

ต้องแยก:

Key Revocation

จาก:

Identity Revocation

เพราะการ compromise ของ key ไม่จำเป็นต้องหมายความว่า identity ทั้งหมดต้องถูกทำลาย

⸻

34. Identity History

Identity ต้องสามารถ reconstruct historical state ได้

ตัวอย่าง:

At T1:
Key A active
At T2:
Key B active
At T3:
Key A revoked
At T4:
Identity suspended

Chronicle ต้องสามารถตอบ:

Which identity was active at T2?
Which key was valid at T2?
Who controlled it?
What assertions existed?

⸻

35. Identity and Chronicle

ทุก consequential identity event ต้องถูกส่งเข้า:

RFC-0031 Event/Audit/Trace Fabric

และเก็บใน:

RFC-0032 Veda Chronicle

ตัวอย่าง:

IdentityCreated
KeyGenerated
IdentityProvisioned
IdentityActivated
KeyRotated
IdentityAttested
AssertionIssued
IdentitySuspended
IdentityQuarantined
IdentityRevoked
IdentityRecovered
IdentityRetired
ExternalIdentityMapped

⸻

36. Identity and Evolution Ledger

หาก evolution เปลี่ยน:

Identity implementation
Key management
Authentication mechanism
Identity schema
Identity policy

ต้องเชื่อมกับ RFC-0038 Evolution Ledger

เพื่อให้รู้ว่า:

Which evolution changed identity behavior?

⸻

37. Identity and Self Model

RFC-0033 Self Model สามารถอ้างถึง identity:

Self Model
    ↓
Identity
    ↓
Current Instance
    ↓
Current Process

แต่ Self Model ห้ามเป็น authority สำหรับ identity

เช่น:

Self Model says:
"I am administrator."

ไม่ถือเป็นหลักฐาน

ต้องมี external/cryptographic evidence

⸻

38. Identity and Agent Passport

RFC-0040 จะสร้าง:

Agent Passport

ซึ่งใช้ identity เป็น root reference

Agent Passport
      │
      ├── Identity
      ├── Capabilities
      ├── Trust
      ├── Provenance
      ├── Lineage
      └── Attestations

Passport ไม่แทน Identity

⸻

39. Identity and Federation

RFC-0046 จะใช้ identity เพื่อ:

Agent A
    ↕
Federation
    ↕
Agent B

Federation ต้องไม่หมายความว่า:

Trust = Automatic
Authority = Automatic

การรู้จัก identity ของอีก agent ไม่ได้แปลว่าเชื่อหรือให้อำนาจมัน

⸻

40. Identity Security Threats

ID-SEC-01 Identity Spoofing

ผู้โจมตีปลอมตัวเป็น Veda

Mitigation:

Cryptographic Authentication

ID-SEC-02 Key Theft

private key ถูกขโมย

Mitigation:

Secure Key Storage
Rotation
Revocation
Quarantine

ID-SEC-03 Replay

นำ assertion เก่ากลับมาใช้

Mitigation:

Nonce
Timestamp
Expiration
Replay Detection

ID-SEC-04 Identity Cloning

identity เดียวถูกใช้หลาย runtime

Mitigation:

Instance Identity
Key Binding
Device Binding
Anomaly Detection

ID-SEC-05 Identity Confusion

ระบบสับสนระหว่าง:

System
Instance
Agent
Process
Tool
Model
User

Mitigation:

Typed Identity

ID-SEC-06 Privilege Confusion

identity ถูกตีความเป็น authority

Mitigation:

Strict separation of Identity and Authorization

ID-SEC-07 Delegation Abuse

delegate ได้ authority มากเกิน scope

Mitigation:

Scoped Delegation
Expiration
Capability Lease

ID-SEC-08 Stale Credential

credential หมดอายุแต่ยังถูกใช้

Mitigation:

Validity Checking
Expiration
Revocation

ID-SEC-09 Trust Laundering

identity ที่ไม่น่าเชื่อถือได้รับ trust ผ่าน intermediary

Mitigation:

Trust provenance
Chain verification
Independent evaluation

ID-SEC-10 Cross-Agent Impersonation

agent หนึ่งแอบอ้างเป็นอีก agent

Mitigation:

Cryptographic identity binding

ID-SEC-11 Supply Chain Identity Attack

artifact ปลอมใช้ identity ของ package จริง

Mitigation:

Artifact hash
Signature
Provenance
Evolution Ledger

ID-SEC-12 Recovery Abuse

ผู้โจมตีใช้ recovery mechanism เพื่อยึด identity

Mitigation:

Human-controlled recovery
Multi-step verification
Audit

⸻

41. Identity APIs

Core API:

create_identity()
get_identity()
resolve_identity()
verify_identity()
authenticate_identity()
create_key()
rotate_key()
revoke_key()
create_assertion()
verify_assertion()
revoke_assertion()
create_attestation()
verify_attestation()
derive_instance_identity()
derive_session_identity()
derive_process_identity()
map_external_identity()
verify_external_mapping()
suspend_identity()
quarantine_identity()
revoke_identity()
retire_identity()
recover_identity()
get_identity_history()
get_identity_lineage()
detect_identity_anomaly()

⸻

42. Identity Verification API

verify_identity(
    identity_id,
    challenge,
    evidence,
    context
)

ผลลัพธ์:

identity_verification:
  identity_id:
  authenticated:
  assurance_level:
  key_ref:
  evidence_refs:
  valid_from:
  valid_until:
  replay_status:
  revocation_status:
  verifier:
  confidence:
  timestamp:

⸻

43. Identity Resolution

ระบบต้อง resolve identity ได้จาก:

identity_id
public_key
fingerprint
assertion
signature
external_identity
session
process
artifact

แต่ resolution ต้องไม่สร้าง identity ใหม่โดยอัตโนมัติเพียงเพราะไม่รู้จักข้อมูล

Unknown ต้องเป็น:

UNKNOWN_IDENTITY

ไม่ใช่:

CREATE_NEW_IDENTITY

⸻

44. Identity State Machine

             ┌──────────────┐
             │   CREATED    │
             └──────┬───────┘
                    ↓
             ┌──────────────┐
             │ PROVISIONED  │
             └──────┬───────┘
                    ↓
             ┌──────────────┐
             │    ACTIVE    │
             └──┬─────┬─────┘
                │     │
          rotate│     │suspend
                ↓     ↓
          ROTATING  SUSPENDED
                │      │
                ↓      ↓
             ACTIVE  QUARANTINED
                         │
                         ↓
                      REVOKED
ACTIVE
   │
   └──────────────→ RETIRED

⸻

45. Identity Event Model

Identity events:

IdentityCreated
IdentityProvisioned
IdentityActivated
IdentitySuspended
IdentityQuarantined
IdentityRevoked
IdentityRetired
IdentityRecovered
KeyGenerated
KeyActivated
KeyRotated
KeyExpired
KeyRevoked
IdentityAuthenticated
IdentityAuthenticationFailed
AssertionCreated
AssertionVerified
AssertionRejected
AssertionExpired
AssertionRevoked
IdentityAttested
AttestationVerified
AttestationRejected
ExternalIdentityMapped
ExternalIdentityUnmapped
IdentityAnomalyDetected
IdentityCollisionDetected
IdentityCloneDetected
IdentityRecoveryStarted
IdentityRecoveryCompleted

⸻

46. Identity Trace

ทุก consequential action ต้องสามารถ trace:

Action
 ↓
Process Identity
 ↓
Session Identity
 ↓
Instance Identity
 ↓
System Identity
 ↓
Root Identity
 ↓
Controller

ตัวอย่าง:

GitHub Commit
      ↓
GitHub Tool
      ↓
Coding Agent
      ↓
Veda Process
      ↓
Veda Session
      ↓
Veda Instance
      ↓
Veda System
      ↓
Veda Root
      ↓
Human Controller

นี่คือ accountability chain

⸻

47. Identity and Human Approval

เมื่อ action ต้องการ human approval:

Veda Identity
      ↓
Action Proposal
      ↓
Human Approval
      ↓
Approval Identity
      ↓
Capability Lease
      ↓
Action

Approval ต้องผูกกับ identity ของผู้อนุมัติ

ไม่ใช่:

approved = true

อย่างเดียว

⸻

48. Identity and Capability

Capability token ต้อง reference identity:

capability_lease:
  lease_id:
  subject_identity:
  capability:
  scope:
  issued_by:
  issued_at:
  expires_at:

ดังนั้น capability ไม่ควรเป็น:

free-floating permission

แต่เป็น:

permission bound to identity + scope + time

⸻

49. Identity and Verification

Verification ต้องสามารถระบุ:

Who performed the action?

และ:

Which identity signed/authorized/dispatched it?

ดังนั้น verification chain:

Identity
   ↓
Authorization
   ↓
Action
   ↓
Execution
   ↓
Observation
   ↓
Evidence
   ↓
Verification

⸻

50. Identity and Failure

ถ้า identity ไม่สามารถตรวจสอบได้:

IDENTITY_UNKNOWN

ไม่ควรเปลี่ยนเป็น:

ASSUME_VEDA

โดยเฉพาะ consequential action

Default:

Unknown Identity
    ↓
No Authorization
    ↓
No High-Risk Action

⸻

51. Identity Integrity

Identity record ต้องมี integrity metadata

ตัวอย่าง:

integrity:
  content_hash:
  previous_record_hash:
  signature:
  signing_key:
  verification_status:

Critical identity events ควรสามารถตรวจสอบ cryptographically ได้

⸻

52. Identity Privacy

Identity system ต้องไม่เปิดเผยข้อมูลเกินความจำเป็น

ควรใช้:

minimum disclosure
scoped assertions
short-lived credentials
pseudonymous identifiers

เมื่อไม่จำเป็นต้องเปิดเผย root identity

ห้ามส่ง:

private keys
raw secrets
API tokens
recovery secrets

เข้า model context

⸻

53. Identity Assurance

Identity verification สามารถมีระดับ:

L0 UNKNOWN
L1 SELF_ASSERTED
L2 SYSTEM_VERIFIED
L3 CRYPTOGRAPHICALLY_VERIFIED
L4 INDEPENDENTLY_ATTESTED
L5 HIGH_ASSURANCE

ระดับสูงไม่ได้หมายความว่า actor มี authority มากขึ้น

เช่น:

L5 Identity
+
No Capability
=
Cannot Act

⸻

54. Identity Metrics

ระบบควรวัด:

Authentication success rate
Authentication failure rate
Identity anomaly rate
Key rotation age
Revocation latency
Recovery time
Identity collision count
Clone detection rate
Assertion validation failure rate
Credential replay attempts
Unauthorized identity attempts
Identity verification latency

Metrics ไม่ควรใช้เป็น authority โดยตรง

⸻

55. Identity Governance

การเปลี่ยนแปลง:

Identity schema
Root identity
Controller relationship
Key policy
Recovery mechanism
Authentication policy
Identity trust boundary

ต้องผ่าน governance

การเปลี่ยนแปลง consequential ต้องมี:

Proposal
Evidence
Impact Analysis
Authorization
Deployment
Verification
Chronicle
Evolution Ledger

⸻

56. Identity Invariants

ID-1

ทุก identity ต้องมี unique identity ID

ID-2

Identity ID ต้อง stable ตลอด lifecycle

ID-3

Display name ไม่ใช่ identity

ID-4

Identity ≠ Authority

ID-5

Identity ≠ Capability

ID-6

Identity ≠ Trust

ID-7

Identity ≠ Version

ID-8

Identity ที่มี security significance ต้องมี verifiable binding

ID-9

Private key ห้ามอยู่ใน cognitive context โดยไม่จำเป็น

ID-10

Identity ต้องมี lifecycle state

ID-11

Identity state transitions ต้องถูกบันทึก

ID-12

Key rotation ต้องไม่ลบ historical association

ID-13

Key revocation ต้องสามารถตรวจสอบย้อนหลังได้

ID-14

Identity revocation ต้องไม่ลบ history

ID-15

Unknown identity ต้องไม่ถูกตีความเป็น known identity

ID-16

Authentication ต้องไม่เท่ากับ authorization

ID-17

Authorization ต้องอ้างถึง identity ที่ตรวจสอบได้

ID-18

Delegation ต้องมี scope

ID-19

Delegation ต้องมี expiration เมื่อเหมาะสม

ID-20

Clone ต้องไม่ reuse identity เดิมโดยไม่มี explicit controlled design

ID-21

Fork ต้องสร้าง identity ใหม่

ID-22

Fork ต้องรักษา lineage

ID-23

External identity ต้องไม่ถูกถือว่าเป็น Veda identity โดยอัตโนมัติ

ID-24

Identity assertion ต้องมี provenance

ID-25

Critical assertions ต้องตรวจสอบ integrity ได้

ID-26

Identity anomalies ต้องสามารถเข้าสู่ quarantine ได้

ID-27

Recovery ต้องถูก audit

ID-28

Identity-related consequential actions ต้อง trace กลับถึง actor ได้

ID-29

Identity history ต้อง reconstruct ได้เมื่อข้อมูลเพียงพอ

ID-30

Identity system ห้ามเพิ่ม authority ให้ตัวเอง

⸻

57. Reference Architecture

                    HUMAN CONTROLLER
                           │
                           ▼
                    ROOT IDENTITY
                           │
                           ▼
                    SYSTEM IDENTITY
                           │
              ┌────────────┴────────────┐
              ▼                         ▼
       INSTANCE IDENTITY          AGENT IDENTITY
              │                         │
              ▼                         ▼
        SESSION ID                AGENT PASSPORT
              │
              ▼
        PROCESS IDENTITY
              │
       ┌──────┴──────┐
       ▼             ▼
   TOOL ID       MODEL ID
       │             │
       └──────┬──────┘
              ▼
          CAPABILITY
              │
              ▼
       AUTHORIZATION
              │
              ▼
            ACTION
              │
              ▼
         VERIFICATION
              │
              ▼
          CHRONICLE
              │
              ▼
       EVOLUTION LEDGER

⸻

58. Relationship to Previous RFCs

RFC-0001 Constitution
       ↓
RFC-0009 Capability
       ↓
RFC-0010 Authorization
       ↓
RFC-0011 Capability Lease
       ↓
RFC-0028 Tool Registry
       ↓
RFC-0039 Identity
       ↓
RFC-0040 Agent Passport
       ↓
RFC-0041 Trust Engine

Identity therefore sits between:

"What exists?"

and:

"Who may act?"

⸻

59. Example

User asks:

"Commit the current code to GitHub."

Veda must not produce:

Git commit

immediately.

Identity-aware flow:

User Identity
      ↓
Intent
      ↓
Goal
      ↓
Planner
      ↓
Decision
      ↓
Veda Instance Identity
      ↓
Coding Agent Identity
      ↓
GitHub Tool Identity
      ↓
Capability Check
      ↓
Authorization
      ↓
Capability Lease
      ↓
GitHub Action
      ↓
External World
      ↓
Evidence
      ↓
Verification
      ↓
Chronicle

Audit สามารถตอบได้:

Who requested it?
Which Veda instance planned it?
Which agent proposed it?
Which model generated the proposal?
Which identity authorized it?
Which tool executed it?
Which credential was used?
What actually happened?
Was the result verified?

นี่คือเหตุผลว่าทำไม Identity ไม่ใช่แค่ user_id ใน database แบบที่ software จำนวนมากชอบทำแล้วค่อยพบปัญหาทีหลัง

⸻

60. Design Principle

ระบบ Identity ของ Veda ต้องรักษาหลัก:

Know who is acting.
Prove who is acting.
Record who acted.
Do not confuse identity with authority.
Do not confuse authentication with trust.
Do not confuse trust with permission.
Do not confuse permission with outcome.

และ:

Identity
    → establishes actor
Authority
    → establishes permission
Action
    → produces attempt
External World
    → determines effect
Verification
    → establishes what actually happened
Chronicle
    → preserves the history

⸻

61. Final Principle

Identity tells Veda who is acting. It does not tell Veda what it is allowed to do.

Veda ต้องรู้ว่า:

Who am I?
Who is the user?
Which agent is acting?
Which process is acting?
Which tool is acting?
Which model produced this?
Which credential was used?
Which identity authorized this?

แต่คำตอบของคำถามเหล่านี้ต้องไม่ถูกนำไปใช้เป็นข้ออ้างว่า:

"I exist, therefore I have authority."

Identity คือรากของ accountability ไม่ใช่บัตรผ่านสำหรับอำนาจทั้งหมด