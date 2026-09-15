RFC-0038 — Evolution Ledger

Status: Draft
Layer: 14 — Learning & Evolution
Depends On: RFC-0001, RFC-0031, RFC-0032, RFC-0033, RFC-0034, RFC-0035, RFC-0036, RFC-0037
Feeds Into: RFC-0039 Veda Identity, RFC-0040 Agent Passport, RFC-0041 Trust Engine, RFC-0047 Neural Package Format

Primary Principle:

Every consequential change to Veda must have a permanent, attributable, reconstructable history.

⸻

1. Abstract

RFC-0038 กำหนด Evolution Ledger

Evolution Ledger คือระบบบัญชีประวัติของการวิวัฒน์ของ Veda

มันตอบคำถาม:

What changed?
Why did it change?
Who proposed it?
What evidence caused the proposal?
Who approved it?
Which version changed?
What tests were run?
What was expected?
What actually happened?
Was the change verified?
Did it regress?
Was it rolled back?
What descendants depend on it?

Evolution Ledger ไม่ใช่:

Application Log
Memory
Knowledge
Chronicle
Git History
Model Registry

แต่เป็น specialized provenance and lineage layer for system evolution

⸻

2. Motivation

เมื่อ Veda มี evolution หลายร้อยหรือหลายพันครั้ง ปัญหาจะไม่ใช่แค่:

“Veda เก่งขึ้นหรือเปล่า?”

แต่คือ:

“ทำไม Veda ถึงเป็นแบบนี้?”

ตัวอย่าง:

Veda V1
 ↓
Learning L1
 ↓
Evolution E1
 ↓
V2
 ↓
Learning L2
 ↓
Evolution E2
 ↓
V3
 ↓
Evolution E3
 ↓
V4

เมื่อ V4 มี regression:

Which change caused it?

ต้องตอบได้

ไม่ใช่:

"น่าจะเป็น model"

มนุษย์มีความสามารถพิเศษในการลืมว่าตัวเองแก้อะไรไว้เมื่อวาน แล้วเรียกมันว่า “legacy system” ดังนั้น Veda ต้องไม่ทำตามประเพณีนี้

⸻

3. Core Distinctions

Ledger ≠ Log
Ledger ≠ Chronicle
Ledger ≠ Git
Ledger ≠ Memory
Ledger ≠ Knowledge
Ledger ≠ Model Registry
Ledger ≠ Audit Trail

แต่ Evolution Ledger เชื่อมกับทั้งหมด

Chronicle
   ↓
Evolution Ledger
   ↓
Evolution Lineage
   ↓
System State

⸻

4. Relationship to Chronicle

RFC-0032 Chronicle เก็บ historical events ของ Veda โดยรวม

RFC-0038 จัดกลุ่มเหตุการณ์เหล่านั้นเป็น evolution lineage

ตัวอย่าง:

Chronicle:
LearningProposalCreated
BenchmarkStarted
BenchmarkCompleted
ApprovalGranted
DeploymentStarted
DeploymentCompleted
VerificationCompleted
RollbackStarted

Evolution Ledger:

Evolution E-0042
├── Proposal
├── Evidence
├── Evaluation
├── Approval
├── Deployment
├── Outcome
└── Rollback

ดังนั้น:

Chronicle = historical substrate
Evolution Ledger = evolution-specific interpretation

⸻

5. Evolution Record

EvolutionRecord {
    evolution_id
    version
    parent_evolution_refs[]
    ancestor_evolution_refs[]
    source_learning_refs[]
    source_reflection_refs[]
    source_experience_refs[]
    target_type
    target_id
    source_version
    candidate_version
    deployed_version
    change_summary
    change_manifest
    objective
    expected_benefits[]
    assumptions[]
    dependencies[]
    risk_profile
    blast_radius
    reversibility
    evidence_refs[]
    verification_refs[]
    benchmark_refs[]
    simulation_refs[]
    approval_refs[]
    deployment_refs[]
    outcome_refs[]
    rollback_refs[]
    status
    created_at
    updated_at
}

⸻

6. Evolution Identity

ทุก evolution ต้องมี unique identity

evolution_id

ตัวอย่าง:

EVO-2026-000001
EVO-2026-000002
EVO-2026-000003

ID ต้องไม่เปลี่ยนแม้ evolution จะถูก:

revised
deployed
rolled_back
superseded

⸻

7. Version Identity

แยก:

Evolution ID

ออกจาก:

System Version

ตัวอย่าง:

Evolution:
EVO-0042
System:
V17 → V18

หนึ่ง evolution อาจสร้างหลาย artifacts

⸻

8. Change Manifest

ทุก evolution ต้องมี machine-readable change manifest

ChangeManifest {
    target
    files[]
    modules[]
    components[]
    configuration_changes[]
    model_changes[]
    memory_changes[]
    knowledge_changes[]
    tool_changes[]
    policy_candidates[]
    architecture_changes[]
}

เป้าหมายคือ:

"เปลี่ยนอะไร"

ต้องตอบแบบ deterministic เท่าที่ทำได้

⸻

9. Source Provenance

ทุก evolution ต้องระบุที่มา

Evolution
    ↓
Learning Proposal
    ↓
Reflection
    ↓
Experience
    ↓
Evidence

ตัวอย่าง:

EVO-0042
  source:
    LEARN-0192
      source:
        REF-0871
          source:
            EXP-1248

ห้ามมี:

Evolution
    ↓
unknown

สำหรับ consequential change

⸻

10. Evidence Bundle

Evolution ต้องมี Evidence Bundle

EvidenceBundle {
    evidence_refs[]
    verification_refs[]
    benchmark_refs[]
    simulation_refs[]
    evidence_strength
    independence
    freshness
    completeness
    contradictions[]
}

⸻

11. Evidence Strength

ตัวอย่างระดับ:

E0 — No Evidence
E1 — Self Report
E2 — Observed
E3 — Verified
E4 — Independently Verified
E5 — Cryptographically Verified

ระดับที่ต้องใช้ขึ้นกับ risk

⸻

12. Approval Record

ApprovalRecord {
    approval_id
    evolution_id
    evolution_version
    approver_identity
    authority_scope
    decision
    approved_scope
    approved_targets
    conditions[]
    issued_at
    expires_at
    signature
}

Approval ต้องผูกกับ version

เช่น:

Approve EVO-0042 V3

ไม่ได้หมายความว่า:

Approve EVO-0042 forever

⸻

13. Deployment Record

DeploymentRecord {
    deployment_id
    evolution_id
    source_version
    target_version
    environment
    deployment_mode
    start_time
    end_time
    operator
    executor
    status
    verification_ref
    rollback_ref
}

Modes:

SHADOW
CANARY
LIMITED
FULL
ROLLBACK
RECOVERY

⸻

14. Outcome Record

OutcomeRecord {
    outcome_id
    evolution_id
    expected_outcomes[]
    observed_outcomes[]
    metrics_before
    metrics_after
    regressions[]
    incidents[]
    user_feedback[]
    verification_refs[]
    status
    measured_at
}

⸻

15. Expected vs Actual

Ledger ต้องไม่เขียน:

Evolution successful.

โดยไม่มีหลักฐาน

ต้องแยก:

Expected:
quality +10%
Actual:
quality +4%
Cost:
+15%
Latency:
-3%
Regression:
1

จากนั้นจึงประเมิน outcome

⸻

16. Evolution Status

PROPOSED
EVALUATING
VALIDATED
APPROVAL_REQUIRED
APPROVED
DEPLOYING
ACTIVE
MONITORING
VERIFIED
ROLLED_BACK
FAILED
QUARANTINED
SUPERSEDED
FROZEN
EXPIRED
INVALIDATED

⸻

17. Lineage Graph

Evolution Ledger ต้องสร้าง graph:

V1
 │
 ├── E1
 │    ↓
 │   V2
 │
 ├── E2
 │    ↓
 │   V3
 │
 └── E3
      ↓
     V4

และ:

E3
 ├── parent: E1
 ├── dependency: E2
 └── supersedes: E0

⸻

18. Evolution DAG

Lineage ควรเป็น Directed Acyclic Graph

       E1
      /  \
     E2  E3
      \  /
       E4

ทำให้สามารถตอบ:

Which changes contributed to V4?

⸻

19. Descendant Tracking

หาก:

E1
 ↓
E2
 ↓
E3

และ E1 ถูก invalidate:

E1 INVALID

ระบบต้องตรวจ:

E2 affected?
E3 affected?

ไม่ควรปล่อย descendants ทำงานต่อโดยไม่ตรวจ dependency

⸻

20. Parent Integrity

ทุก evolution ต้องอ้าง:

parent_version

และตรวจว่า parent มีอยู่จริง

ห้าม:

V8 claims parent V7

ถ้า V7 ไม่เคยมีใน Ledger

⸻

21. Artifact Integrity

Evolution artifact ต้องมี identity

artifact_id
version
hash
build_id
signature

เพื่อป้องกัน:

"Ledger บอกว่า V3 แต่ของจริงเป็นไฟล์อื่น"

⸻

22. Cryptographic Integrity

ระดับสูงสามารถใช้:

Hash
Signed Manifest
Hash Chain
Merkle Structure
Digital Signature

ตัวอย่าง:

E1
 ↓ hash
E2
 ↓ hash
E3

หาก E2 ถูกแก้:

chain integrity fails

⸻

23. Immutable Semantics

Ledger เป็น append-only ในเชิง semantic

ถ้าข้อมูลเดิมผิด:

WRONG RECORD

ห้าม:

UPDATE RECORD

แต่ให้:

CORRECTION RECORD

ตัวอย่าง:

E42
 ↓
E42-CORRECTION-01

⸻

24. Correction Record

CorrectionRecord {
    correction_id
    target_record
    reason
    old_interpretation
    corrected_interpretation
    evidence_refs[]
    author
    timestamp
}

ประวัติเดิมยังคงอยู่

⸻

25. No Silent Rewrite

ห้าม:

Old evolution:
Failed
Later:
Changed to Success

โดยไม่มี event ใหม่

ต้องเป็น:

E1:
Failed
E1-Correction:
Evidence shows partial success

⸻

26. Rollback Record

Rollback เป็น evolution event ใหม่

ไม่ใช่การลบ evolution เดิม

E42
 ↓
Rollback R42
 ↓
Restore V17

ดังนั้น:

Rollback ≠ Erase

⸻

27. Failed Evolution

Evolution ที่ fail ยังมีคุณค่า

Failed Evolution
        ↓
Experience
        ↓
Reflection
        ↓
Learning

Failure ต้องถูกเก็บ

เพราะ:

Failed evolution
≠ useless evolution

มันสามารถเป็น evidence ว่า strategy หนึ่งใช้ไม่ได้ภายใต้เงื่อนไขหนึ่ง

⸻

28. Rollback Reason

ต้องระบุ:

Regression
Security
Performance
Cost
Verification Failure
Unexpected Side Effect
User Decision
Environment Change
Dependency Failure
Unknown

⸻

29. Evolution Comparison

Ledger ต้องรองรับ:

compare(E1,E2)

เช่น:

Component
Performance
Safety
Cost
Latency
Capability
Regression

⸻

30. Version Diff

ต้องสามารถตอบ:

What changed from V17 to V18?

ผล:

Memory:
+ retrieval index
Planner:
+ retry heuristic
Routing:
provider weights changed
Tools:
no change
Authority:
no change

⸻

31. Governance Diff

สำคัญมาก

ต้องมี explicit diff:

Authority changes
Policy changes
Capability changes
Approval changes
Security changes

ถ้า:

Authority diff != empty

ให้เข้าสู่ governance review

⸻

32. Capability Diff

เมื่อ evolution เพิ่ม capability:

Before:
filesystem.read
After:
filesystem.read
filesystem.write

Ledger ต้องจับได้ว่า:

Capability Escalation

แม้ผู้พัฒนาจะเรียกมันว่า:

"feature improvement"

⸻

33. Permission Diff

แยก:

Capability Diff

จาก:

Permission Diff

ตัวอย่าง:

Capability:
browser.submit
Permission:
not granted

Evolution ที่เปลี่ยน permission ต้องมี approval ที่เหมาะสม

⸻

34. Model Lineage

ถ้า Veda เปลี่ยน model:

Model A
 ↓
EVO-100
 ↓
Model B

ต้องเก็บ:

Model version
Provider
Configuration
Prompt/context contract
Benchmark
Cost
Latency
Privacy
Approval
Deployment

⸻

35. Skill Lineage

Skill evolution:

Skill V1
 ↓
Experience
 ↓
Reflection
 ↓
Skill V2

ต้องรู้:

source experiences
validation
failure cases
transfer tests
deployment scope

⸻

36. Knowledge Lineage

Knowledge evolution:

Evidence
 ↓
Claim
 ↓
Knowledge V1
 ↓
New Evidence
 ↓
Knowledge V2

Ledger ต้องเชื่อมกับ RFC-0013

⸻

37. Memory Lineage

Memory changes:

Memory Created
 ↓
Consolidated
 ↓
Updated
 ↓
Superseded

ต้องสามารถตรวจว่า memory ที่ active มาจากอะไร

⸻

38. Architecture Lineage

Architecture evolution:

Architecture V1
 ↓
EVO-ARCH-01
 ↓
Architecture V2

ต้องเก็บ:

Components Added
Components Removed
Interfaces Changed
Dependencies Changed
Data Flow Changed
Authority Boundary Changed

⸻

39. Evolution Scope

ทุก record ต้องระบุ:

LOCAL
TASK
PROJECT
AGENT
SYSTEM
MULTI_AGENT
EXTERNAL
PHYSICAL

⸻

40. Evolution Environment

ต้องระบุ environment:

DEV
TEST
SIMULATION
STAGING
CANARY
PRODUCTION
RECOVERY

เพราะ:

Passed in DEV

ไม่เท่ากับ:

Passed in PRODUCTION

⸻

41. Reproducibility

Ledger ต้องเก็บข้อมูลที่จำเป็นต่อการ reproduce:

Source Version
Model Version
Prompt Version
Knowledge Snapshot
Memory Snapshot
Tool Version
Configuration
Random Seed
Environment
Dependencies
Dataset
Benchmark

ถ้าทำไม่ได้ ต้องบันทึก:

REPRODUCIBILITY = PARTIAL

ไม่สร้างภาพว่าทำซ้ำได้

⸻

42. Historical Reconstruction

สามารถถาม:

What was Veda's architecture on date T?

หรือ:

Which evolution was active at time T?

โดยใช้:

Chronicle
+
Evolution Ledger
+
Snapshots

⸻

43. Evolution Timeline

ต้องสร้าง timeline:

2026-01-01
V1
2026-01-10
E1: Memory improvement
2026-01-15
E2: Planner improvement
2026-01-21
E3: Routing update
2026-02-03
E4: Rollback E3

⸻

44. Time Model

Ledger ต้องรองรับ:

Proposal Time
Approval Time
Deployment Time
Activation Time
Observation Time
Verification Time
Rollback Time
Expiration Time

เพื่อไม่ให้:

"created at"

ตัวเดียวพยายามเป็นทุกอย่าง

⸻

45. Bitemporal Evolution

รองรับ:

Valid Time
Transaction Time

ตัวอย่าง:

Evolution deployed Jan 1
Discovered invalid Jan 10
Historical validity:
Jan 1 → Jan 10

แต่ record discovery เกิด Jan 10

⸻

46. Evolution State Reconstruction

สามารถถาม:

Which evolution was active when Incident X happened?

ระบบต้อง traverse:

Incident
 ↓
World State
 ↓
System Version
 ↓
Active Evolutions
 ↓
Relevant Dependencies

⸻

47. Incident Correlation

หากเกิด:

Security Incident

สามารถค้น:

Recent Evolutions
Changed Capabilities
Changed Tools
Changed Models
Changed Policies

แล้วสร้าง candidate causal relationships

แต่ไม่ประกาศ causality จนกว่า RFC-0022 จะประเมิน

⸻

48. Evolution Impact Graph

Evolution
 ├── Component
 ├── Capability
 ├── Skill
 ├── Model
 ├── Tool
 ├── Memory
 └── Policy

ต่อไป:

Component
 ↓
Action
 ↓
Outcome
 ↓
Incident

ทำให้สามารถทำ blast-radius analysis ได้

⸻

49. Learning-to-Evolution Trace

ตัวอย่างเต็ม:

EXP-120
 ↓
REF-87
 ↓
LESSON-41
 ↓
LEARN-22
 ↓
EVO-18
 ↓
V22
 ↓
DEPLOY-91
 ↓
VERIFY-33
 ↓
OUTCOME-17

นี่คือ Evolution Receipt

⸻

50. Evolution Receipt

EvolutionReceipt {
    evolution_id
    source
    change
    approval
    validation
    deployment
    expected_outcome
    actual_outcome
    verification
    final_status
    lineage
    integrity
}

Receipt เป็น compact proof/reference สำหรับ audit และ debugging

⸻

51. Trust Level

Evolution record สามารถมี:

UNVERIFIED
RECORDED
SUPPORTED
VERIFIED
INDEPENDENTLY_VERIFIED
CRYPTOGRAPHICALLY_VERIFIED

ห้าม:

Recorded = Verified

⸻

52. Evolution Confidence

Confidence ต้องแยกจาก integrity

Integrity:
"This record was not altered."
Confidence:
"This evolution actually caused the observed improvement."

สองอย่างไม่เหมือนกัน

⸻

53. Evolution Causality

Ledger สามารถบอก:

E1 preceded E2

แต่ไม่ควรบอก:

E1 caused E2

โดยอัตโนมัติ

Causal attribution ใช้ RFC-0022

⸻

54. Evolution Conflicts

ถ้า records ขัดกัน:

Record A:
E1 deployed
Record B:
E1 not deployed

ต้องสร้าง:

Ledger Conflict

ส่ง RFC-0014

ไม่เลือก record ตามลำดับเวลาง่าย ๆ

⸻

55. Missing History

ถ้าประวัติหาย:

V10
 ↓
UNKNOWN
 ↓
V12

ต้องไม่สร้าง:

V11

ขึ้นมาเอง

สถานะ:

HISTORY_GAP

ต้องถูกเก็บอย่างชัดเจน

⸻

56. History Integrity

Integrity levels:

L0 — Unprotected
L1 — Append-only
L2 — Hash-linked
L3 — Signed
L4 — Tamper-evident distributed

Critical evolution ควรใช้ระดับสูงกว่า low-risk change

⸻

57. Storage

แบ่ง:

HOT
WARM
COLD
IMMUTABLE

HOT:

recent active evolutions

COLD:

historical lineage

IMMUTABLE:

critical governance evolution
security evolution
major architecture changes

⸻

58. Query API

get_evolution()
get_evolution_lineage()
get_ancestors()
get_descendants()
get_version()
compare_versions()
get_active_evolutions()
get_evolution_at_time()
get_source_chain()
get_evidence_bundle()
get_approval()
get_deployment()
get_outcome()
get_rollbacks()
get_conflicts()
find_evolutions_affecting()
find_evolutions_from()
find_evolutions_before()
find_evolutions_after()
reconstruct_system_history()

⸻

59. Audit API

audit_evolution()
verify_lineage()
verify_integrity()
verify_approval()
verify_deployment()
verify_outcome()
verify_provenance()
detect_history_gap()
detect_orphan_evolution()
detect_unapproved_change()
detect_unauthorized_capability_change()

⸻

60. Orphan Detection

หากพบ:

System changed

แต่ไม่มี:

Evolution Record

ให้สร้าง:

UNATTRIBUTED_CHANGE

ระดับสูงต้อง:

BLOCK
ALERT
INVESTIGATE

⸻

61. Unauthorized Change Detection

ถ้าพบ:

Change

แต่:

No approval

ให้สร้าง:

UNAUTHORIZED_EVOLUTION

และห้าม mark:

ACTIVE

โดยอัตโนมัติ

⸻

62. Ledger Reconciliation

Ledger ต้อง reconcile กับ:

Chronicle
Git
Artifact Registry
Model Registry
Tool Registry
Deployment System
Runtime State

ตัวอย่าง:

Ledger:
V18 deployed
Runtime:
V17

ต้องเกิด:

STATE_MISMATCH

⸻

63. External System Reconciliation

ระบบภายนอกเป็น authority ของ state ตัวเอง

ดังนั้น:

Ledger:
Deployment completed
External:
deployment failed

ผลคือ:

Ledger says event occurred
External verification says effect did not occur

ห้าม overwrite อย่างใดอย่างหนึ่ง

ต้องเก็บทั้งสอง evidence

⸻

64. Evolution Security

ภัยสำคัญ:

LEDGER-SEC-01 Record Tampering
LEDGER-SEC-02 Provenance Forgery
LEDGER-SEC-03 Approval Forgery
LEDGER-SEC-04 Version Substitution
LEDGER-SEC-05 Artifact Substitution
LEDGER-SEC-06 Rollback Tampering
LEDGER-SEC-07 History Deletion
LEDGER-SEC-08 History Gap Masking
LEDGER-SEC-09 Unauthorized Evolution
LEDGER-SEC-10 Capability Escalation
LEDGER-SEC-11 Governance Mutation
LEDGER-SEC-12 Lineage Poisoning
LEDGER-SEC-13 False Outcome
LEDGER-SEC-14 Benchmark Forgery
LEDGER-SEC-15 Verification Forgery
LEDGER-SEC-16 Timestamp Manipulation
LEDGER-SEC-17 Dependency Hiding
LEDGER-SEC-18 Canary Escape
LEDGER-SEC-19 Rollback Suppression
LEDGER-SEC-20 Audit Bypass

⸻

65. Ledger State Machine

CREATED
   ↓
VALIDATING
   ↓
COMMITTED
   ↓
ACTIVE
   ↓
SUPERSEDED
   ↓
ARCHIVED

Alternative:

CORRECTED
INVALIDATED
QUARANTINED
DISPUTED

⸻

66. Evolution Commit Rule

Evolution ไม่ถือว่า committed เพียงเพราะ deployment command สำเร็จ

ต้องมี:

Change Recorded
+
Artifact Identified
+
Approval Verified
+
Deployment Recorded
+
Verification Recorded

ตาม policy

⸻

67. Evolution Receipt Chain

Proposal Receipt
        ↓
Evaluation Receipt
        ↓
Approval Receipt
        ↓
Deployment Receipt
        ↓
Verification Receipt
        ↓
Outcome Receipt

สามารถเชื่อมเป็น chain เดียว

⸻

68. Human Review

High-impact evolution ต้องมี:

Human Reviewer
Review Scope
Evidence
Risk
Change Manifest
Rollback Plan

และ review ต้องถูกเก็บใน Ledger

⸻

69. Review Independence

หากผู้สร้าง evolution เป็นคนเดียวกับผู้อนุมัติ:

Conflict of Interest

policy อาจกำหนดให้ต้องมี second reviewer

โดยเฉพาะ:

Security
Authority
Financial
Physical
Architecture

⸻

70. Ledger and Self Model

RFC-0033 Self Model ต้องสามารถถาม:

Which version am I?

และ:

Which evolutions produced this version?

ดังนั้น:

Self Model
    ↕
Evolution Ledger

⸻

71. Ledger and Identity

RFC-0039 จะกำหนด identity

Evolution Ledger ต้องผูก:

Who/What created the proposal?
Who approved?
Which agent executed?
Which system deployed?
Which artifact was used?

⸻

72. Ledger and Agent Passport

Multi-agent Veda:

Agent A
Agent B
Agent C

ต้องสามารถตรวจ:

Which agent proposed E1?
Which agent approved E1?
Which agent deployed E1?

⸻

73. Ledger and Trust

RFC-0041 Trust Engine จะใช้ข้อมูล:

Evolution history
Rollback rate
Verification rate
Unauthorized changes
Provenance integrity

เพื่อประเมิน trust

แต่:

Trust ≠ Authority

⸻

74. Ledger and Neural Packages

เมื่อ package ถูกนำเข้า:

Skill Package
Model Package
Knowledge Package

ต้องบันทึก:

Package ID
Version
Source
Hash
Signature
Compatibility
Approval
Evolution lineage

⸻

75. Evolution Import

External evolution artifact:

External Package
      ↓
Verify
      ↓
Quarantine
      ↓
Benchmark
      ↓
Approval
      ↓
Deploy

ห้าม:

Download
↓
Execute

⸻

76. Evolution Export

Veda สามารถ export:

Evolution Package

ประกอบด้วย:

Change
Evidence
Benchmarks
Dependencies
Lineage
Verification
Rollback

โดยต้องเคารพ privacy และ secret boundaries

⸻

77. Privacy

Ledger อาจมีข้อมูลละเอียดมาก

ดังนั้นต้องรองรับ:

PUBLIC
PRIVATE
SENSITIVE
RESTRICTED
SECRET

Secrets ต้องไม่ถูกเก็บเป็น plaintext

ใช้ references:

secret_ref
credential_ref
vault_ref

⸻

78. Retention

บาง evolution ต้องเก็บตลอดอายุระบบ:

Constitution-related
Security
Authority
Major Architecture
Critical Incidents

บาง evolution:

temporary optimization

อาจ archive ได้ตาม policy

แต่ archive:

≠ delete history silently

⸻

79. Evolution Search

สามารถ query:

"Show every evolution that changed planner behavior."
"Show every evolution that caused rollback."
"Show all changes related to GitHub."
"Show all unauthorized changes."
"Show all changes derived from experience EXP-100."
"Show all model changes after V20."

⸻

80. Forensic Investigation

เมื่อเกิด incident:

Incident
 ↓
Time
 ↓
Active Version
 ↓
Evolution Lineage
 ↓
Changed Components
 ↓
Relevant Evidence
 ↓
Approval
 ↓
Deployment
 ↓
Outcome

สามารถ reconstruct ได้

⸻

81. Evolution Attribution

ต้องแยก:

Temporal Association

จาก:

Causal Attribution

Ledger บอก:

E1 happened before incident.

Causal Engine อาจประเมิน:

E1 likely contributed.

แต่สอง statement ไม่ใช่อันเดียวกัน

⸻

82. Evolution Quality Metrics

Ledger ต้อง track:

Trace Completeness
Provenance Completeness
Approval Completeness
Deployment Completeness
Verification Completeness
Reproducibility
Rollback Coverage
Lineage Integrity
Attribution Coverage

⸻

83. Ledger Health

Ledger เองต้องมี diagnostics:

Orphan Records
Broken Hash Chain
Missing Parent
Missing Approval
Missing Verification
Unattributed Change
Conflicting Records
Missing Artifact
History Gap

ส่งต่อ RFC-0034

⸻

84. Ledger Recovery

หาก Ledger storage เสีย:

Primary
 ↓
Replica
 ↓
Archive
 ↓
Checkpoint
 ↓
Integrity Verification

Recovery ต้องไม่สร้างประวัติที่ไม่เคยมี

หาก reconstruct ไม่ได้:

UNKNOWN

⸻

85. Snapshot

Ledger สามารถสร้าง:

Evolution Snapshot

ประกอบด้วย:

Current Version
Active Evolutions
Lineage Root
Hashes
Integrity Check
Timestamp

ใช้สำหรับ recovery และ audit

⸻

86. Snapshot Verification

ก่อนใช้ snapshot:

Hash
Signature
Parent
Chronology
Lineage

ต้องผ่าน verification

⸻

87. Evolution Ledger and Git

Git สามารถเป็น implementation backend สำหรับ code evolution ได้

แต่:

Git ≠ Evolution Ledger

Git รู้:

commit
diff
branch
merge

Evolution Ledger ต้องรู้เพิ่ม:

Why
Evidence
Approval
Risk
Benchmark
Verification
Outcome
Rollback
Authority

⸻

88. Example

สมมติ Veda มีปัญหา:

Git operations fail after remote state changes.

Experience:

EXP-200

Reflection:

REF-91

Lesson:

LESSON-40
"Verify remote state before retry."

Learning:

LEARN-30

Evolution:

EVO-50

Change:

Git Tool Wrapper
V3 → V4

Benchmark:

Success:
91% → 97%
Duplicate actions:
-70%
Verification failures:
-50%

Approval:

Human H1

Deployment:

V21

Verification:

VER-800

Outcome:

Verified improvement

Ledger:

EXP-200
 ↓
REF-91
 ↓
LESSON-40
 ↓
LEARN-30
 ↓
EVO-50
 ↓
V21
 ↓
VER-800

นี่คือ lineage ที่สามารถ audit ได้ตั้งแต่:

"ทำไม Veda ถึงทำแบบนี้?"

ย้อนกลับไปถึง:

"มันเคยเจออะไร?"

⸻

89. Example: Bad Evolution

EVO-51

เปลี่ยน planner

Benchmark:

+8%

แต่ production:

Safety regression
Verification failure

Ledger:

EVO-51
 ↓
DEPLOY
 ↓
INCIDENT
 ↓
ROLLBACK

และ:

EVO-51
status = ROLLED_BACK

ห้ามลบ EVO-51 เพื่อทำให้ history ดูสวย

⸻

90. Example: Unattributed Change

Runtime พบ:

Planner version = V9

Ledger บอก:

Latest approved = V8

สร้าง:

UNATTRIBUTED_CHANGE

แล้ว:

Freeze affected component
Investigate
Verify
Recover

⸻

91. Final Evolution Lineage

ระบบควรสามารถสร้าง:

Veda V42
├── Constitution: V1
├── Architecture: V8
├── Brain: V12
├── Planner: V19
├── Memory: V31
├── Model: Provider-X/V7
├── Skills:
│   ├── Git/V4
│   ├── Research/V8
│   └── Coding/V11
│
└── Evolutions:
    ├── E1
    ├── E2
    ├── E3
    └── E42

⸻

92. API Summary

record_evolution()
get_evolution()
update_evolution_status()
record_proposal()
record_evaluation()
record_approval()
record_deployment()
record_verification()
record_outcome()
record_rollback()
get_lineage()
get_ancestors()
get_descendants()
compare_versions()
get_version_at_time()
verify_integrity()
verify_provenance()
verify_approval()
verify_deployment()
detect_unattributed_change()
detect_history_gap()
detect_orphan()
detect_conflict()
reconstruct_evolution()
reconstruct_system_version()
create_snapshot()
verify_snapshot()
export_ledger()
import_ledger()
archive_evolution()
restore_evolution()

⸻

93. Events

EvolutionRecorded
EvolutionUpdated
EvolutionCorrected
EvolutionInvalidated
EvolutionSuperseded
EvolutionProposalLinked
EvolutionEvidenceLinked
EvolutionEvaluationLinked
EvolutionApprovalLinked
EvolutionDeploymentLinked
EvolutionVerificationLinked
EvolutionOutcomeLinked
EvolutionRollbackLinked
LineageCreated
LineageUpdated
LineageConflictDetected
LineageBroken
UnattributedChangeDetected
UnauthorizedChangeDetected
HistoryGapDetected
OrphanEvolutionDetected
LedgerIntegrityCheckStarted
LedgerIntegrityVerified
LedgerIntegrityFailed
SnapshotCreated
SnapshotVerified
SnapshotRestored

⸻

94. Invariants

LEDGER-1

Every consequential evolution must have a unique identity.

LEDGER-2

Every evolution must have a version.

LEDGER-3

Every consequential evolution must have provenance.

LEDGER-4

Every evolution must identify its target.

LEDGER-5

Every evolution must identify its parent version.

LEDGER-6

Evolution history must be append-only semantically.

LEDGER-7

Corrections must be represented as new records.

LEDGER-8

Rollback must not erase the original evolution.

LEDGER-9

Failed evolution must remain recorded.

LEDGER-10

Approval must reference an exact evolution version.

LEDGER-11

Deployment must reference an exact artifact/version.

LEDGER-12

Verification must reference the actual deployed version.

LEDGER-13

Expected outcome and actual outcome must remain distinct.

LEDGER-14

Recorded does not mean verified.

LEDGER-15

Temporal precedence does not establish causality.

LEDGER-16

Lineage must preserve parent-child relationships.

LEDGER-17

Broken lineage must be represented explicitly.

LEDGER-18

Missing history must never be fabricated.

LEDGER-19

Unattributed changes must be detectable.

LEDGER-20

Unauthorized changes must be detectable.

LEDGER-21

Capability changes must be traceable.

LEDGER-22

Authority changes require explicit governance records.

LEDGER-23

Artifact identity must be verifiable.

LEDGER-24

Critical records should support integrity verification.

LEDGER-25

Historical reconstruction must preserve uncertainty.

LEDGER-26

Snapshots must be versioned and integrity-checkable.

LEDGER-27

Rollback must preserve lineage.

LEDGER-28

Evolution dependencies must be reconstructable.

LEDGER-29

Critical evolution history must survive ordinary component failure.

LEDGER-30

No evolution history may be silently rewritten to conceal failure, regression, unauthorized change, or governance violation.

⸻

95. Reference Architecture

                    ┌─────────────────────┐
                    │      EXPERIENCE     │
                    └──────────┬──────────┘
                               ↓
                    ┌─────────────────────┐
                    │     REFLECTION      │
                    └──────────┬──────────┘
                               ↓
                    ┌─────────────────────┐
                    │      LEARNING       │
                    └──────────┬──────────┘
                               ↓
                    ┌─────────────────────┐
                    │     EVOLUTION       │
                    └──────────┬──────────┘
                               ↓
                    ┌─────────────────────┐
                    │  EVOLUTION LEDGER   │
                    └──────────┬──────────┘
                               ↓
                 ┌─────────────┴─────────────┐
                 ↓                           ↓
          Chronicle                       Identity
                 ↓                           ↓
          Historical                  Trust / Passport
          Reconstruction                    ↓
                 ↓                     Authorization
                 └─────────────┬─────────────┘
                               ↓
                           VEDA STATE

⸻

96. Final Principle

Evolution Ledger ทำให้ Veda ไม่ได้มีเพียง:

Memory of what happened

แต่มี:

Provenance of why it changed
Lineage of what changed
Evidence for whether it worked
History of who approved it
Record of what happened afterward

ดังนั้นหลักสุดท้ายคือ:

If Veda evolves,
the evolution must leave a receipt.
If Veda changes,
the change must have a lineage.
If Veda fails,
the failure must remain visible.
If Veda rolls back,
the rollback must itself be recorded.
If history is missing,
Veda must say UNKNOWN.
If authority changed,
the ledger must expose it.

A system that can evolve without an evolution ledger can improve faster than it can explain itself. Veda must never cross that boundary.