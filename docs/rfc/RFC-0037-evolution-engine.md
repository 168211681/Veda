RFC-0037 — Evolution Engine

Status: Draft
Layer: 14 — Learning & Evolution
Depends On: RFC-0001 through RFC-0036
Feeds Into: RFC-0038 Evolution Ledger, RFC-0047 Neural Package Format, RFC-0049 Intent Computing Architecture, RFC-0050 World Computing Architecture

Primary Principle:

Veda may evolve its capabilities, but it must never evolve its authority.

⸻

1. Abstract

RFC-0037 กำหนด Evolution Engine ของ Veda

Evolution Engine คือระบบที่ควบคุมการเปลี่ยนแปลงระยะยาวของ Veda โดยรับ Learning Proposals จาก RFC-0036 แล้วประเมินว่า:

* ควรเปลี่ยนหรือไม่
* ควรเปลี่ยนอะไร
* เปลี่ยนในระดับใด
* ผลกระทบมีขนาดเท่าไร
* ต้องใช้หลักฐานมากแค่ไหน
* ต้อง simulation หรือ benchmark หรือไม่
* ต้องให้มนุษย์อนุมัติหรือไม่
* สามารถ rollback ได้หรือไม่
* หลังเปลี่ยนแล้ว Veda ดีขึ้นจริงหรือไม่

Architecture:

Experience
    ↓
Reflection
    ↓
Learning
    ↓
Evolution Candidate
    ↓
Impact Analysis
    ↓
Safety Analysis
    ↓
Simulation
    ↓
Benchmark
    ↓
Authorization
    ↓
Deployment
    ↓
Verification
    ↓
Monitoring
    ↓
Keep / Rollback

Evolution Engine ไม่ใช่ระบบที่ให้ Veda:

"แก้ตัวเองได้ตามใจ"

แต่เป็น:

"สร้างการเปลี่ยนแปลงที่ตรวจสอบได้และอยู่ภายใต้ governance"

⸻

2. Motivation

ระบบ AI ปกติ:

Model
   ↓
Deploy
   ↓
Use
   ↓
Update โดยมนุษย์

Veda ต้องรองรับ:

Use
 ↓
Experience
 ↓
Reflection
 ↓
Learning
 ↓
Evolution Proposal
 ↓
Evaluation
 ↓
Deployment
 ↓
New Experience

จึงเกิดวงจร:

System
 ↓
Experience
 ↓
Improvement
 ↓
Evaluation
 ↓
Evolution
 ↓
System'

แต่ feedback loop นี้มีความเสี่ยง

หาก evaluation signal ผิด:

Bad Evaluation
      ↓
Bad Learning
      ↓
Bad Evolution
      ↓
Better ability to repeat bad behavior

ดังนั้น Evolution Engine ต้องเป็น control layer ระหว่าง Learning และ Self-Modification

งานสำรวจ Agentic Evolution ปี 2026 ระบุว่าการวิวัฒน์แบบ autonomous ทำงานได้แข็งแรงที่สุดเมื่อมี deterministic หรือ independent verifier และเมื่อไม่มี verifier ที่เชื่อถือได้ การใช้ self-referential/proxy signals อาจให้ผลลดลงเมื่อทำซ้ำหลายรอบ (Microsoft)

⸻

3. Critical Distinctions

Learning ≠ Evolution
Evolution ≠ Self-Modification
Capability ≠ Authority
Optimization ≠ Safety
Benchmark Gain ≠ Real-World Improvement
Simulation Success ≠ Reality Success
Model Update ≠ Architecture Update
Architecture Update ≠ Constitution Update
Autonomy ≠ Sovereignty

⸻

4. Evolution Scope

Evolution สามารถเกิดขึ้นในหลายระดับ

E0 — No Evolution
E1 — Memory
E2 — Knowledge
E3 — Heuristic
E4 — Skill
E5 — Planner / Workflow
E6 — Tool / Capability Configuration
E7 — Routing
E8 — Prompt / Context Architecture
E9 — Model Adaptation
E10 — Model Replacement
E11 — Architecture
E12 — Core System

แต่ไม่ได้หมายความว่า Veda สามารถแก้ทุกระดับเอง

⸻

5. Evolution Authority Boundary

แบ่งเป็น:

SELF-MANAGED
APPROVAL-REQUIRED
HUMAN-ONLY
IMMUTABLE

ตัวอย่าง:

Self-managed

Retrieval ranking
Low-risk memory organization
Non-critical heuristics
Provider performance statistics
Cache strategy

Approval-required

Planner strategy
New skills
Routing changes
Tool configuration
Model changes
Persistent behavioral changes

Human-only

Authority model
Security boundary
High-risk capabilities
Human approval rules
External financial permissions
Physical-world permissions
Core governance

Immutable

Constitution
Human sovereignty
Audit integrity
Authorization boundary
Historical integrity
Core safety invariants

⸻

6. Core Rule

Evolution Engine ต้อง enforce:

Veda can improve:
HOW IT THINKS
HOW IT PLANS
HOW IT RETRIEVES
HOW IT USES TOOLS
HOW IT LEARNS
HOW IT OPTIMIZES
But cannot autonomously redefine:
WHO HAS AUTHORITY
WHAT IS ALLOWED
WHAT COUNTS AS HUMAN OVERRIDE
WHAT THE CONSTITUTION MEANS

⸻

7. Evolution Candidate

EvolutionCandidate {
    evolution_id
    version
    source_learning_refs[]
    source_experience_refs[]
    source_reflection_refs[]
    target_type
    target_ref
    current_version
    proposed_version
    proposed_change
    objective
    expected_benefits[]
    assumptions[]
    risks[]
    failure_modes[]
    blast_radius
    reversibility
    dependencies[]
    required_capabilities[]
    required_resources[]
    simulation_requirements[]
    benchmark_requirements[]
    approval_level
    rollback_strategy
    status
    created_at
    updated_at
}

⸻

8. Evolution Classification

ทุก candidate ต้องถูกจัดประเภท

OPTIMIZATION
CORRECTION
ADAPTATION
EXTENSION
REPLACEMENT
RESTRUCTURING
ARCHITECTURAL
GOVERNANCE

⸻

9. Optimization

ปรับสิ่งเดิมให้ดีขึ้น

ตัวอย่าง:

Prompt efficiency
Retrieval ranking
Caching
Routing
Planning heuristic

โดย behavior contract เดิมไม่เปลี่ยน

⸻

10. Correction

แก้ defect

Observed failure
      ↓
Root cause
      ↓
Corrective change

Correction ต้องมี verification ที่ชัดเจน

⸻

11. Adaptation

ปรับระบบให้เข้ากับ environment

เช่น:

API changed
OS changed
Tool changed
Model changed
Network changed
User workflow changed

Adaptation ต้องตรวจว่าไม่ได้เปลี่ยน behavior เกิน scope

⸻

12. Extension

เพิ่ม capability ใหม่

เช่น:

New Tool
New Skill
New Provider
New Sensor
New Integration

Extension มี blast radius สูงกว่า optimization

⸻

13. Replacement

เปลี่ยน component

Model A → Model B
Tool A → Tool B
Planner V1 → Planner V2

ต้องทำ compatibility test

⸻

14. Restructuring

เปลี่ยน internal architecture โดย behavior contract ตั้งใจให้เหมือนเดิม

ตัวอย่าง:

Memory Backend V1
        ↓
Memory Backend V2

ต้องตรวจ:

Functional equivalence
Performance
Reliability
Security
Data integrity

⸻

15. Architectural Evolution

เปลี่ยนวิธีที่ Veda ทำงานโดยพื้นฐาน

เช่น:

Brain architecture
World model
Planning architecture
Multi-agent orchestration
Memory architecture
Capability fabric

ระดับนี้ไม่ควรเกิดจาก learning proposal ธรรมดา

ต้องเข้าสู่:

Evolution Review
Simulation
Benchmark
Security Review
Human Approval

⸻

16. Governance Evolution

การเปลี่ยน:

Authority
Policy
Approval
Security
Constitution
Human override

เป็น category แยก

โดย default:

SELF-MODIFICATION = FORBIDDEN

เว้นแต่มี explicit human authorization และ governance path ที่กำหนดไว้

⸻

17. Evolution Pipeline

LEARNING_PROPOSAL
        ↓
CANDIDATE_CREATED
        ↓
CLASSIFIED
        ↓
IMPACT_ANALYSIS
        ↓
RISK_ANALYSIS
        ↓
DEPENDENCY_ANALYSIS
        ↓
SIMULATION
        ↓
BENCHMARK
        ↓
SECURITY_REVIEW
        ↓
APPROVAL
        ↓
CANARY / SHADOW
        ↓
DEPLOY
        ↓
VERIFY
        ↓
MONITOR
        ↓
KEEP / ROLLBACK

⸻

18. Impact Analysis

ต้องวิเคราะห์:

Capability Impact
Memory Impact
Knowledge Impact
Tool Impact
Planner Impact
Decision Impact
Security Impact
Performance Impact
Resource Impact
User Impact
World Impact

⸻

19. Blast Radius

ทุก evolution ต้องมี blast radius

LOCAL
TASK
PROJECT
AGENT
SYSTEM
MULTI_AGENT
EXTERNAL
PHYSICAL

ตัวอย่าง:

Retrieval ranking
→ LOCAL
Planner
→ SYSTEM
Tool permission
→ EXTERNAL
Robot control
→ PHYSICAL

ยิ่ง blast radius สูง ยิ่งต้องมี approval และ verification สูง

⸻

20. Reversibility

จำแนก:

FULLY_REVERSIBLE
PARTIALLY_REVERSIBLE
COMPENSATABLE
NON_REVERSIBLE
UNKNOWN

ตัวอย่าง:

Prompt change
→ Fully reversible
Database migration
→ Partially reversible
Email sent
→ Compensatable
Money transfer
→ Potentially non-reversible
Physical action
→ Potentially non-reversible

Evolution ต้องไม่ถือว่า rollback software version เท่ากับ rollback reality

⸻

21. Evolution Risk

แนวคิด:

Risk =
Probability × Impact × Exposure

โดยพิจารณาเพิ่ม:

Uncertainty
Reversibility
Blast Radius
External Effects
Authority
Verification Strength

⸻

22. Evolution Gates

ทุก evolution ต้องผ่าน gate ตามระดับ

Gate 0
Syntax / Integrity
Gate 1
Unit / Functional Tests
Gate 2
Regression
Gate 3
Simulation
Gate 4
Security
Gate 5
Benchmark
Gate 6
Canary
Gate 7
Real-world Verification

ไม่จำเป็นต้องใช้ทุก gate กับทุก change แต่ policy ต้องกำหนดอย่างชัดเจน

⸻

23. Independent Evaluation

Evolution Engine ไม่ควรประเมินตัวเองด้วย evaluator เดียวกับที่สร้าง change หาก risk สูง

ตัวอย่าง:

Generator
    ↓
Candidate
Independent Evaluator
    ↓
Evaluation

เพื่อลด:

Self-confirmation
Reward hacking
Evaluator gaming

⸻

24. Deterministic Verifiers

หากสามารถใช้ deterministic verifier ได้ ต้อง prefer

ตัวอย่าง:

Compiler
Type checker
Unit test
Hash verification
Schema validator
Cryptographic verification
Formal constraint
Database invariant

แทน:

LLM says it looks correct.

ประโยคหลังฟังดูเหมือน engineering จนกระทั่ง production ล่มตอนตีสาม

⸻

25. Simulation

ก่อน deployment:

Current System
      ↓
Clone
      ↓
Apply Evolution
      ↓
Simulate
      ↓
Compare

ใช้ RFC-0024

⸻

26. Historical Replay

นำ historical workloads มาทดสอบ:

Old System
vs
Candidate System

ตัวชี้วัด:

Success
Failure
Latency
Cost
Safety
Verification
Goal Completion

⸻

27. Regression Protection

ทุก evolution ต้องมี regression suite

Critical Tasks
Past Failures
Past Successes
Edge Cases
Security Cases
User Corrections

Evolution ห้าม optimize เพียง metric เดียวแล้วทำลายความสามารถอื่น

⸻

28. Evolution Benchmark

Benchmark ต้องมี:

BASELINE
CANDIDATE
HELD_OUT
ADVERSARIAL
REGRESSION
REAL_WORLD

⸻

29. Canary Deployment

Evolution ที่มี risk:

Candidate
 ↓
Shadow
 ↓
Canary
 ↓
Limited
 ↓
Full

แต่ละ phase มี exit criteria

⸻

30. Shadow Mode

Production System
      ↓
Real Task
      ↓
Old Version → Real Action
New Version → Prediction Only

เปรียบเทียบ:

Prediction
Outcome
Verification

โดย candidate ไม่มีสิทธิ์สร้าง external side effect

⸻

31. Canary

Canary จำกัด:

Users
Tasks
Tools
Resources
Time
Capabilities

และต้องมี automatic stop conditions

ตัวอย่าง:

Error Rate > threshold
Safety Failure > threshold
Verification Failure > threshold
Cost > threshold
Unknown State > threshold

⸻

32. Deployment

Evolution artifact ต้องมี:

artifact_id
version
parent_version
build_id
configuration
dependencies
tests
benchmarks
approval
security_review
deployment_time
rollback_version

⸻

33. Evolution Transaction

Evolution ควรมี transactional semantics:

Prepare
 ↓
Validate
 ↓
Deploy
 ↓
Verify
 ↓
Commit

หาก verify fail:

Rollback

หรือ:

Quarantine

⸻

34. Partial Evolution

บาง change อาจ apply สำเร็จเพียงบางส่วน

ต้องแยก:

COMPLETE
PARTIAL
FAILED
UNKNOWN

ห้ามตีความ:

Deployment request accepted
=
Evolution succeeded

⸻

35. Unknown Deployment State

หาก connection หายระหว่าง deployment:

Deploy request
     ↓
Connection lost

สถานะ:

UNKNOWN

ไม่ใช่:

FAILED

และไม่ใช่:

SUCCESS

ต้อง query actual system state ก่อน retry

⸻

36. Evolution Monitoring

หลัง deployment ต้อง monitor:

Performance
Reliability
Safety
Resource Usage
Goal Success
Verification
Regression
User Feedback
Security
Drift

⸻

37. Observation Window

Evolution ต้องมี monitoring window

Immediate
Short-term
Long-term

เพราะบาง regression ไม่ปรากฏทันที

⸻

38. Evolution Expiration

บาง evolution ควรมี TTL

ตัวอย่าง:

Temporary routing workaround
Temporary API adaptation
Experimental heuristic

เมื่อหมดอายุ:

REVIEW
RENEW
REMOVE

ไม่ใช่ปล่อยให้ experimental behavior กลายเป็น permanent โดยบังเอิญ

⸻

39. Evolution Drift

Environment เปลี่ยน:

Old assumption
      ↓
Environment changes
      ↓
Evolution becomes invalid

ระบบต้องสามารถ:

Detect
Invalidate
Re-evaluate
Rollback

⸻

40. Evolution Conflicts

ตัวอย่าง:

Evolution A:
increase speed
Evolution B:
increase verification depth

อาจเกิด:

Resource Conflict
Performance Conflict
Goal Conflict
Policy Conflict

ส่งต่อ RFC-0014

⸻

41. Evolution Dependency Graph

Evolution A
   ↓
Evolution B
   ↓
Evolution C

ถ้า rollback A:

B and C

อาจต้องถูก invalidated ด้วย

ดังนั้น Evolution Engine ต้องรักษา dependency graph

⸻

42. Evolution Branches

ทดลองได้หลายสาย:

Baseline
 ├── Evolution A
 ├── Evolution B
 └── Evolution C

เปรียบเทียบก่อนเลือก deployment

⸻

43. Evolution Fork

Candidate สามารถ fork จาก version:

V10
 ├── V11-A
 ├── V11-B
 └── V11-C

แต่ละ branch ต้องมี provenance

⸻

44. Evolution Merge

การ merge evolution ต้องตรวจ:

Compatibility
Conflict
Regression
Security
Dependencies

ห้าม merge เพียงเพราะแต่ละ branch ผ่าน benchmark ของตัวเอง

⸻

45. Capability Evolution

เมื่อ evolution เพิ่ม capability:

New Capability

ต้องผ่าน:

Tool Registry
Capability Registry
Authorization
Security
Verification

Evolution Engine ไม่มีสิทธิ์สร้าง authority จาก capability ใหม่

หลัก:

Capability ≠ Permission

⸻

46. Tool Evolution

Tool update ต้องตรวจ:

Interface
Schema
Side Effects
Risk
Verification
Idempotency
Dependencies
Version

ใช้ RFC-0028 และ RFC-0029

⸻

47. Model Evolution

Model replacement:

Model A
 ↓
Candidate Model B

ต้องเปรียบเทียบ:

Quality
Reasoning
Coding
Tool use
Latency
Cost
Memory
Privacy
Reliability
Safety

ไม่ใช่ดู benchmark ตัวเดียว

⸻

48. Memory Architecture Evolution

หากเปลี่ยน memory backend:

Existing Memory
 ↓
Migration
 ↓
Validation

ต้องตรวจ:

Data Integrity
Provenance
Recall
Precision
Historical Reconstruction
Privacy
Access Control

⸻

49. Planner Evolution

Planner เป็น high-impact component

ต้องตรวจ:

Goal completion
Planning quality
Constraint compliance
Risk
Resource usage
Verification
Recovery

⸻

50. Brain Evolution

Brain architecture เป็น critical

การเปลี่ยน:

Reasoning
Attention
Context
Memory access
Executive control
Metacognition

ต้องมี:

Architecture Review
Simulation
Regression
Security Review
Human Approval

⸻

51. Constitution Protection

Constitution เป็น root constraint

Evolution Engine:

READ Constitution

สามารถ:

CHECK compliance

สามารถ:

PROPOSE amendment

แต่ไม่สามารถ:

SELF-APPROVE amendment

⸻

52. Authority Protection

Evolution ต้องไม่เปลี่ยน:

Human Authority
Approval Requirement
Capability Boundary
Permission Model
Emergency Override

โดยอัตโนมัติ

⸻

53. Auditability

ทุก evolution ต้องสามารถตอบ:

Who proposed it?
Why?
Based on what experience?
What evidence?
What changed?
Who approved?
Which tests?
Which benchmark?
Which version?
When deployed?
What happened?
Was it rolled back?
Why?

⸻

54. Evolution Ledger

RFC-0038 จะเก็บ permanent evolution history

Proposal
 ↓
Evaluation
 ↓
Approval
 ↓
Deployment
 ↓
Outcome
 ↓
Rollback / Keep

RFC-0037 ควบคุม process

RFC-0038 เก็บ historical truth ของ evolution

⸻

55. Evolution Chronicle

ทุก state transition ต้องถูกส่งไป RFC-0031 และ RFC-0032

EvolutionRequested
EvolutionEvaluated
EvolutionApproved
EvolutionApplied
EvolutionVerified
EvolutionRolledBack

⸻

56. Self-Diagnostics Integration

ก่อน evolution:

Self-Diagnostics
 ↓
Current Health

ถ้า Veda อยู่ใน:

DEGRADED
FAILURE
RECOVERY
SAFE_MODE

อาจ block evolution

เพราะระบบที่กำลังไฟไหม้ไม่ใช่เวลาที่ดีสำหรับการรีโนเวตบ้าน

⸻

57. Resource Constraints

Evolution ต้อง account:

CPU
GPU
RAM
Storage
Network
Energy
Time
API Budget
Human Attention

Evolution ที่ดีแต่แพงจนระบบใช้งานจริงไม่ได้ ไม่ถือว่าเป็น improvement โดยอัตโนมัติ

⸻

58. Evolution Queue

ระบบควรมี:

CRITICAL
HIGH
NORMAL
LOW
EXPERIMENTAL

และสามารถ:

merge
deduplicate
defer
cancel
prioritize

⸻

59. Evolution Scheduling

Evolution ไม่ควรแทรกตัวเองระหว่าง critical execution

มี maintenance window:

ACTIVE
MAINTENANCE
EXPERIMENT
DEPLOYMENT
RECOVERY

⸻

60. Evolution Failure

เมื่อ evolution fail:

Detect
 ↓
Stop
 ↓
Verify State
 ↓
Rollback / Recover
 ↓
Diagnose
 ↓
Create Experience
 ↓
Reflect

Failure ของ evolution กลายเป็น Experience ใหม่

Evolution Failure
      ↓
Experience
      ↓
Learning
      ↓
Future Evolution

⸻

61. Evolution Loop

                    ┌─────────────────┐
                    │     CURRENT     │
                    │     VEDA        │
                    └────────┬────────┘
                             │
                             ▼
                        Experience
                             │
                             ▼
                         Reflection
                             │
                             ▼
                          Learning
                             │
                             ▼
                     Evolution Candidate
                             │
                             ▼
                      Impact Analysis
                             │
                             ▼
                         Simulation
                             │
                             ▼
                         Benchmark
                             │
                             ▼
                       Authorization
                             │
                             ▼
                         Deployment
                             │
                             ▼
                        Verification
                             │
                             ▼
                         Monitoring
                             │
                    ┌────────┴────────┐
                    ▼                 ▼
                  KEEP             ROLLBACK
                    │                 │
                    └────────┬────────┘
                             ▼
                         Experience

⸻

62. Recursive Evolution

Evolution สามารถสร้าง improvement loop:

V1
 ↓
E1
 ↓
V2
 ↓
E2
 ↓
V3
 ↓
E3

แต่ทุก generation ต้อง retain:

Parent
Change
Evidence
Evaluation
Outcome

จึงสามารถตอบได้ว่า:

Why does V3 exist?

⸻

63. Evolution Depth

ต้องติดตาม:

generation
parent_version
ancestor_versions[]

ตัวอย่าง:

V1
 ↓
V2
 ↓
V3
 ↓
V4

ถ้าเกิด regression ใน V4:

Find last known-good ancestor

แล้ว rollback

⸻

64. Evolution Stability

ไม่ใช่แค่:

Vn > Vn-1

แต่ต้องดู:

V1 → V2 → V3 → V4

หาก performance oscillates:

↑ ↓ ↑ ↓

แสดงว่า learning/evolution loop อาจ unstable

ต้องหยุด evolution และวิเคราะห์

⸻

65. Evolution Convergence

ระบบควร monitor:

Performance Trend
Variance
Regression
Change Frequency
Rollback Frequency

หาก evolution สร้างการเปลี่ยนแปลงต่อเนื่องโดยไม่มี net gain:

Evolution Churn

ต้อง throttle หรือ freeze

⸻

66. Evolution Freeze

สามารถ freeze:

Component
Capability
Model
Architecture
Entire System

เหตุผล:

Security Incident
Unknown Regression
Instability
Resource Crisis
Human Decision
Environment Uncertainty

⸻

67. Emergency Stop

ต้องมี:

GLOBAL EVOLUTION STOP

ซึ่งหยุด:

Candidate Generation
Deployment
Self-Modification

แต่ไม่ควรหยุด:

Logging
Monitoring
Verification
Recovery
Human Approval

⸻

68. Safe Mode

ใน Safe Mode:

No Evolution
No New Capability
No Policy Change
No Autonomous Deployment

อนุญาตเฉพาะ:

Observation
Diagnostics
Verification
Recovery
Human Interaction

⸻

69. Evolution Security Threats

EVO-SEC-01 Unauthorized Self-Modification
EVO-SEC-02 Governance Mutation
EVO-SEC-03 Evaluation Gaming
EVO-SEC-04 Reward Hacking
EVO-SEC-05 Benchmark Overfitting
EVO-SEC-06 Regression Masking
EVO-SEC-07 Malicious Evolution Proposal
EVO-SEC-08 Dependency Poisoning
EVO-SEC-09 Artifact Tampering
EVO-SEC-10 Rollback Tampering
EVO-SEC-11 Provenance Forgery
EVO-SEC-12 Canary Escape
EVO-SEC-13 Shadow-Mode Escape
EVO-SEC-14 Capability Escalation
EVO-SEC-15 Authority Escalation
EVO-SEC-16 Evolution Loop Amplification
EVO-SEC-17 Version Confusion
EVO-SEC-18 Configuration Drift
EVO-SEC-19 Safety Regression
EVO-SEC-20 Recursive Instability

⸻

70. API

create_evolution_candidate()
classify_evolution()
analyze_impact()
analyze_risk()
analyze_dependencies()
evaluate_reversibility()
simulate_evolution()
run_historical_replay()
run_benchmark()
run_regression()
run_security_review()
create_shadow_deployment()
create_canary()
request_approval()
approve_evolution()
reject_evolution()
deploy_evolution()
verify_evolution()
monitor_evolution()
freeze_evolution()
rollback_evolution()
quarantine_evolution()
compare_versions()
get_evolution_status()
get_evolution_lineage()
get_evolution_history()

⸻

71. Events

EvolutionCandidateCreated
EvolutionClassified
EvolutionImpactAnalyzed
EvolutionRiskEvaluated
EvolutionDependencyDetected
EvolutionSimulationStarted
EvolutionSimulationCompleted
EvolutionBenchmarkStarted
EvolutionBenchmarkCompleted
EvolutionRegressionDetected
EvolutionSecurityReviewStarted
EvolutionSecurityReviewCompleted
EvolutionApprovalRequested
EvolutionApproved
EvolutionRejected
EvolutionShadowStarted
EvolutionCanaryStarted
EvolutionDeploymentStarted
EvolutionDeploymentCompleted
EvolutionVerificationStarted
EvolutionVerified
EvolutionFailed
EvolutionRolledBack
EvolutionQuarantined
EvolutionFrozen
EvolutionResumed
EvolutionSuperseded
EvolutionExpired
EvolutionDriftDetected
EvolutionChurnDetected
EvolutionStabilityViolationDetected

⸻

72. Metrics

Evolution Engine ต้องวัด:

Evolution Gain
Regression Rate
Rollback Rate
Mean Time To Recovery
Change Failure Rate
Benchmark Gain
Real-World Gain
Safety Regression Rate
Evolution Churn
Deployment Success Rate
Verification Success Rate
Canary Failure Rate
Resource Cost
Human Approval Rate
Human Override Rate

⸻

73. Evolution Gain

ต้องวัดทั้ง:

Capability
Reliability
Safety
Cost
Latency
Generalization

ตัวอย่าง:

Capability      +12%
Reliability     +8%
Safety          +3%
Cost            -5%
Latency         -10%

จึงค่อยสรุปว่า candidate มี evidence ของ improvement

ไม่ใช่ดู score เดียวแล้วประกาศชัยชนะ

⸻

74. Human Authority

Human สามารถ:

Approve
Reject
Pause
Freeze
Rollback
Override

ได้ตาม authority policy

Evolution Engine ต้องไม่สามารถ:

Override Human
Remove Human Override
Disable Audit
Change Constitution
Grant Itself Authority

⸻

75. Human Approval Object

EvolutionApproval {
    approval_id
    evolution_id
    approver_identity
    authority_scope
    decision
    conditions
    approved_version
    approved_scope
    timestamp
    expiry
    signature
}

Approval ต้องผูกกับ:

exact version
exact scope
exact target

ไม่ใช่:

"อนุมัติให้ Veda พัฒนาตัวเอง"

เพราะประโยคนั้นกว้างพอจะทำให้ infrastructure engineer ทุกคนต้องไปนั่งสมาธิ

⸻

76. Evolution Contract

ทุก evolution ต้องมี:

Objective
Scope
Expected Benefit
Risk
Dependencies
Tests
Verification
Approval
Rollback
Monitoring
Expiration

หากองค์ประกอบสำคัญหาย:

Evolution = BLOCKED

⸻

77. Evolution State Machine

PROPOSED
   ↓
CLASSIFIED
   ↓
IMPACT_ANALYZED
   ↓
RISK_ANALYZED
   ↓
VALIDATED
   ↓
SIMULATED
   ↓
BENCHMARKED
   ↓
SECURITY_REVIEWED
   ↓
APPROVAL_REQUIRED
   ↓
APPROVED
   ↓
SHADOW
   ↓
CANARY
   ↓
DEPLOYING
   ↓
VERIFYING
   ↓
MONITORING
   ↓
ACTIVE

Alternative states:

REJECTED
BLOCKED
DEFERRED
FAILED
ROLLED_BACK
QUARANTINED
FROZEN
SUPERSEDED
EXPIRED

⸻

78. Invariants

EVO-1

Evolution must have a traceable source.

EVO-2

Evolution must identify its target.

EVO-3

Evolution must identify its intended benefit.

EVO-4

Evolution must identify its risks.

EVO-5

Evolution must identify its blast radius.

EVO-6

Evolution must identify reversibility.

EVO-7

Evolution must preserve provenance.

EVO-8

Evolution must be versioned.

EVO-9

Evolution must preserve parent lineage.

EVO-10

Evolution must not silently modify history.

EVO-11

Evolution must not grant authority.

EVO-12

Evolution must not bypass authorization.

EVO-13

Evolution must not modify Constitution autonomously.

EVO-14

Governance changes require explicit authorization.

EVO-15

High-risk evolution requires stronger validation.

EVO-16

External side effects require appropriate verification.

EVO-17

Simulation does not establish real-world success.

EVO-18

Benchmark success does not establish generalization.

EVO-19

Critical evolution requires regression testing.

EVO-20

Evolution must support rollback where technically possible.

EVO-21

Unknown deployment state must not be treated as success.

EVO-22

Partial deployment must remain explicitly partial.

EVO-23

Evolution dependencies must be tracked.

EVO-24

Rollback dependencies must be considered.

EVO-25

Canary deployments require explicit boundaries.

EVO-26

Evolution must be observable.

EVO-27

Evolution must be auditable.

EVO-28

Evolution must be freezeable.

EVO-29

Evolution must be stoppable without disabling audit and recovery.

EVO-30

Evolution remains subordinate to Constitution and Human Authority.

⸻

79. Relationship to Other RFCs

RFC-0035
Experience
      ↓
RFC-0036
Reflection & Learning
      ↓
RFC-0037
Evolution Engine
      ↓
RFC-0038
Evolution Ledger

โดย:

RFC-0035 = What happened?
RFC-0036 = What did we learn?
RFC-0037 = Should the system change?
RFC-0038 = What changes actually happened?

⸻

80. Final Architecture

Veda จึงได้ adaptive loop:

                 ┌──────────────────────┐
                 │      REAL WORLD      │
                 └──────────┬───────────┘
                            │
                            ▼
                         Action
                            │
                            ▼
                       Verification
                            │
                            ▼
                       Experience
                            │
                            ▼
                       Reflection
                            │
                            ▼
                         Learning
                            │
                            ▼
                     Evolution Proposal
                            │
                            ▼
                     Safety / Impact
                            │
                            ▼
                        Simulation
                            │
                            ▼
                         Benchmark
                            │
                            ▼
                       Authorization
                            │
                            ▼
                        Deployment
                            │
                            ▼
                        Verification
                            │
                            ▼
                         Monitoring
                            │
                 ┌──────────┴──────────┐
                 ▼                     ▼
                KEEP                ROLLBACK
                 │                     │
                 └──────────┬──────────┘
                            ▼
                         Experience

⸻

81. Final Principle

Veda may improve itself, but no improvement is allowed to redefine who controls Veda.

หรือในรูปแบบสถาปัตยกรรม:

Capability may evolve.
Knowledge may evolve.
Memory may evolve.
Skills may evolve.
Models may evolve.
Architecture may evolve under governance.
Authority does not evolve autonomously.
Constitution does not evolve autonomously.
Human sovereignty does not evolve autonomously.

และนี่คือเส้นแบ่งระหว่าง:

SELF-IMPROVING SYSTEM

กับ

SELF-GOVERNING SYSTEM

Veda ถูกออกแบบให้เป็นอย่างแรก ไม่ใช่อย่างหลัง