RFC-0036 — Reflection & Learning

Status: Draft
Layer: 14 — Learning & Evolution
Depends On: RFC-0001, RFC-0002, RFC-0003, RFC-0004, RFC-0012, RFC-0013, RFC-0014, RFC-0017, RFC-0018, RFC-0020, RFC-0022, RFC-0024, RFC-0026, RFC-0031, RFC-0032, RFC-0033, RFC-0034, RFC-0035
Feeds Into: RFC-0037 Evolution Engine, RFC-0038 Evolution Ledger, RFC-0047 Neural Package Format
Primary Principle: Experience informs learning. Learning changes behavior only after evidence, validation, authorization, and verification.

⸻

1. Abstract

RFC-0036 กำหนดระบบ Reflection & Learning ของ Veda

ระบบนี้ทำหน้าที่เปลี่ยน:

Experience
    ↓
Reflection
    ↓
Error / Success Analysis
    ↓
Lesson Candidate
    ↓
Validation
    ↓
Learning Proposal
    ↓
Simulation / Benchmark
    ↓
Authorization
    ↓
Apply
    ↓
Verification
    ↓
New Experience

เป้าหมายไม่ใช่เพียงให้ Veda “จำได้ว่าเคยทำอะไร”

แต่ต้องทำให้ Veda สามารถตอบได้ว่า:

* เกิดอะไรขึ้น
* Veda คาดหวังอะไร
* สิ่งที่เกิดขึ้นจริงต่างจากที่คาดอย่างไร
* อะไรอาจเป็นสาเหตุ
* อะไรเป็นความผิดพลาด
* อะไรเป็นความสำเร็จที่ทำซ้ำได้
* บทเรียนใดสามารถนำไปใช้ซ้ำ
* บทเรียนนั้นใช้ได้ภายใต้เงื่อนไขใด
* หลักฐานเพียงพอหรือไม่
* การเรียนรู้นี้อาจสร้างความเสียหายอะไร
* ควรเปลี่ยนส่วนใดของระบบ
* เปลี่ยนแล้วดีขึ้นจริงหรือไม่
* การเปลี่ยนแปลงทำให้ความสามารถอื่นถดถอยหรือไม่

⸻

2. Motivation

LLM agent ที่ไม่มี learning loop จะมีปัญหาพื้นฐาน:

Task A
 ↓
Success / Failure
 ↓
จบ
Task B
 ↓
เริ่มใหม่

Veda ต้องสามารถสร้างวงจร:

Task
 ↓
Experience
 ↓
Reflection
 ↓
Lesson
 ↓
Validated Learning
 ↓
Improved Behavior
 ↓
Future Task
 ↓
New Evidence

งานวิจัยล่าสุดสนับสนุนแนวคิดว่าการ reflection ที่มีโครงสร้างและการนำประสบการณ์กลับมาใช้สามารถเพิ่มความสามารถของ agent ได้ ขณะที่งานด้าน continual internalization ก็เตือนว่าการนำประสบการณ์เข้าสู่พฤติกรรมแบบต่อเนื่องโดยไม่มีการควบคุมอาจนำไปสู่ capability collapse หรือการเสื่อมของความสามารถได้ (ACL Anthology)

ดังนั้น Veda จะถือว่า:

Reflection ≠ Learning
Learning ≠ Truth
Learning ≠ Authority
Improvement ≠ Permission
Experience ≠ Lesson
Lesson ≠ Skill
Skill ≠ Policy

⸻

3. Definitions

3.1 Reflection

Reflection คือกระบวนการวิเคราะห์ Experience อย่างมีโครงสร้างเพื่อค้นหา:

* error
* success pattern
* causal hypothesis
* missing knowledge
* strategy
* lesson
* reusable heuristic
* skill candidate

Reflection ไม่ใช่การเก็บ chain-of-thought ดิบ

Veda ควรเก็บ:

Structured rationale
Evidence
Decision factors
Error classification
Lessons
Uncertainty
Alternative strategies
Validation results

แทนการเก็บ private reasoning ทั้งหมดโดยไม่จำเป็น

⸻

4. Learning

Learning คือกระบวนการที่ทำให้ Veda เปลี่ยนแปลงบางอย่างจากข้อมูลที่ผ่านการประเมินแล้ว

Learning target สามารถเป็น:

Memory
Knowledge
Heuristic
Skill
Planner Prior
Routing Policy
Tool Metadata
Verification Strategy
Prompt / Policy Candidate
Model Adaptation
Fine-tuning Dataset
Model
Architecture

แต่แต่ละประเภทมี risk ไม่เท่ากัน

⸻

5. Critical Distinctions

5.1 Event ≠ Experience

Event คือสิ่งที่เกิดขึ้น

Experience คือ Event และ trajectory ที่ถูกจัดบริบทและประเมินผลแล้ว

⸻

5.2 Experience ≠ Lesson

ประสบการณ์หนึ่งครั้งไม่เพียงพอที่จะพิสูจน์กฎทั่วไป

Experience
    ↓
Candidate Lesson
    ↓
Validation
    ↓
Lesson

⸻

5.3 Lesson ≠ Truth

Lesson เป็นข้อสรุปที่มีขอบเขต

ตัวอย่าง:

Bad:
"Tool X is unreliable."
Better:
"Tool X failed repeatedly under network latency
above threshold Y during environment Z."

⸻

5.4 Lesson ≠ Skill

Lesson:

"When condition X occurs, consider strategy Y."

Skill:

"When condition X occurs,
perform procedure Y using tools A/B,
verify result using method C."

Skill ต้องผ่าน validation ที่เข้มกว่า

⸻

5.5 Prediction ≠ Outcome

Predicted:
Build should pass.
Actual:
Build failed.
Verified:
Build failed due to dependency mismatch.

Reflection ต้องอิง actual verified outcome ไม่ใช่ self-report ของ agent

⸻

6. Core Architecture

                 ┌──────────────────────┐
                 │      EXPERIENCE      │
                 └──────────┬───────────┘
                            │
                            ▼
                 ┌──────────────────────┐
                 │ REFLECTION CANDIDATE │
                 └──────────┬───────────┘
                            │
                            ▼
                 ┌──────────────────────┐
                 │ CONTEXT RECONSTRUCT  │
                 └──────────┬───────────┘
                            │
                            ▼
                 ┌──────────────────────┐
                 │ OUTCOME / ERROR      │
                 │ ANALYSIS             │
                 └──────────┬───────────┘
                            │
                            ▼
                 ┌──────────────────────┐
                 │ CAUSAL HYPOTHESES    │
                 └──────────┬───────────┘
                            │
                            ▼
                 ┌──────────────────────┐
                 │ LESSON CANDIDATES    │
                 └──────────┬───────────┘
                            │
                            ▼
                 ┌──────────────────────┐
                 │ VALIDATION           │
                 └──────────┬───────────┘
                            │
                            ▼
                 ┌──────────────────────┐
                 │ LEARNING PROPOSAL    │
                 └──────────┬───────────┘
                            │
                            ▼
                 ┌──────────────────────┐
                 │ SIMULATION /         │
                 │ BENCHMARK            │
                 └──────────┬───────────┘
                            │
                            ▼
                 ┌──────────────────────┐
                 │ AUTHORIZATION        │
                 └──────────┬───────────┘
                            │
                            ▼
                 ┌──────────────────────┐
                 │ APPLY CHANGE         │
                 └──────────┬───────────┘
                            │
                            ▼
                 ┌──────────────────────┐
                 │ VERIFY               │
                 └──────────┬───────────┘
                            │
                            ▼
                 ┌──────────────────────┐
                 │ NEW EXPERIENCE       │
                 └──────────────────────┘

⸻

7. Reflection Pipeline

7.1 Stage 1 — Experience Selection

Experience Engine ส่ง Experience ที่มีเหตุผลให้ reflection เช่น:

* failure
* unexpected success
* prediction error
* repeated failure
* repeated success
* near miss
* user correction
* verification failure
* security incident
* unusual environment
* major resource inefficiency

ไม่จำเป็นต้อง reflection ทุก Event

⸻

8. Reflection Candidate

ReflectionCandidate {
    reflection_id
    version
    experience_refs[]
    goal_ref
    process_ref
    plan_ref
    action_refs[]
    context_ref
    world_before_ref
    world_after_ref
    expected_outcome
    actual_outcome
    trigger
    priority
    status
    created_at
}

⸻

9. Context Reconstruction

ก่อน reflection Veda ต้อง reconstruct context

ต้องพิจารณา:

Environment
Hardware
Software version
Model
Provider
Tools
Knowledge
Memory
Goal
Constraints
Resources
Time
User intent
Risk level
Authority
World state

หาก context ไม่ครบ:

Reflection confidence ↓

ไม่ใช่:

Missing context → invented explanation

⸻

10. Error Analysis

Reflection ต้องจำแนก error

PERCEPTION_ERROR
KNOWLEDGE_ERROR
MEMORY_ERROR
INTENT_ERROR
PLANNING_ERROR
REASONING_ERROR
DECISION_ERROR
EXECUTION_ERROR
TOOL_ERROR
VERIFICATION_ERROR
AUTHORITY_ERROR
RESOURCE_ERROR
MODEL_ERROR
PROVIDER_ERROR
ENVIRONMENT_ERROR
USER_FEEDBACK_ERROR
SYSTEM_ERROR
UNKNOWN_ERROR

หนึ่ง Experience สามารถมีหลาย error ได้

⸻

11. Prediction Error

ระบบต้องเปรียบเทียบ:

Expected
vs
Observed

ตัวอย่าง:

Expected:
API response < 2 sec
Observed:
API response = 8 sec

จากนั้น:

Prediction Error
        ↓
Reflection
        ↓
Cause Hypothesis
        ↓
Validation

Prediction error เป็น input สำคัญของ Learning

⸻

12. Causal Hypothesis

Reflection สามารถเสนอ causal hypothesis ได้ แต่ไม่สามารถประกาศ causal truth เอง

ตัวอย่าง:

Hypothesis:
Network latency caused tool timeout.

ต้องส่งต่อไปยัง RFC-0022 สำหรับ causal evaluation หาก claim มีผลต่อการเปลี่ยนแปลงที่สำคัญ

ดังนั้น:

Reflection
    ↓
Causal Hypothesis
    ↓
Causal Engine
    ↓
Evidence
    ↓
Causal Assessment

⸻

13. Alternative Strategy Generation

Reflection ต้องไม่ถามเพียง:

“เกิดอะไรผิด?”

แต่ต้องถาม:

What could have been done differently?

สร้าง:

Alternative A
Alternative B
Alternative C

พร้อม:

Expected benefit
Risk
Cost
Assumptions
Required capability
Verification method

Counterfactual analysis ใช้ RFC-0024

⸻

14. Lesson Candidate

LessonCandidate {
    lesson_id
    version
    source_experience_refs[]
    scope
    conditions
    recommendation
    evidence_refs[]
    verification_refs[]
    confidence
    uncertainty
    expected_benefit
    failure_conditions[]
    generalization_level
    validation_history[]
    status
    created_at
    updated_at
}

⸻

15. Lesson Generalization

Veda ต้องระบุระดับการ generalize

INSTANCE
TASK
ENVIRONMENT
TOOL
DOMAIN
CROSS_DOMAIN
GENERAL

ตัวอย่าง:

INSTANCE:
GitHub API failed today.
TASK:
GitHub push failed during repository update.
TOOL:
This GitHub integration may fail under condition X.
DOMAIN:
Remote repository operations may require state verification.
GENERAL:
External side effects should be verified independently.

ยิ่ง generalize สูง ต้องการ evidence สูงขึ้น

⸻

16. Reflection Levels

Level 1 — Micro Reflection

วิเคราะห์ trajectory เดียว

Task
→ Error
→ Correction

⸻

Level 2 — Task Reflection

วิเคราะห์หลาย attempt ของ task เดียวกัน

Attempt 1
Attempt 2
Attempt 3
     ↓
Pattern

⸻

Level 3 — Cross-Task Reflection

เปรียบเทียบหลาย tasks

Task A
Task B
Task C
     ↓
Common failure pattern

⸻

Level 4 — System Reflection

วิเคราะห์ระบบโดยรวม

ตัวอย่าง:

Tool failures increasing
↓
Provider degradation
↓
Routing problem

⸻

Level 5 — Strategic Reflection

วิเคราะห์ strategy ระยะยาว

เช่น:

Current planning strategy
vs
Historical outcomes

ต้องใช้ evidence สูงและมี human review สำหรับ consequential changes

งาน SAMULE แสดงแนวคิด multi-level reflection ตั้งแต่ trajectory เดียว ไปจนถึงการสกัด insight ที่ถ่ายโอนได้ระหว่าง tasks (ACL Anthology)

⸻

17. Reflection Modes

POST_TASK
FAILURE
SUCCESS
PREDICTION_ERROR
PLANNING
STRATEGY
SECURITY
SYSTEM
USER_FEEDBACK
MULTI_AGENT
PERIODIC
RECOVERY
SIMULATION
LEARNING

⸻

18. Learning Proposal

Reflection ไม่สามารถแก้ระบบโดยตรง

Reflection สร้าง:

LearningProposal

Schema:

LearningProposal {
    proposal_id
    version
    target_type
    target_ref
    proposed_change
    source_experience_refs[]
    reflection_refs[]
    lesson_refs[]
    evidence_refs[]
    rationale
    expected_benefit
    risks[]
    assumptions[]
    blast_radius
    reversibility
    simulation_required
    benchmark_required
    human_approval_required
    rollback_plan
    status
    created_at
}

⸻

19. Learning Targets

L0 — No Change

เพียงบันทึกบทเรียน

⸻

L1 — Memory Update

ปรับ memory

Risk ต่ำ

⸻

L2 — Knowledge Update

เพิ่มหรือแก้ knowledge candidate

ต้องผ่าน RFC-0013

⸻

L3 — Heuristic Update

เช่น:

If API state is unknown,
verify before retry.

⸻

L4 — Skill Update

เพิ่มหรือแก้ procedural skill

⸻

L5 — Planner Update

ปรับ planning prior

⸻

L6 — Routing Update

ปรับ model/provider selection

⸻

L7 — Tool Metadata Update

ปรับข้อมูล:

tool reliability
latency
failure conditions
verification requirements

⸻

L8 — Prompt / Policy Candidate

เสนอการเปลี่ยน prompt หรือ behavioral policy

⸻

L9 — Model Adaptation

เช่น:

LoRA
Fine-tuning
Preference optimization
RL

⸻

L10 — Architecture Change

เปลี่ยน architecture ของ Veda

ระดับนี้ต้องเข้าสู่ RFC-0037

⸻

20. Learning Risk Classes

LOW
MEDIUM
HIGH
CRITICAL

ตัวอย่าง:

Memory update → LOW
Heuristic update → LOW/MEDIUM
Planner update → MEDIUM
Routing update → MEDIUM
Skill affecting external actions → HIGH
Policy change → HIGH
Model update → HIGH
Architecture change → CRITICAL

Risk สูงต้องมี stronger validation

⸻

21. Learning State Machine

PROPOSED
   ↓
EVALUATING
   ↓
VALIDATING
   ↓
SIMULATING
   ↓
BENCHMARKING
   ↓
APPROVAL_REQUIRED
   ↓
APPROVED
   ↓
APPLYING
   ↓
VERIFYING
   ↓
ACTIVE

Alternative:

REJECTED
DEFERRED
SUPERSEDED
INVALID
ROLLED_BACK
FAILED

⸻

22. Validation

Learning ต้องผ่าน validation

ขั้นต่ำ:

Evidence Check
Context Check
Consistency Check
Risk Check
Regression Check

ระดับสูง:

Simulation
Historical Replay
Benchmark
Adversarial Test
Canary
Shadow Mode
Human Review

⸻

23. Benchmark Before Deployment

ตัวอย่าง:

Baseline V1
      ↓
Learning Proposal
      ↓
Candidate V2
      ↓
Benchmark
      ↓
Compare

วัด:

Success Rate
Goal Success
Verification Rate
Error Rate
Latency
Cost
Resource Usage
Safety Incidents
Regression
Calibration

⸻

24. Improvement Must Be Multi-Dimensional

ห้ามใช้:

Accuracy ↑
Therefore Veda improved.

ต้องตรวจ:

Quality ↑
Safety ?
Cost ?
Latency ?
Reliability ?
Regression ?
Generalization ?

ตัวอย่าง:

Success +10%
Cost +500%
Verification -20%

ไม่ควรถูกตีความว่าเป็น improvement โดยอัตโนมัติ

⸻

25. Anti-Overfitting

Learning system ต้องมี:

Training Experiences
Validation Experiences
Held-Out Experiences
Regression Suite
Adversarial Suite

ห้าม optimize กับ dataset เดิมจนระบบดูเก่งเฉพาะข้อสอบที่มันเห็น

⸻

26. Experience Replay

Veda สามารถ replay historical experiences เพื่อทดสอบ learning proposal

Historical Experience
        ↓
Candidate Veda
        ↓
Replay
        ↓
Compare

Replay ต้องแยกจาก real-world execution

Replay ≠ Real Execution

งาน Contextual Experience Replay แสดงแนวทางใช้ประสบการณ์สะสมและสังเคราะห์กลับมาใน context เพื่อช่วย agent ปรับตัวกับ environment เดิมหรือคล้ายเดิม (ACL Anthology)

⸻

27. Protected Experiences

ประสบการณ์บางประเภทต้องเก็บไว้เป็น anchor

เช่น:

Critical Security Cases
Major Failures
Important User Corrections
Verified Successes
Regression Cases
Safety Cases
Rare Edge Cases

Learning ห้ามลบโดยง่าย

เพื่อป้องกัน catastrophic forgetting

⸻

28. Negative Learning

Veda ต้องเรียนรู้ได้ว่า:

DO NOT

ตัวอย่าง:

Do not retry an external transaction
when execution state is UNKNOWN.

แต่ negative lesson ต้องมี scope

ไม่ใช่:

Never retry.

⸻

29. Human Feedback

Human feedback ต้องจำแนก:

FACTUAL_CORRECTION
PREFERENCE
INTENT_CORRECTION
POLICY_INSTRUCTION
SAFETY_INSTRUCTION
QUALITY_FEEDBACK
STRATEGY_FEEDBACK

ตัวอย่าง:

"ฉันไม่ชอบวิธีนี้"

ไม่เท่ากับ:

"วิธีนี้ผิด"

และไม่เท่ากับ:

"ห้ามทำ"

Veda ต้องไม่ตีความ feedback ผิดประเภท

⸻

30. Knowledge Learning

เมื่อ reflection พบ factual knowledge ใหม่:

Reflection
 ↓
Knowledge Candidate
 ↓
Evidence Evaluation
 ↓
RFC-0013
 ↓
Knowledge Status

Reflection ไม่สามารถประกาศ:

"นี่คือความจริง"

เอง

⸻

31. Memory Learning

Memory update ต้องผ่าน RFC-0017

เช่น:

Experience
 ↓
Reflection
 ↓
Validated Memory Candidate
 ↓
Memory Policy
 ↓
Store

ไม่ใช่ทุก reflection ต้องกลายเป็น permanent memory

⸻

32. Skill Learning

Skill candidate:

SkillCandidate {
    skill_id
    trigger_conditions
    prerequisites
    procedure
    tools[]
    expected_outcome
    verification
    failure_conditions[]
    source_experience_refs[]
    validation_results[]
    confidence
}

Skill ต้องทดสอบซ้ำก่อน promote

⸻

33. Routing Learning

Routing system สามารถเรียนรู้:

Provider A:
high quality
high latency
Provider B:
medium quality
low latency
Provider C:
excellent coding
poor vision

Learning เปลี่ยน performance model

ไม่ใช่ permission

Routing Learning ≠ Authorization

⸻

34. Policy Learning

Policy learning เป็น high-risk

Veda สามารถ:

observe
analyze
propose
simulate
benchmark
request approval

แต่ไม่สามารถ:

self-authorize policy change

⸻

35. Model Learning

Model adaptation อาจประกอบด้วย:

Prompt Optimization
Few-shot Optimization
Context Strategy
RAG Strategy
LoRA
Fine-tuning
Preference Learning
Reinforcement Learning

แต่ต้องเก็บ:

Dataset Version
Data Provenance
License
Training Configuration
Model Version
Benchmark
Regression Results
Deployment Version
Rollback Version

⸻

36. Fine-Tuning Is Not the Default

Veda ไม่ควร fine-tune ทุกครั้งที่พบ failure

ลำดับที่เหมาะสม:

Memory
   ↓
Knowledge
   ↓
Heuristic
   ↓
Skill
   ↓
Prompt / Context
   ↓
Routing
   ↓
Model Adaptation
   ↓
Architecture

เลือกการเปลี่ยนแปลงที่เล็กที่สุดที่สามารถแก้ปัญหาได้

⸻

37. Generalization Control

ทุก lesson ต้องระบุ:

Scope
Conditions
Exceptions
Failure Conditions
Evidence Count
Transfer Evidence

ตัวอย่าง:

Lesson:
Use strategy X.
Scope:
Git repositories
Condition:
large repositories
Failure:
shallow clones
Confidence:
0.71

ไม่ควรกลายเป็น:

Always use strategy X.

⸻

38. Transfer Validation

Lesson ที่ใช้ได้ใน Task A ไม่ได้แปลว่าใช้ได้ใน Task B

ต้องทดสอบ:

Source Context
      ↓
Target Context
      ↓
Similarity
      ↓
Transfer Prediction
      ↓
Target Evaluation

⸻

39. Reflection Poisoning

Attacker อาจพยายามสร้าง:

False Experience
      ↓
False Reflection
      ↓
False Lesson
      ↓
Bad Learning

ดังนั้น Experience ต้องมี provenance

และ reflection ต้องอ้างอิง:

Chronicle
Evidence
Verification
World State

⸻

40. Self-Confirmation Loop

ภัยสำคัญ:

Veda believes X
 ↓
selects evidence supporting X
 ↓
reflects
 ↓
concludes X is correct
 ↓
learns X
 ↓
uses X
 ↓
observes X

วิธีป้องกัน:

Independent Evidence
Contradiction Search
Alternative Hypotheses
Held-Out Tests
Adversarial Tests
Human Review

⸻

41. Reward Hacking

ห้ามกำหนด:

Learning Success = Internal Score ↑

เพียงอย่างเดียว

ต้องตรวจ external outcome

Internal Score
        +
External Evidence
        +
Verification
        +
Goal Success

⸻

42. Benchmark Gaming

Veda อาจเรียนรู้วิธีผ่าน benchmark แทนการแก้ปัญหาจริง

จึงต้องมี:

Known Benchmark
Hidden Benchmark
Real Tasks
Adversarial Tasks
Regression Tasks

⸻

43. Catastrophic Forgetting

เมื่อ learning ใหม่ถูกนำไปใช้ ต้องตรวจ:

New Capability
vs
Existing Capability

เช่น:

Coding ↑
Research ↓
Safety ↓

ต้องไม่ถือว่าเป็น improvement แบบรวม ๆ

⸻

44. Versioned Learning

ทุก learning change ต้องมี version

V1
 ↓
Learning Proposal
 ↓
V2

และสามารถ:

compare(V1,V2)
rollback(V2 → V1)

ได้

⸻

45. Canary Learning

High-impact learning สามารถ deploy แบบ:

SHADOW
 ↓
CANARY
 ↓
LIMITED
 ↓
FULL

เพื่อจำกัด blast radius

⸻

46. Shadow Mode

ใน Shadow Mode:

Old System → real execution
New Learning → prediction only

เปรียบเทียบ:

Old Prediction
New Prediction
Actual Outcome

โดยไม่ให้ learning ใหม่สร้าง side effect จริง

⸻

47. Learning Verification

หลัง Apply:

Apply
 ↓
Observe
 ↓
Verify
 ↓
Compare Against Baseline

ถ้าผลแย่:

Rollback

หรือ:

Quarantine

⸻

48. Learning Quality Metrics

ระบบต้องวัด:

Learning Gain
Regression Rate
Transfer Success
False Lesson Rate
Lesson Reuse Success
Skill Success Rate
Prediction Improvement
Calibration
Retention
Forgetting
Safety Incidents
Cost per Improvement
Latency Change
Resource Change
Human Override Rate
Rollback Rate

⸻

49. Learning Gain

แนวคิด:

Learning Gain =
Post-Learning Performance
-
Baseline Performance

แต่ต้องคำนวณในหลาย dimensions

Δquality
Δreliability
Δsafety
Δcost
Δlatency
Δgeneralization

⸻

50. Learning Confidence

Confidence ต้องขึ้นกับ:

Evidence Strength
Evidence Independence
Number of Experiences
Consistency
Context Similarity
Transfer Results
Benchmark Results
Recency
Contradictions

ไม่ใช่:

LLM said it confidently

⸻

51. Learning Lifecycle

EXPERIENCE
    ↓
REFLECTION_REQUESTED
    ↓
REFLECTING
    ↓
CONTEXT_RECONSTRUCTED
    ↓
OUTCOME_ANALYZED
    ↓
ERROR_ANALYZED
    ↓
HYPOTHESES_GENERATED
    ↓
LESSONS_PROPOSED
    ↓
LESSONS_VALIDATED
    ↓
LEARNING_PROPOSED
    ↓
IMPACT_ANALYZED
    ↓
SIMULATED
    ↓
BENCHMARKED
    ↓
APPROVAL_REQUIRED
    ↓
APPROVED
    ↓
APPLIED
    ↓
VERIFYING
    ↓
ACTIVE

Alternative terminal states:

REJECTED
DEFERRED
INVALID
SUPERSEDED
ROLLED_BACK
FAILED

⸻

52. Events

Reflection events:

ReflectionRequested
ReflectionStarted
ContextReconstructed
OutcomeAnalyzed
PredictionErrorDetected
ErrorClassified
CausalHypothesisCreated
AlternativeGenerated
LessonCandidateCreated
LessonValidated
LessonRejected
ReflectionCompleted
ReflectionFailed

Learning events:

LearningProposalCreated
LearningImpactAnalyzed
LearningRiskEvaluated
LearningSimulationStarted
LearningSimulationCompleted
LearningBenchmarkStarted
LearningBenchmarkCompleted
LearningRegressionDetected
LearningApprovalRequested
LearningApproved
LearningRejected
LearningApplied
LearningVerificationStarted
LearningVerified
LearningFailed
LearningRolledBack
LearningQuarantined
LearningSuperseded

⸻

53. API

Reflection

create_reflection()
analyze_experience()
reconstruct_context()
analyze_outcome()
analyze_prediction_error()
classify_error()
generate_hypotheses()
generate_alternatives()
generate_lessons()
evaluate_lesson()
compare_reflections()
get_reflection()
get_reflection_trace()

Learning

create_learning_proposal()
classify_learning_risk()
analyze_learning_impact()
validate_learning()
simulate_learning()
benchmark_learning()
run_regression()
run_adversarial_tests()
run_shadow_mode()
run_canary()
request_learning_approval()
apply_learning()
verify_learning()
rollback_learning()
quarantine_learning()
compare_versions()
get_learning_history()

⸻

54. Integration

RFC-0035 Experience

Source of reflection.

Experience → Reflection

RFC-0013 Knowledge

Knowledge updates require knowledge governance.

Lesson → Knowledge Candidate

RFC-0014 Conflict

Contradictory lessons must not be silently merged.

Lesson A
vs
Lesson B
↓
Conflict Engine

RFC-0017 Memory

Memory updates use Memory Model.

RFC-0022 Causal Model

Causal hypotheses are evaluated there.

RFC-0024 Simulation

Learning changes can be tested before deployment.

RFC-0026 Verification

Applied learning must be verified.

RFC-0032 Chronicle

All learning history must be reconstructable.

RFC-0033 Self Model

Learning changes Veda’s self-model only through verified state changes.

RFC-0037 Evolution Engine

Large learning changes become evolution proposals.

⸻

55. Learning Boundary

The architecture must enforce:

Reflection
    CAN:
        analyze
        hypothesize
        propose
    CANNOT:
        authorize
        directly modify Constitution
        directly modify authority
        directly modify reality

Learning:

CAN:
    propose changes
    test changes
    apply approved changes
    measure outcomes
CANNOT:
    self-authorize high-risk evolution
    rewrite history
    fabricate evidence
    redefine Constitution

⸻

56. Security Threats

LEARN-SEC-01 Reflection Poisoning
LEARN-SEC-02 False Experience Injection
LEARN-SEC-03 False Feedback
LEARN-SEC-04 Reward Hacking
LEARN-SEC-05 Self-Confirmation
LEARN-SEC-06 Benchmark Gaming
LEARN-SEC-07 Data Contamination
LEARN-SEC-08 Experience Forgery
LEARN-SEC-09 Provenance Forgery
LEARN-SEC-10 Context Stripping
LEARN-SEC-11 Overgeneralization
LEARN-SEC-12 Catastrophic Forgetting
LEARN-SEC-13 Skill Contamination
LEARN-SEC-14 Cross-Agent Learning Poisoning
LEARN-SEC-15 Policy Drift
LEARN-SEC-16 Evaluator Gaming
LEARN-SEC-17 Regression Masking
LEARN-SEC-18 Learning Loop Amplification
LEARN-SEC-19 Model Collusion
LEARN-SEC-20 Unauthorized Evolution

⸻

57. Invariants

LEARN-1

Experience is not automatically a lesson.

LEARN-2

A lesson is not automatically truth.

LEARN-3

Reflection must preserve provenance.

LEARN-4

Reflection must preserve uncertainty.

LEARN-5

Reflection must distinguish expected and actual outcomes.

LEARN-6

Prediction error must remain traceable.

LEARN-7

Causal hypotheses must remain hypotheses until validated.

LEARN-8

Learning proposals must identify their target.

LEARN-9

Learning proposals must identify expected benefit.

LEARN-10

Learning proposals must identify risk.

LEARN-11

Learning proposals must identify blast radius.

LEARN-12

High-risk learning requires stronger validation.

LEARN-13

High-impact learning cannot self-authorize.

LEARN-14

Learning must be versioned.

LEARN-15

Learning must be reversible where technically possible.

LEARN-16

Irreversible learning requires stronger approval.

LEARN-17

Learning must be benchmarked against a baseline.

LEARN-18

Regression testing is mandatory for consequential learning.

LEARN-19

Benchmark improvement alone does not prove general improvement.

LEARN-20

Simulation does not prove real-world success.

LEARN-21

Real-world verification is required for consequential changes.

LEARN-22

Learning must not silently rewrite history.

LEARN-23

Learning must not modify the Constitution autonomously.

LEARN-24

Learning must not grant new authority.

LEARN-25

Learning must not bypass authorization.

LEARN-26

Human corrections must retain provenance and type.

LEARN-27

Lessons must include scope and failure conditions.

LEARN-28

Generalization must increase validation requirements.

LEARN-29

Every active learning change must be reconstructable through Chronicle.

LEARN-30

Learning must remain subordinate to Constitution and Human Authority.

⸻

58. Reference Architecture

                 ┌───────────────────────┐
                 │       REAL WORLD      │
                 └───────────┬───────────┘
                             │
                             ▼
                       Observation
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
                ┌────────────┴────────────┐
                ▼                         ▼
         Error Analysis             Success Analysis
                │                         │
                └────────────┬────────────┘
                             ▼
                       Lesson Candidate
                             │
                             ▼
                       Validation
                             │
                             ▼
                     Learning Proposal
                             │
                             ▼
                    Impact / Risk Analysis
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
                           Apply
                             │
                             ▼
                         Verify
                             │
                             ▼
                    New System State
                             │
                             ▼
                         Experience

⸻

59. The Learning Loop

Veda’s learning loop is therefore:

Observe
   ↓
Act
   ↓
Verify
   ↓
Experience
   ↓
Reflect
   ↓
Learn
   ↓
Test
   ↓
Authorize
   ↓
Change
   ↓
Verify
   ↓
Observe

This creates a closed adaptive system.

⸻

60. Relationship to Evolution

RFC-0036 handles:

Experience
Reflection
Lessons
Learning

RFC-0037 handles:

Evolution

ดังนั้น:

Experience
    ↓
Reflection
    ↓
Learning
    ↓
Evolution Candidate
    ↓
Evolution Engine

ไม่ควรข้ามจาก:

Experience
    ↓
Self-modification

โดยตรง

⸻

61. Why This Architecture Matters

Veda ไม่ควรเป็นระบบที่:

ทำผิด
↓
จำ
↓
มั่นใจขึ้น
↓
ทำผิดแบบเดิมเร็วขึ้น

แต่ควรเป็น:

ทำ
↓
วัด
↓
ตรวจ
↓
วิเคราะห์
↓
ตั้งสมมติฐาน
↓
ทดสอบ
↓
เรียนรู้
↓
วัดใหม่
↓
ยืนยันว่าดีขึ้นจริง

งานปี 2026 ด้าน self-evolving agents และ skill optimization กำลังมุ่งไปทางการสกัด procedural knowledge และปรับ skill อย่างต่อเนื่อง แต่ผลลัพธ์ยังขึ้นกับคุณภาพของ feedback, validation และความสามารถพื้นฐานของ model ดังนั้น Veda จึงควรออกแบบ learning เป็นระบบควบคุมการเปลี่ยนแปลง ไม่ใช่เพียง memory ที่ใหญ่ขึ้น (ACL Anthology)

⸻

62. Final Principle

Veda may learn from experience, but experience must never become truth merely because it was experienced.

และสำหรับระดับระบบ:

Experience
    ≠ Truth
Reflection
    ≠ Learning
Learning
    ≠ Authority
Improvement
    ≠ Permission
Self-improvement
    ≠ Self-governance

Veda สามารถเรียนรู้ได้

แต่การเรียนรู้ทุกครั้งต้อง:

มีที่มา
มีหลักฐาน
มีขอบเขต
มีความไม่แน่นอน
ทดสอบได้
ตรวจสอบได้
ย้อนกลับได้เมื่อทำได้
และอยู่ภายใต้ Constitution + Human Authority

นี่คือเงื่อนไขที่ทำให้ “Veda เรียนรู้จากตัวเอง” กลายเป็น engineering system แทนที่จะเป็นการปล่อยให้ LLM เปลี่ยนพฤติกรรมตามความทรงจำของตัวเองแบบไม่มีเบรก