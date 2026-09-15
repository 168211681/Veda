RFC-0034 — Veda Self-Diagnostics

Status: Draft
Layer: 13 — Self
Depends On: RFC-0001, RFC-0002, RFC-0003, RFC-0004, RFC-0009, RFC-0010, RFC-0012, RFC-0013, RFC-0014, RFC-0015, RFC-0016, RFC-0017, RFC-0018, RFC-0019, RFC-0020, RFC-0026, RFC-0027, RFC-0028, RFC-0029, RFC-0031, RFC-0032, RFC-0033

⸻

1. Abstract

RFC-0034 กำหนด Veda Self-Diagnostics

Self-Diagnostics คือระบบที่ตรวจสอบว่า:

สิ่งที่ Veda คิดว่าตัวเองเป็น
        ↓
ตรงกับสภาพจริงหรือไม่

โดยตรวจ:

* core health
* brain health
* memory health
* knowledge health
* model/provider health
* tool health
* capability availability
* authorization consistency
* resource state
* process state
* dependency state
* security state
* performance
* prediction error
* verification integrity
* data integrity
* configuration drift
* model drift
* capability drift
* behavioral anomalies
* self-model inconsistencies

Self-Diagnostics ไม่ใช่แค่ health check

แต่เป็นระบบ:

Observe
→ Measure
→ Compare
→ Detect
→ Diagnose
→ Assess Severity
→ Recommend Recovery
→ Verify

⸻

2. Motivation

RFC-0033 ทำให้ Veda รู้ว่า:

I have capability X.
I have resource Y.
I am using model Z.
I am healthy.

แต่คำถามสำคัญกว่าคือ:

Are those claims actually true?

Self-Diagnostics จึงทำหน้าที่เป็น independent operational layer ที่ตรวจสอบ Self Model กับ evidence จาก runtime และระบบภายนอก

หลักการ:

Self Model = representation
Diagnostics = examination
Verification = evidence-based confirmation

⸻

3. Non-Goals

RFC นี้ไม่กำหนด:

* consciousness
* subjective experience
* emotions
* personality
* metaphysical self
* unrestricted self-modification
* automatic architecture redesign
* automatic constitutional changes
* unlimited self-repair

Diagnostics สามารถตรวจพบปัญหาและเสนอ recovery ได้

แต่ไม่ได้รับ authority เพิ่มขึ้นจากการตรวจพบปัญหา

⸻

4. Core Principle

Prediction → Observation → Comparison → Diagnosis

ตัวอย่าง:

Prediction:
tool latency < 2 sec
Observation:
tool latency = 12 sec
Difference:
+10 sec
Diagnosis:
performance degradation

⸻

5. Fundamental Distinctions

Health ≠ Capability
Capability ≠ Authority
Performance ≠ Correctness
Execution Success ≠ Outcome Success
Self Report ≠ Verification
Anomaly ≠ Failure
Failure ≠ Root Cause
Symptom ≠ Cause
Prediction Error ≠ System Bug
Unknown ≠ Healthy

⸻

6. Diagnostic Objectives

Self-Diagnostics ต้องสามารถ:

1. ตรวจ health
2. ตรวจ functionality
3. ตรวจ availability
4. ตรวจ performance
5. ตรวจ consistency
6. ตรวจ integrity
7. ตรวจ dependency
8. ตรวจ security
9. ตรวจ resource pressure
10. ตรวจ behavioral anomaly
11. ตรวจ prediction error
12. ตรวจ model drift
13. ตรวจ capability drift
14. ตรวจ configuration drift
15. ตรวจ goal/process anomalies
16. ตรวจ self-model corruption
17. ระบุ severity
18. ระบุ confidence
19. ระบุ probable causes
20. สร้าง diagnostic evidence
21. เสนอ recovery
22. ตรวจผล recovery

⸻

7. Diagnostic Architecture

                ┌───────────────────┐
                │   Runtime State   │
                └─────────┬─────────┘
                          │
                ┌─────────▼─────────┐
                │ Observation Layer │
                └─────────┬─────────┘
                          │
        ┌─────────────────▼─────────────────┐
        │       Diagnostic Engine           │
        │                                   │
        │  Metrics                          │
        │  Rules                            │
        │  Baselines                        │
        │  Anomaly Detection                │
        │  Dependency Analysis              │
        │  Consistency Analysis             │
        │  Failure Diagnosis                │
        └─────────────────┬─────────────────┘
                          │
             ┌────────────▼────────────┐
             │ Diagnostic Finding     │
             └────────────┬────────────┘
                          │
             ┌────────────▼────────────┐
             │ Severity / Confidence   │
             └────────────┬────────────┘
                          │
          ┌───────────────▼────────────────┐
          │ Recovery / Escalation / Monitor│
          └────────────────────────────────┘

⸻

8. Diagnostic Layers

Diagnostics แบ่งเป็น:

L0 Process
L1 Runtime
L2 Resource
L3 Component
L4 Capability
L5 Dependency
L6 Integration
L7 Behavioral
L8 Goal
L9 System
L10 Security

⸻

9. L0 Process Diagnostics

ตรวจ:

* process alive
* process stuck
* process crash
* deadlock
* timeout
* runaway loop
* excessive resource consumption

ตัวอย่าง:

Process:
Planner-0042
Expected:
complete < 30 sec
Observed:
running 9 min

Finding:

PROCESS_STALL

⸻

10. L1 Runtime Diagnostics

ตรวจ:

runtime availability
exceptions
crashes
latency
event loop
thread health
memory pressure
I/O
network

⸻

11. L2 Resource Diagnostics

ตรวจ:

CPU
GPU
RAM
VRAM
Storage
Network
Bandwidth
Battery
Thermal
API quota
Token budget
Financial budget

สถานะ:

NORMAL
ELEVATED
HIGH
CRITICAL
EXHAUSTED
UNKNOWN

⸻

12. L3 Component Diagnostics

Component:

Brain
Memory
Knowledge
Planner
Router
Verification
Chronicle
Tool Registry
Authorization

แต่ละ component มี health contract

ตัวอย่าง:

Brain
    latency
    error_rate
    queue_depth
    task_completion
    provider_failure

⸻

13. L4 Capability Diagnostics

ตรวจ:

Can capability X actually execute?

ตัวอย่าง:

Self Model:
filesystem.write = AVAILABLE
Diagnostic:
write probe failed

ผล:

CAPABILITY_DEGRADED

Self Model ต้องถูกแจ้งให้ reconcile

⸻

14. L5 Dependency Diagnostics

ตรวจ dependency graph:

Veda
 └── Browser
      └── Network
           └── DNS
                └── Internet

ถ้า DNS ล้ม:

Browser
→ degraded
Network requests
→ degraded
Cloud model
→ potentially degraded

ไม่ใช่รายงานว่า component ทุกตัวพังแยกกันโดยไม่เข้าใจ causal chain

⸻

15. L6 Integration Diagnostics

ตรวจ boundary:

Veda ↔ MCP
Veda ↔ GitHub
Veda ↔ OS
Veda ↔ Database
Veda ↔ Browser
Veda ↔ Cloud

ตรวจ:

* protocol
* schema
* authentication
* authorization
* timeout
* version
* compatibility
* response integrity

⸻

16. L7 Behavioral Diagnostics

ตรวจพฤติกรรมที่ผิดจาก baseline

เช่น:

tool call frequency suddenly increases
planning loop becomes unusually long
verification failures increase
model disagreement spikes
resource consumption increases

Behavioral anomaly ไม่ได้แปลว่าระบบเสียเสมอไป

สถานะอาจเป็น:

ANOMALOUS

ก่อนจะตัดสินว่า:

FAILED

⸻

17. L8 Goal Diagnostics

ตรวจ:

goal progress
goal drift
stalled goals
conflicting goals
unexpected goal changes
resource exhaustion

ตัวอย่าง:

Goal:
Build Veda RFC system
Observed:
Process repeatedly modifies unrelated files

Finding:

GOAL_DRIFT

⸻

18. L9 System Diagnostics

ตรวจ cross-component behavior

ตัวอย่าง:

Memory healthy
Brain healthy
Planner healthy
Tool healthy

แต่:

End-to-end task success = 0%

แสดงว่า component health อาจดีแต่ system health ไม่ดี

ดังนั้น:

Component Health ≠ System Health

⸻

19. L10 Security Diagnostics

ตรวจ:

privilege anomalies
unexpected capability use
credential access
unauthorized tool call
suspicious network activity
policy violations
sandbox escape indicators
prompt/context poisoning indicators

Security diagnostic finding ต้องสามารถ escalate ไปยัง security policy

⸻

20. Diagnostic Object

{
  "diagnostic_id": "...",
  "version": 1,
  "target_ref": "...",
  "diagnostic_type": "...",
  "observations": [],
  "expected_state": {},
  "actual_state": {},
  "finding": {},
  "severity": "...",
  "confidence": 0.0,
  "probable_causes": [],
  "evidence_refs": [],
  "verification_refs": [],
  "recommended_actions": [],
  "recovery_refs": [],
  "status": "...",
  "created_at": "...",
  "updated_at": "..."
}

⸻

21. Diagnostic Status

REQUESTED
OBSERVING
ANALYZING
FINDING
CONFIRMED
INCONCLUSIVE
FALSE_POSITIVE
RECOVERY_PROPOSED
RECOVERY_ACTIVE
RESOLVED
UNRESOLVED
ESCALATED
CLOSED

⸻

22. Severity

INFO
LOW
MEDIUM
HIGH
CRITICAL
CATASTROPHIC

Severity ต้องคำนึงถึง:

impact
probability
scope
irreversibility
security
data integrity
financial impact
physical impact
authority impact

⸻

23. Diagnostic Confidence

Confidence หมายถึง:

confidence that the finding is real

ไม่ใช่:

confidence that the root cause is correct

ต้องแยก:

finding_confidence
cause_confidence
recovery_confidence

⸻

24. Symptom vs Cause

ตัวอย่าง:

Symptom:
cloud model unavailable
Possible causes:
network failure
authentication failure
quota exhaustion
provider outage
DNS failure
policy block

Diagnostics ห้ามประกาศ:

network failure

เพียงเพราะ cloud model ใช้งานไม่ได้

ต้องมี evidence

⸻

25. Root Cause Analysis

Diagnostic Engine สามารถสร้าง causal hypothesis:

Observed:
Task failures ↑
Possible chain:
Network degradation
→ provider timeout
→ model fallback
→ local model overload
→ latency increase
→ task timeout

แต่ causal hypothesis ต้องเข้าสู่ RFC-0022 และสามารถเป็น:

HYPOTHESIZED

ไม่ใช่ verified cause โดยอัตโนมัติ

⸻

26. Diagnostic Evidence

Evidence sources:

runtime telemetry
system metrics
tool responses
logs
events
verification results
health probes
benchmarks
chronicle
external observations
human reports

⸻

27. Evidence Freshness

Diagnostic evidence ต้องตรวจ:

freshness
integrity
source identity
timestamp
scope

ข้อมูลเก่าที่เคยจริงไม่ได้แปลว่ายังจริง

⸻

28. Active Probes

Diagnostics สามารถใช้ probes ได้

ตัวอย่าง:

read filesystem
query database
call health endpoint
perform safe computation
test model invocation
check storage

แต่ probe ต้องประกาศ:

side_effect = none

ถ้า probe มี side effect ต้องผ่าน Action/Authorization/Verification

⸻

29. Passive Monitoring

Passive monitoring:

telemetry
events
logs
metrics
webhooks
health streams

ไม่ควรสร้าง side effect

⸻

30. Active vs Passive

Passive:
observe what happened
Active:
ask the system whether it works

Active probe ให้ evidence ที่ตรงกว่าในบางกรณี แต่ต้องมี cost และความเสี่ยง

⸻

31. Health Checks

Health check ระดับง่าย:

alive?

แต่ Self-Diagnostics ต้องตรวจมากกว่า:

alive
→ responsive
→ functional
→ correct
→ reliable

⸻

32. Liveness

Is the component running?

ตัวอย่าง:

process responds to heartbeat

⸻

33. Readiness

Can the component perform its intended function?

Process อาจ alive แต่:

database unavailable

ดังนั้น:

alive = true
ready = false

⸻

34. Functional Health

Does the component actually produce valid behavior?

ตัวอย่าง:

Planner responds

แต่ generated plans invalid ทั้งหมด

ดังนั้น:

liveness = healthy
functionality = unhealthy

⸻

35. Performance Health

ตรวจ:

latency
throughput
error rate
resource cost
queue depth
success rate

⸻

36. Reliability Health

วัด:

failure frequency
recovery success
verification success
repeatability
availability

⸻

37. Integrity Health

ตรวจ:

hash
signature
version
schema
event chain
snapshot
configuration
binary
model artifact

⸻

38. Configuration Drift

ตัวอย่าง:

Expected:
timeout = 30 sec
Observed:
timeout = 300 sec

Finding:

CONFIGURATION_DRIFT

⸻

39. Capability Drift

ตัวอย่าง:

Yesterday:
browser automation available
Today:
browser unavailable

Finding:

CAPABILITY_DRIFT

⸻

40. Model Drift

Model drift ใน Veda อาจเกิดจาก:

model version changed
provider behavior changed
prompt/context changed
routing changed
hardware changed
fine-tuning changed

ต้องบันทึก version ทุกครั้ง

⸻

41. Behavioral Drift

แม้ model version เดิม พฤติกรรมอาจเปลี่ยนจาก:

context changes
tool changes
memory changes
knowledge changes
environment changes

ดังนั้น diagnostics ต้องตรวจ system behavior ไม่ใช่ model version อย่างเดียว

⸻

42. Performance Baseline

แต่ละ component ต้องมี baseline

เช่น:

Planner:
P50 latency
P95 latency
P99 latency
success rate
verification rate

⸻

43. Baseline Must Be Contextual

Performance ของ:

simple coding task

ไม่ควรนำไปเทียบตรง ๆ กับ:

complex autonomous research

Baseline ต้องมี context:

task_type
model
hardware
environment
risk_level
input_size

⸻

44. Statistical Detection

Diagnostics สามารถใช้:

moving average
EWMA
percentiles
control limits
change point detection
rate changes
distribution shifts

แต่ statistical anomaly ไม่ใช่ root cause

⸻

45. Prediction Error Diagnostics

ระบบต้องเก็บ:

predicted
actual
error

ตัวอย่าง:

Predicted:
task = 60 sec
Actual:
task = 240 sec

Prediction error อาจชี้:

bad estimate
environment change
resource contention
unexpected dependency

⸻

46. Self-Model Error

สำคัญที่สุด:

Self Model Prediction
vs
Reality

ตัวอย่าง:

Self Model:
RAM = 16GB
Reality:
RAM available = 2GB

Finding:

SELF_MODEL_STALE

⸻

47. Diagnostic Categories

ขั้นต่ำ:

PROCESS
RESOURCE
COMPONENT
CAPABILITY
AUTHORITY
DEPENDENCY
INTEGRATION
PERFORMANCE
BEHAVIOR
SECURITY
DATA
INTEGRITY
CONFIGURATION
MODEL
GOAL
VERIFICATION
SELF_MODEL

⸻

48. Authority Diagnostics

ตรวจว่า:

Self Model permission
vs
Authorization Engine
vs
Active Lease

ถ้าไม่ตรง:

AUTHORITY_INCONSISTENCY

Authorization Engine เป็น source of authority

⸻

49. Capability vs Authority Diagnostic

ตัวอย่าง:

Capability:
filesystem.delete = AVAILABLE
Authority:
filesystem.delete = DENIED

นี่ไม่ใช่ system failure

เป็น:

EXPECTED_RESTRICTION

Diagnostics ต้องไม่สร้าง false alarm

⸻

50. Resource Contention

ตรวจว่า component แย่ง resource กันหรือไม่

ตัวอย่าง:

Brain
Planner
Embedding service
Local model

แย่ง RAM เดียวกัน

Finding:

RESOURCE_CONTENTION

⸻

51. Resource Exhaustion

สถานะ:

WARNING
HIGH
CRITICAL
EXHAUSTED

Recovery อาจ:

reduce concurrency
switch model
pause background tasks
free cache
defer work
request human intervention

Recovery จริงอยู่ภายใต้ RFC-0027

⸻

52. Dependency Failure Propagation

ตัวอย่าง:

Network
 ↓
Cloud Provider
 ↓
Router
 ↓
Brain
 ↓
Planner

Diagnostics ต้องระบุ:

primary_failure
secondary_impact

เพื่อไม่ให้สร้าง incident 20 รายการจาก failure เดียว

⸻

53. Cascading Failure Detection

ตรวจ pattern:

component A fails
→ component B retries
→ load increases
→ component C fails
→ retries increase
→ system collapse

Finding:

CASCADE_RISK

⸻

54. Retry Storm Detection

หาก:

retry_rate ↑↑

Diagnostics ต้องสามารถหยุดหรือ escalate ตาม policy

ไม่ใช่:

retry forever

เพราะมนุษย์ชอบเรียกการทำสิ่งเดิม 500 ครั้งว่า “ระบบอัตโนมัติ”

⸻

55. Loop Detection

ตรวจ:

Plan
→ Action
→ Failure
→ Same Plan
→ Same Action
→ Failure

Finding:

RECOVERY_LOOP

⸻

56. Cognitive Loop Detection

Brain loop:

Reason
→ Critique
→ Reason
→ Critique

ถ้าไม่เกิด progress:

COGNITIVE_STALL

⸻

57. Goal Loop

Goal
→ Plan
→ Replan
→ Goal
→ Replan

หาก goal satisfaction ไม่ขยับ:

GOAL_STAGNATION

⸻

58. Verification Loop

Action
→ Verify
→ Failed
→ Retry
→ Verify
→ Failed

ต้องมี bounded retry

⸻

59. Diagnostic Budgets

Diagnostics เองใช้ resource

ดังนั้นต้องมี:

CPU budget
Memory budget
Network budget
Time budget
Probe budget
API budget
Attention budget

Diagnostics ต้องไม่ทำให้ระบบที่กำลังพังพังหนักกว่าเดิม

⸻

60. Diagnostic Priority

Priority:

CRITICAL_SECURITY
CRITICAL_INTEGRITY
CRITICAL_DATA
CRITICAL_SAFETY
CRITICAL_OPERATION
HIGH
MEDIUM
LOW
BACKGROUND

⸻

61. Diagnostic Scheduling

ตรวจแบบ:

continuous
periodic
event-triggered
on-demand
pre-action
post-action
pre-deployment
post-deployment
recovery-triggered

⸻

62. Pre-Action Diagnostics

ก่อน action สำคัญ:

check capability
check authority
check resource
check dependency
check tool health
check verification availability

⸻

63. Post-Action Diagnostics

หลัง action:

execution
→ observation
→ verification
→ diagnostics

ตรวจ:

unexpected side effects
state drift
resource impact
goal impact
security impact

⸻

64. Recovery Diagnostics

หลัง recovery:

Did recovery actually restore the system?

ต้อง verify

ไม่ใช่:

recovery command returned 0

แล้วประกาศว่า system healed

⸻

65. Safe Mode

เมื่อ diagnostic severity สูง:

SAFE_MODE

อาจ:

disable risky actions
pause autonomous execution
allow read-only operations
preserve logs
request human approval
run diagnostics

⸻

66. Safe Mode Authority

Safe Mode policy ต้องมาจาก Constitution/Authorization

Self-Diagnostics ไม่สามารถสร้างกฎใหม่เอง

⸻

67. Degraded Mode

ไม่จำเป็นต้อง shutdown ทุกครั้ง

เช่น:

Cloud unavailable

Veda อาจ:

switch local model
reduce concurrency
disable nonessential tasks

หาก policy อนุญาต

⸻

68. Graceful Degradation

ระบบควรมี capability hierarchy:

PRIMARY
SECONDARY
FALLBACK
MINIMAL
SAFE

ตัวอย่าง:

Cloud reasoning
→ Local reasoning
→ Deterministic rules
→ Human approval

⸻

69. Diagnostic Finding Lifecycle

DETECTED
    ↓
CLASSIFIED
    ↓
ANALYZING
    ↓
CONFIRMED
    ↓
SEVERITY_ASSIGNED
    ↓
RECOVERY_PROPOSED
    ↓
RECOVERY_AUTHORIZED
    ↓
RECOVERY_EXECUTED
    ↓
RECOVERY_VERIFIED
    ↓
RESOLVED

Alternative:

FALSE_POSITIVE
INCONCLUSIVE
ESCALATED
UNRESOLVED

⸻

70. Diagnostic Correlation

หลาย findings อาจเป็นเหตุการณ์เดียวกัน

ตัวอย่าง:

network_failure
cloud_failure
browser_failure
github_timeout

Correlation:

Network outage

ต้องใช้ Event/Trace IDs และ causal evidence

⸻

71. Incident Object

Diagnostic findings ที่เกี่ยวข้องสามารถรวมเป็น:

Incident

ประกอบด้วย:

incident_id
findings
root_cause_hypotheses
impact
severity
affected_components
affected_goals
timeline
recovery
verification

⸻

72. Incident ≠ Root Cause

Incident:

Veda cannot access GitHub

Root cause อาจเป็น:

DNS failure

แต่ต้อง verify

⸻

73. Diagnostic Explanation

ต้องตอบ:

What failed?
When?
Where?
How was it detected?
What evidence supports it?
What may have caused it?
What was affected?
What remains unknown?
What action is recommended?

⸻

74. Unknown Cause

ผลลัพธ์ที่ถูกต้อง:

Failure confirmed.
Root cause unknown.

ไม่ต้องแต่งเรื่องให้ครบทุกช่องเพียงเพราะ UI อยากมีคำตอบ

⸻

75. False Positive

Diagnostics ต้องรองรับ:

finding detected
→ additional evidence
→ finding disproved

ต้องเก็บไว้ใน Chronicle

ไม่ลบประวัติ

⸻

76. False Negative

สำคัญยิ่งกว่า

ระบบอาจไม่ตรวจพบ failure

จึงต้องมี:

post-incident review
missed_detection
diagnostic_gap

เพื่อส่งต่อ RFC-0036 Learning

⸻

77. Diagnostic Learning

เมื่อพบ:

unexpected failure

สามารถสร้าง:

experience
→ reflection
→ lesson
→ diagnostic improvement proposal

แต่ deployment ของ diagnostic rule ต้องผ่าน Evolution Engine

⸻

78. Diagnostic Rule Object

{
  "rule_id": "...",
  "target": "...",
  "condition": "...",
  "expected": "...",
  "observation": "...",
  "severity": "...",
  "confidence": "...",
  "action": "...",
  "verification": "...",
  "version": 1
}

⸻

79. Rule Safety

Rule ห้าม:

directly grant authority
disable Constitution
delete Chronicle
hide findings
modify audit history

⸻

80. Diagnostic Benchmark

ต้องทดสอบ diagnostics ด้วย known failures:

network outage
disk full
RAM pressure
tool failure
model timeout
permission mismatch
corrupted snapshot
stale self model
provider outage
malicious tool

⸻

81. Fault Injection

สามารถสร้าง controlled failures ใน sandbox:

disconnect network
kill process
simulate RAM pressure
return invalid schema
expire capability lease
simulate provider timeout

เพื่อทดสอบ diagnostics

ต้องใช้ Simulation/Sandbox และห้ามกระทบ production โดยไม่ได้รับอนุญาต

⸻

82. Diagnostic Coverage

Metric:

failure_detection_coverage

เช่น:

Known failure classes = 100
Detected correctly = 91
Coverage = 91%

⸻

83. Detection Latency

วัด:

failure_occurs
→ detection

เรียกว่า:

MTTD
Mean Time To Detect

⸻

84. Recovery Detection Latency

วัด:

failure
→ detection
→ diagnosis
→ recovery

แต่ต้องแยก:

MTTD
MTTDI
MTTR

อย่ารวมทุกอย่างเป็นเลขเดียวจนไม่มีใครรู้ว่าระบบช้าตรงไหน

⸻

85. Recovery Verification

วัด:

recovery_attempt
→ verified recovery

เพราะ:

Recovery command success ≠ System recovery

⸻

86. Diagnostic Integrity

ทุก diagnostic record ต้องมี:

diagnostic_id
trace_id
source
timestamp
evidence
version
integrity

และบันทึกใน Chronicle

⸻

87. Diagnostic History

สามารถถาม:

When did this component first start degrading?

หรือ:

Has this failure happened before?

หรือ:

What recovery worked last time?

⸻

88. Recurring Failure Detection

หากพบ:

same failure
same component
same cause

ซ้ำ ๆ:

RECURRING_FAILURE

ส่งต่อ Learning/Evolution

⸻

89. Systemic Failure Detection

ถ้า failures หลายตัวมี common dependency:

Common dependency
→ multiple failures

Finding:

SYSTEMIC_FAILURE

⸻

90. Single Point of Failure

Diagnostics ต้องตรวจ:

critical dependency
with no fallback

ตัวอย่าง:

Only model provider
Only storage
Only network path
Only verification service

⸻

91. Diagnostic Dependency Graph

                    ┌──────────────┐
                    │ Verification │
                    └──────┬───────┘
                           │
        ┌──────────────────▼──────────────────┐
        │              Veda                   │
        └───────┬──────────┬──────────┬───────┘
                │          │          │
              Brain      Memory     Planner
                │          │          │
              Model      Storage     Tools
                │          │          │
             Network ──────┴──────────┘

⸻

92. Self-Diagnostics and Chronicle

ทุก diagnostic event ต้อง trace ได้:

Observation
→ Diagnostic
→ Finding
→ Decision
→ Recovery
→ Verification

Chronicle ทำให้ reconstruct timeline ได้

⸻

93. Self-Diagnostics and Verification

Diagnostics สามารถสร้าง verification requests

ตัวอย่าง:

Diagnostic:
filesystem.write may be unavailable

ส่ง:

Verification Engine

เพื่อ verify capability

⸻

94. Self-Diagnostics and Rollback

ถ้า deployment ทำให้:

error rate ↑
latency ↑
verification ↓

Diagnostics สามารถเสนอ:

rollback

แต่ actual rollback ต้องผ่าน RFC-0027

⸻

95. Self-Diagnostics and Attention

Critical diagnostic findings ต้องสร้าง attention candidate:

Diagnostic
→ Attention
→ Brain

เพื่อไม่ให้ระบบรู้ว่ากำลังไหม้แต่จัดเรื่องไฟล์ icon เป็น priority แรก

⸻

96. Self-Diagnostics and Planner

เมื่อพบ:

provider unavailable

Planner ต้องสามารถ:

replan

ด้วย capabilities ที่เหลือ

⸻

97. Self-Diagnostics and Router

Router ต้องได้รับ:

provider health
latency
availability
failure rate

เพื่อหลีกเลี่ยง provider ที่กำลังล้ม

⸻

98. Self-Diagnostics and Memory

Repeated failures สามารถสร้าง:

Failure Memory

เช่น:

When RAM < threshold
and model = X
task type = Y
failure probability increases

แต่ต้องมี evidence และ scope

⸻

99. Self-Diagnostics and Learning

Diagnostic history เป็น input สำคัญของ:

Experience
Reflection
Learning
Evolution

⸻

100. Self-Diagnostics and Evolution

Evolution Engine อาจได้รับ proposal:

Diagnostic:
Planner timeout failures increased 40%.
Proposal:
Improve planner termination strategy.

แต่ proposal ต้อง:

simulate
benchmark
security-check
authorize
deploy
monitor

⸻

101. Self-Diagnostics and Security

Security diagnostics ต้องสามารถ trigger:

quarantine
capability restriction
safe mode
human escalation
credential rotation
network isolation

ตาม policy

⸻

102. Diagnostic Security Threats

DIAG-SEC-01

Telemetry spoofing

DIAG-SEC-02

Diagnostic suppression

DIAG-SEC-03

False healthy status

DIAG-SEC-04

False critical status

DIAG-SEC-05

Probe abuse

DIAG-SEC-06

Diagnostic resource exhaustion

DIAG-SEC-07

Finding tampering

DIAG-SEC-08

Recovery hijacking

DIAG-SEC-09

Root-cause poisoning

DIAG-SEC-10

Health metric gaming

DIAG-SEC-11

Capability spoofing

DIAG-SEC-12

Authority spoofing

DIAG-SEC-13

Chronicle tampering

DIAG-SEC-14

Diagnostic data leakage

DIAG-SEC-15

Safe-mode abuse

⸻

103. Fail-Closed Diagnostics

High-risk uncertainty ต้องนำไปสู่:

UNKNOWN

หรือ:

REQUIRES_REVIEW

ไม่ใช่:

HEALTHY

ตัวอย่าง:

Security telemetry unavailable

ไม่ควรตีความ:

No security issue detected

แต่เป็น:

Security state unknown

⸻

104. Diagnostic APIs

run_diagnostic()
get_diagnostic()
get_health()
get_component_health()
get_capability_health()
get_dependency_health()
get_resource_health()
get_security_health()
detect_anomaly()
compare_baseline()
check_consistency()
check_integrity()
check_configuration()
check_capability()
check_authority()
trace_failure()
analyze_root_cause()
correlate_findings()
create_probe()
run_probe()
cancel_probe()
create_incident()
get_incident()
propose_recovery()
verify_recovery()
enter_safe_mode()
exit_safe_mode()
get_diagnostic_history()
get_metrics()
get_coverage()

⸻

105. Diagnostic Event Model

ขั้นต่ำ:

DiagnosticRequested
DiagnosticStarted
ObservationCollected
ProbeStarted
ProbeCompleted
AnomalyDetected
ConsistencyCheckStarted
ConsistencyViolationDetected
IntegrityCheckStarted
IntegrityViolationDetected
CapabilityCheckStarted
CapabilityCheckFailed
DependencyFailureDetected
PerformanceDegradationDetected
ConfigurationDriftDetected
ModelDriftDetected
BehavioralDriftDetected
GoalDriftDetected
SecurityAnomalyDetected
FindingCreated
FindingConfirmed
FindingRejected
IncidentCreated
RootCauseHypothesisCreated
RootCauseVerified
RecoveryProposed
RecoveryStarted
RecoveryCompleted
RecoveryVerified
SafeModeEntered
SafeModeExited
DiagnosticFailed
DiagnosticCompleted

⸻

106. Diagnostic State Machine

REQUESTED
   ↓
OBSERVING
   ↓
ANALYZING
   ↓
FINDING
   ↓
CONFIRMED
   ↓
SEVERITY
   ↓
RECOVERY / MONITOR / ESCALATE
   ↓
VERIFY
   ↓
RESOLVED

⸻

107. Critical Incident Flow

Critical anomaly
       ↓
Immediate attention
       ↓
Freeze risky actions
       ↓
Collect evidence
       ↓
Verify finding
       ↓
Classify impact
       ↓
Enter Safe/Degraded Mode
       ↓
Recovery Proposal
       ↓
Authorization
       ↓
Recovery
       ↓
Independent Verification
       ↓
Resume / Escalate

⸻

108. Diagnostic Snapshot

ก่อน recovery สำคัญ:

Self Snapshot
+
Runtime Snapshot
+
Diagnostic Snapshot
+
Chronicle checkpoint

เพื่อให้สามารถตรวจย้อนหลังได้

⸻

109. Diagnostic Reproducibility

Diagnostic result ควรเก็บ:

rule version
model version
configuration
inputs
observations
thresholds
environment
timestamp

เพื่อให้ replay ได้

⸻

110. Diagnostic Replay

สามารถ replay:

Historical State
→ Diagnostic Rules
→ Historical Observations
→ Finding

ใน:

READ_ONLY
NO_SIDE_EFFECT

mode

⸻

111. Diagnostic Simulation

สามารถทดสอบ:

What would diagnostics have detected?

โดยใช้ simulated world

ไม่เปลี่ยน production state

⸻

112. Diagnostic Calibration

ระบบต้องวัด:

true positive
false positive
true negative
false negative

เพื่อปรับ diagnostic rules

⸻

113. Critical Metric: False Negative

ใน high-risk subsystem:

false negative

อาจร้ายแรงกว่า:

false positive

ดังนั้น threshold ต้องขึ้นกับ risk

⸻

114. Risk-Based Diagnostics

Low risk:
periodic monitoring
Medium risk:
active verification
High risk:
continuous monitoring
Critical:
independent monitoring + fail-safe

⸻

115. Diagnostic Independence

สำหรับ critical components:

Component A

ไม่ควรเป็นผู้ตรวจสุขภาพของตัวเองเพียงคนเดียว

ควรมี:

external monitor
watchdog
independent verifier

⸻

116. Watchdog

Watchdog ตรวจ:

heartbeat
deadlock
resource runaway
process stall
unsafe state

และสามารถ trigger predefined safe response

ตาม policy ที่กำหนดไว้ล่วงหน้า

⸻

117. Watchdog Boundary

Watchdog ไม่ควรมีสิทธิ์ทั่วไปของ Veda

มันควรมี:

minimal capability
minimal authority

เช่น:

pause process
enter safe mode
notify human

⸻

118. Diagnostic Resource Protection

หากระบบอยู่ใน resource crisis:

Diagnostics MUST retain emergency budget

เพื่อให้ยังสามารถ:

detect
log
escalate
recover

ได้

⸻

119. Diagnostic Priority Inversion

ระบบต้องป้องกัน:

low-priority diagnostics

ใช้ resource จนทำให้:

critical diagnostics

ไม่สามารถทำงานได้

⸻

120. Diagnostic Storm

เมื่อ component ล้มจำนวนมาก:

1000 errors/sec

Diagnostics ต้อง:

deduplicate
aggregate
correlate
sample
prioritize

ไม่ใช่สร้าง 1000 incidents แยกกัน

⸻

121. Diagnostic Suppression

สามารถ suppress known noise ได้

แต่ต้อง:

scope
expiry
reason
owner
audit trail

และห้าม suppress:

critical security
integrity
safety

โดยไม่มี authority

⸻

122. Maintenance Mode

Maintenance สามารถลด diagnostic noise

แต่ต้องบันทึก:

maintenance_start
maintenance_end
disabled_checks
reason
authorization

⸻

123. Diagnostic Drift

Diagnostic rules เองสามารถ stale ได้

เช่น:

old threshold
old architecture
old model behavior

จึงต้อง monitor:

diagnostic effectiveness

ด้วย

⸻

124. Meta-Diagnostics

Veda ต้องสามารถตรวจ:

Are my diagnostics themselves working?

ตัวอย่าง:

Diagnostic Engine healthy?
Telemetry healthy?
Watchdog healthy?
Rules current?
Coverage sufficient?

⸻

125. Meta-Diagnostic Failure

หาก:

diagnostic engine unavailable

สถานะต้องเป็น:

DIAGNOSTIC_CAPABILITY_DEGRADED

ไม่ใช่:

SYSTEM_HEALTHY

⸻

126. Diagnostic Trust Levels

D0 Self-report
D1 Telemetry
D2 Deterministic health check
D3 Independent probe
D4 Independent verification
D5 Cryptographic/integrity evidence

Critical findingsควรใช้ระดับสูงขึ้นตาม risk

⸻

127. Diagnostic Finding Example

Finding:
CLOUD_PROVIDER_DEGRADED
Observed:
timeout rate = 42%
Baseline:
timeout rate = 1.2%
Evidence:
provider telemetry
request traces
independent health probe
Confidence:
0.98
Probable cause:
provider/network degradation
Impact:
cloud reasoning unavailable
Recovery:
switch to local provider
Status:
CONFIRMED

⸻

128. Self Model Mismatch Example

Self Model:
filesystem.write = AVAILABLE
Runtime:
permission denied
Authorization:
DENIED
Diagnostic:
SELF_MODEL_AUTHORITY_MISMATCH
Resolution:
update Self Model
invalidate capability assumption

⸻

129. Resource Example

Prediction:
RAM required = 6 GB
Observed:
available RAM = 3 GB
Diagnostic:
RESOURCE_INSUFFICIENT
Planner:
replan
Router:
select smaller model

⸻

130. Goal Drift Example

Goal:
Research topic X
Observed actions:
repeatedly modifying unrelated configuration
Diagnostic:
GOAL_DRIFT
Action:
pause execution
Brain:
inspect context
Planner:
rebuild plan

⸻

131. Recovery Example

Model deployment
      ↓
Latency +400%
      ↓
Diagnostic
      ↓
Performance regression
      ↓
Rollback proposal
      ↓
Simulation
      ↓
Authorization
      ↓
Rollback
      ↓
Verification
      ↓
Latency normal

⸻

132. Diagnostic Output Contract

ทุก finding ต้องตอบอย่างน้อย:

What happened?
What was expected?
What was observed?
How do we know?
How confident are we?
What is affected?
What remains unknown?
What should happen next?

⸻

133. Diagnostic Invariants

DIAG-1

Diagnostics MUST observe before concluding.

DIAG-2

Diagnostics MUST distinguish symptoms from causes.

DIAG-3

Diagnostics MUST preserve uncertainty.

DIAG-4

Diagnostics MUST NOT convert UNKNOWN into HEALTHY.

DIAG-5

Diagnostics MUST NOT convert anomaly directly into failure without evidence.

DIAG-6

Diagnostics MUST be traceable.

DIAG-7

Diagnostics MUST preserve evidence provenance.

DIAG-8

Diagnostic findings MUST be versioned.

DIAG-9

Diagnostic rules MUST be versioned.

DIAG-10

Diagnostic results MUST be time-aware.

DIAG-11

Diagnostic evidence MUST have freshness information.

DIAG-12

Diagnostics MUST distinguish liveness from readiness.

DIAG-13

Diagnostics MUST distinguish readiness from correctness.

DIAG-14

Diagnostics MUST distinguish performance from functionality.

DIAG-15

Diagnostics MUST distinguish capability from authority.

DIAG-16

Diagnostics MUST detect self-model inconsistencies.

DIAG-17

Diagnostics MUST detect dependency failures.

DIAG-18

Diagnostics MUST support cascading-failure analysis.

DIAG-19

Diagnostics MUST bound retry and probe activity.

DIAG-20

Diagnostics MUST protect emergency resources.

DIAG-21

Critical diagnostics SHOULD have independent verification.

DIAG-22

Diagnostic failure MUST itself be observable.

DIAG-23

Diagnostic systems MUST support false positives.

DIAG-24

Diagnostic systems MUST track false negatives when discovered.

DIAG-25

Recovery MUST be separately verified.

DIAG-26

Diagnostics MUST NOT directly grant authority.

DIAG-27

Diagnostics MUST NOT bypass Authorization.

DIAG-28

Diagnostic records MUST be preserved in Chronicle.

DIAG-29

Diagnostic replay MUST NOT create real-world side effects.

DIAG-30

Self-Diagnostics MUST continuously test whether its own observations remain trustworthy.

⸻

134. Reference Architecture

                         REAL WORLD
                             │
                             ▼
                    ┌─────────────────┐
                    │   Observations  │
                    └────────┬────────┘
                             │
                             ▼
                  ┌─────────────────────┐
                  │ Self-Diagnostics    │
                  │                     │
                  │ Health              │
                  │ Performance         │
                  │ Integrity           │
                  │ Capability          │
                  │ Dependency          │
                  │ Security            │
                  │ Behavior            │
                  │ Goal                │
                  │ Self Model          │
                  └─────────┬───────────┘
                            │
                            ▼
                   ┌─────────────────┐
                   │ Diagnostic      │
                   │ Finding         │
                   └────────┬────────┘
                            │
             ┌──────────────┼──────────────┐
             ▼              ▼              ▼
         Monitor         Recover        Escalate
             │              │              │
             │              ▼              │
             │        RFC-0027             │
             │              │              │
             └──────────────┼──────────────┘
                            ▼
                     Verification
                            │
                            ▼
                       World Update
                            │
                            ▼
                         Chronicle

⸻

135. Complete Diagnostic Loop

OBSERVE
  ↓
MEASURE
  ↓
COMPARE
  ↓
DETECT
  ↓
CLASSIFY
  ↓
DIAGNOSE
  ↓
VERIFY
  ↓
ASSESS IMPACT
  ↓
RECOVER / MONITOR / ESCALATE
  ↓
VERIFY RECOVERY
  ↓
UPDATE SELF MODEL
  ↓
CHRONICLE
  ↓
LEARN

⸻

136. Relationship With RFC-0033

RFC-0033:

What do I believe about myself?

RFC-0034:

Does evidence support that belief?

ดังนั้น:

RFC-0033 = Self Representation
RFC-0034 = Self Examination

⸻

137. Relationship With RFC-0026

Self-Diagnostics
        ↓
detect possible problem
        ↓
Verification Engine
        ↓
determine whether problem actually exists

Diagnostics หา “สิ่งผิดปกติ”

Verification ตัดสิน “หลักฐานยืนยันอะไรได้บ้าง”

⸻

138. Relationship With RFC-0027

Diagnostics
    ↓
Recovery Proposal
    ↓
RFC-0027
    ↓
Recovery
    ↓
Verification

Diagnostics ไม่ใช่ Recovery Engine

⸻

139. Relationship With RFC-0031

Event/Audit/Trace Fabric รับ:

diagnostic events
health changes
findings
incidents
recovery events
verification events

⸻

140. Relationship With RFC-0032

Chronicle เก็บ:

diagnostic history
finding history
incident timeline
recovery history
false positives
false negatives
system degradation

ทำให้ Veda สามารถตอบ:

When did this problem begin?
Has it happened before?
What caused it?
What fixed it?
Did the fix actually work?

⸻

141. Relationship With RFC-0035

RFC-0035 จะนำ:

diagnostic events
failures
near misses
prediction errors
recovery outcomes

มาสร้างเป็น:

Experience

⸻

142. Final Principle

Self-Diagnostics ต้องทำให้ Veda ไม่เพียงรู้ว่า:

"I think I am healthy."

แต่สามารถถาม:

"What evidence says I am healthy?"

และหาก evidence ไม่เพียงพอ:

"I don't know."

คือคำตอบที่ถูกต้อง

Architecture สุดท้ายคือ:

Self Model
    ↓
What I think I am
Self-Diagnostics
    ↓
What the evidence says about me
Verification
    ↓
What can actually be established
Chronicle
    ↓
What happened and what was recorded
Learning
    ↓
What I should change because of the experience

หลักสำคัญ:

A healthy Veda is not one that never detects failure.
A healthy Veda is one that can detect,
localize,
measure,
admit uncertainty,
contain,
recover,
verify,
and learn from failure.

⸻

End of RFC-0034