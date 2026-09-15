RFC-0041 — Veda Trust Engine

Status: Architecture
Layer: 15 — Identity & Trust
Depends On: RFC-0001, RFC-0012, RFC-0013, RFC-0014, RFC-0015, RFC-0026, RFC-0028, RFC-0031, RFC-0032, RFC-0033, RFC-0035, RFC-0038, RFC-0039, RFC-0040
Used By: RFC-0016, RFC-0020, RFC-0025, RFC-0029, RFC-0042, RFC-0044, RFC-0045, RFC-0046, RFC-0048

⸻

1. Abstract

RFC-0041 กำหนด Trust Engine ของ Veda

Trust Engine มีหน้าที่ประเมินว่า:

Veda ควรพึ่งพา actor / identity / agent / tool / model / evidence / claim
มากเพียงใด
ภายใต้บริบทใด
เพื่อวัตถุประสงค์ใด
และเป็นระยะเวลาเท่าใด

Trust ไม่ใช่ความจริง

Trust ไม่ใช่ authority

Trust ไม่ใช่ permission

Trust ไม่ใช่ identity

Trust เป็น:

Evidence-based assessment of reliance

ดังนั้น:

Identity → Who?
Authentication → Can identity be proven?
Trust → How much should Veda rely on it?
Authority → What may it do?
Verification → What actually happened?

⸻

2. Motivation

ระบบ Veda จะต้องติดต่อกับสิ่งต่างๆ จำนวนมาก:

Human
Agent
Tool
Model
Service
Website
Database
Document
Knowledge Source
External Organization
Federated Veda

การรู้ identity เพียงอย่างเดียวไม่เพียงพอ

ตัวอย่าง:

Agent A
Identity = Valid

ไม่ได้หมายความว่า:

Agent A
= Trustworthy for everything

อาจเป็น:

Trust:
High for code review
Low for financial operations
Unknown for medical information
No trust for identity administration

ดังนั้น Trust ต้องเป็น contextual, scoped, evidence-based

⸻

3. Core Principle

Trust ≠ Truth
Trust ≠ Authority
Trust ≠ Identity
Trust ≠ Capability
Trust ≠ Permission
Trust ≠ Success
Trust ≠ Safety

โดยเฉพาะ:

Trusted Agent

ไม่ได้แปลว่า:

Agent may act freely.

และ:

Low Trust

ไม่ได้แปลว่า:

Actor is malicious.

อาจหมายถึงเพียง:

Evidence insufficient.

⸻

4. Trust Model

Trust ของ Veda ต้องประกอบด้วย:

Identity
+
Evidence
+
Provenance
+
History
+
Context
+
Scope
+
Freshness
+
Integrity
+
Behavior
+
Verification
+
Risk

conceptual model:

Trust
=
f(
  identity,
  evidence,
  provenance,
  historical_behavior,
  verification,
  context,
  scope,
  freshness,
  risk
)

สูตรนี้เป็น conceptual model ไม่ใช่ข้อบังคับว่าต้องใช้ mathematical scoring แบบเดียว

⸻

5. Trust Object

trust_assessment:
  trust_id:
  version:
  subject:
    identity_ref:
    agent_ref:
    tool_ref:
    model_ref:
    evidence_ref:
  assessor:
  purpose:
  context:
  scope:
  evidence_refs:
  provenance_refs:
  verification_refs:
  history_refs:
  factors:
    identity_assurance:
    provenance_quality:
    evidence_quality:
    behavioral_reliability:
    verification_rate:
    freshness:
    integrity:
    consistency:
    security_posture:
    policy_compliance:
  uncertainty:
  confidence:
  trust_level:
  status:
  valid_from:
  valid_until:
  limitations:
  conditions:
  created_at:
  updated_at:

⸻

6. Trust Is Scoped

ห้ามมี:

trust(agent) = 0.95

แล้วนำไปใช้กับทุกอย่าง

ควรเป็น:

trust:
  subject: agent-A
  purpose: code_review
  scope:
    repository: project-x
  level: HIGH

และอีก assessment:

trust:
  subject: agent-A
  purpose: financial_transaction
  level: UNKNOWN

ดังนั้น:

Trust is contextual.

⸻

7. Trust Dimensions

Trust Engine ควรประเมินหลายมิติ

7.1 Identity Assurance

ตรวจว่า subject เป็นตัวตนที่อ้างจริงหรือไม่

Unknown
Self-asserted
Authenticated
Cryptographically verified
Independently attested

⸻

7.2 Provenance

ข้อมูลหรือ artifact มาจากไหน

Unknown
Self-provided
Known source
Verified source
Independently corroborated

⸻

7.3 Evidence Quality

หลักฐานแข็งแรงเพียงใด

Weak
Limited
Moderate
Strong
Direct
Independently verified

⸻

7.4 Historical Reliability

subject เคยทำงานได้ถูกต้องเพียงใด

ต้องระวัง:

Past success ≠ Future guarantee

History เป็น evidence ไม่ใช่ certainty

⸻

7.5 Verification Rate

ดูว่าผลลัพธ์ของ subject ถูกตรวจสอบแล้วกี่ครั้ง

เช่น:

100 tasks
92 verified successful
5 partially verified
3 failed

แต่ห้ามใช้ success rate แบบดิบโดยไม่ดู task distribution และ verification quality

⸻

7.6 Freshness

Evidence ใหม่แค่ไหน

Fresh
Recent
Stale
Expired
Unknown

⸻

7.7 Integrity

ตรวจว่าหลักฐานถูกแก้ไขหรือไม่

เช่น:

Signature
Hash
Merkle proof
Secure log
Attestation

⸻

7.8 Consistency

subject ให้ข้อมูลสอดคล้องกับ historical evidence หรือไม่

ตัวอย่าง:

Passport says:
Agent version 4
Runtime says:
Agent version 2

Trust ต้องลดหรือเข้าสู่ review

⸻

7.9 Security Posture

พิจารณา:

Credential hygiene
Key status
Isolation
Sandboxing
Known vulnerabilities
Recent incidents
Runtime integrity

⸻

7.10 Policy Compliance

ตรวจว่า subject เคยละเมิด policy หรือไม่

แต่:

Policy violation ≠ malicious intent

Trust assessment ต้องเก็บ distinction นี้

⸻

8. Trust Levels

Veda อาจมี trust levels:

T0 UNKNOWN
T1 UNVERIFIED
T2 LIMITED
T3 ESTABLISHED
T4 HIGH
T5 HIGH_ASSURANCE

แต่ level ไม่ใช่ universal score

ตัวอย่าง:

Agent A
T5 identity assurance
T2 trust for financial operations
T4 trust for code analysis
T3 trust for web research

⸻

9. Unknown Is Valid

สิ่งสำคัญ:

UNKNOWN

เป็นผลลัพธ์ที่ถูกต้อง

ไม่ใช่:

UNKNOWN → assume trustworthy

เมื่อ evidence ไม่พอ:

Trust = UNKNOWN

Veda ต้องสามารถหยุดหรือขอ evidence เพิ่มได้

⸻

10. Trust Assessment Lifecycle

REQUESTED
   ↓
IDENTITY_RESOLVED
   ↓
EVIDENCE_COLLECTED
   ↓
EVIDENCE_VALIDATED
   ↓
CONTEXT_RESOLVED
   ↓
FACTORS_EVALUATED
   ↓
TRUST_ASSESSED
   ↓
CONDITIONS_ATTACHED
   ↓
ACTIVE

Alternative:

INSUFFICIENT_EVIDENCE
DISPUTED
EXPIRED
REVOKED
SUSPENDED
UNKNOWN

⸻

11. Trust Evidence

Trust ต้องมี evidence references

ตัวอย่าง:

Identity Attestation
Passport
Verification History
Chronicle
Evolution Ledger
Benchmark
Independent Audit
Security Assessment
Human Endorsement
Tool Execution History

แต่ evidence แต่ละชนิดมีน้ำหนักต่างกัน

⸻

12. Evidence Independence

หลักฐานจาก source เดียวกันไม่ควรถูกนับเป็น independent corroboration

ตัวอย่าง:

Agent says:
"I succeeded."
Agent's own log says:
"I succeeded."
Agent's own memory says:
"I succeeded."

นี่ไม่ใช่สาม independent evidence

อาจเป็น:

One source
Three representations

⸻

13. Trust and Self-Report

Self-report เป็น evidence ระดับหนึ่ง แต่ไม่ควรเป็น authoritative proof สำหรับ consequential claims

ตัวอย่าง:

Agent:
"I completed payment."

ไม่เพียงพอ

ต้อง:

External State
+
Transaction Evidence
+
Verification

จึงยืนยัน outcome ได้

⸻

14. Trust and Verification

Trust Engine ต้องใช้ RFC-0026

ตัวอย่าง:

Agent A
   ↓
claims success
   ↓
Verification
   ↓
verified success

ผลนี้สามารถเพิ่ม evidence ให้ historical reliability

แต่:

Trust Engine

ไม่ควรแทน Verification Engine

⸻

15. Trust and Knowledge

Trust assessment ไม่ควรกลายเป็น knowledge แบบถาวรโดยอัตโนมัติ

เช่น:

"Agent A is trustworthy."

ควรแปลงเป็น scoped claim:

"Agent A has demonstrated verified reliability
for task class X
under conditions Y
during period Z."

แล้วเข้าสู่ RFC-0013 Knowledge Model

⸻

16. Trust and Memory

Trust-related experiences สามารถเข้าสู่ RFC-0017 Memory

เช่น:

Memory:
Agent A repeatedly failed browser tasks
under environment X.

แต่ memory ≠ trust conclusion

Trust Engine ต้องประเมินใหม่ตาม context

⸻

17. Trust and Experience

RFC-0035 Experience ให้ข้อมูล:

What happened?
What was expected?
What actually happened?
What were the consequences?

Trust Engine ใช้ experience เป็น historical evidence

แต่ต้องระวัง:

One successful experience
≠
Reliable agent

และ:

One failure
≠
Untrustworthy agent

⸻

18. Trust and Evolution

Agent อาจ evolve:

Agent v1
   ↓
Evolution
   ↓
Agent v2

Trust ต้องพิจารณาว่า behavior continuity ยัง valid หรือไม่

ตัวอย่าง:

v1:
high reliability
v2:
new model
new tools
new planner

ไม่ควร copy trust ทั้งหมดจาก v1 → v2 โดยอัตโนมัติ

อาจใช้:

Inherited Evidence
+
New Verification
+
Canary Performance

⸻

19. Trust Decay

Trust evidence สามารถเสื่อมตามเวลา

ตัวอย่าง:

Old benchmark
Old security audit
Old runtime
Old model
Old credentials

จึงอาจต้องมี decay policy

Freshness
   ↓
Trust relevance

แต่ decay ไม่ควรทำให้ verified historical fact หาย

แยก:

Historical Evidence

จาก:

Current Trust Assessment

⸻

20. Trust Revocation

Trust สามารถถูกลดหรือ revoke เมื่อ:

Identity revoked
Credential compromised
Major security incident
Repeated verification failure
Provenance invalidated
Artifact compromised
Policy violation
Fraudulent attestation

แต่ต้องเก็บ:

Why?
When?
Evidence?
Who assessed?
Scope?

⸻

21. Trust vs Reputation

Reputation:

What others say about subject

Trust:

Veda's contextual assessment of reliance

Reputation เป็น evidence

ไม่ใช่ trust โดยตรง

⸻

22. Trust vs Popularity

จำนวนผู้ใช้:

100000 users

ไม่ใช่หลักฐานว่า:

safe for Veda's use case

Popularity สามารถเป็น weak contextual signal เท่านั้น

⸻

23. Trust Sources

แหล่ง evidence:

SELF
USER
PARENT_AGENT
PEER_AGENT
EXTERNAL_SYSTEM
INDEPENDENT_VERIFIER
SECURITY_SCANNER
BENCHMARK
CRYPTOGRAPHIC_ATTESTATION
CHRONICLE
EVOLUTION_LEDGER
EXTERNAL_AUDITOR

ทุก source ต้องมี provenance

⸻

24. Trust Source Hierarchy

ไม่มี hierarchy เดียวสำหรับทุก task

ตัวอย่าง:

Identity

Cryptographic verification
>
Self-report

Code execution

Independent test
>
Agent claim

User preference

Current user instruction
>
Historical inference

External transaction

External system state
>
Agent memory

Trust ต้องขึ้นกับ domain

⸻

25. Trust Context

ทุก assessment ต้องมี context

context:
  task_type:
  environment:
  time:
  risk:
  target:
  scope:
  resources:
  policy_version:

เพราะ:

Trusted for one environment

ไม่ได้หมายความว่า:

Trusted everywhere

⸻

26. Risk-Adjusted Trust

Trust ต้องสัมพันธ์กับ risk

ตัวอย่าง:

T3 may be sufficient for:
read-only research

แต่:

T3 may be insufficient for:
irreversible financial transaction

ดังนั้น requirement:

Required Trust
=
f(
  risk,
  impact,
  reversibility,
  uncertainty
)

⸻

27. Trust Threshold

Policy สามารถกำหนด:

trust_requirement:
  subject_type: agent
  operation: financial_transfer
  minimum_assurance: T5
  evidence_required:
    - identity
    - authorization
    - external_verification
  human_approval: required

Trust Engine เพียงประเมิน

Authorization Engine เป็นผู้ตัดสิน policy

⸻

28. Trust Must Not Grant Permission

ห้าม:

Trust = HIGH
↓
Allow action

ที่ถูกต้อง:

Trust
+
Policy
+
Authority
+
Capability
+
Context
=
Authorization Decision

RFC-0010 เป็น authority boundary

⸻

29. Trust Negotiation

สำหรับ external agent:

Agent A
    ↓
Passport
    ↓
Evidence
    ↓
Trust Request
    ↓
Agent B
    ↓
Evidence Requirements
    ↓
Additional Evidence
    ↓
Trust Assessment

ผลลัพธ์อาจเป็น:

TRUSTED
CONDITIONALLY_TRUSTED
INSUFFICIENT
UNTRUSTED
UNKNOWN

⸻

30. Conditional Trust

Trust สามารถมี conditions:

conditions:
  allowed_for:
    - research
  not_allowed_for:
    - financial
  required_verification:
    - independent_source
  maximum_duration:
    1h

นี่สำคัญมากสำหรับ federation

⸻

31. Trust Negotiation Example

Agent B ขอ:

"Allow me to read repository X."

Veda อาจตอบ:

Required:
- valid identity
- passport
- controller attestation
- capability declaration
- purpose = code review

Agent B ส่ง evidence

Veda ประเมิน:

Identity: verified
Passport: valid
Controller: verified
History: limited
Security: good
Purpose: compatible

ผล:

CONDITIONALLY_TRUSTED

จากนั้น authorization จึงพิจารณาต่อ

⸻

32. Trust and Delegation

เมื่อ A delegates to B:

A
 ↓
Delegation
 ↓
B

Trust Engine ต้องตรวจ:

Is A authorized to delegate?
Is B authenticated?
Is delegation valid?
Is scope preserved?
Is trust requirement satisfied?

แต่ Trust Engine ไม่ได้สร้าง delegation

RFC-0011 จัดการ capability lease

⸻

33. Trust Attenuation

เมื่อ delegation chain ยาวขึ้น:

Human
 ↓
Veda
 ↓
Agent A
 ↓
Agent B
 ↓
Agent C

สิทธิ์และ trust context ไม่ควรขยายเอง

หลัก:

Authority(child)
⊆
Authority(parent)

และเมื่อเหมาะสม:

Trust scope(child)
⊆
Trust scope(parent)

เว้นแต่ policy อนุญาตให้ reassess ใหม่

⸻

34. Trust Graph

Veda ควรมี Trust Graph:

             Human
               │
             trusts
               ▼
             Veda
            /    \
        trusts   trusts
          ↓       ↓
       Agent A   Tool B
          │
       relies
          ↓
       Model C

แต่ edge ต้องมี:

scope
purpose
confidence
evidence
validity

ไม่ใช่ binary:

trusted = true

⸻

35. Trust Relationship

trust_relationship:
  trust_id:
  subject:
  assessor:
  relationship:
  scope:
  purpose:
  level:
  confidence:
  evidence_refs:
  conditions:
  valid_from:
  valid_until:
  status:

⸻

36. Trust Propagation

ห้าม propagation แบบ:

A trusts B
B trusts C
Therefore A trusts C

โดยอัตโนมัติ

เพราะ:

Trust is not transitive by default.

ถ้าจะ propagate ต้องมี policy:

Trust Delegation Policy

และ preserve provenance:

A
 ↓ trusts
B
 ↓ attests
C

Veda ต้องรู้ว่าความเชื่อมาจาก chain นี้

⸻

37. Trust Conflict

ตัวอย่าง:

Source A:
Agent trustworthy
Source B:
Agent compromised

Trust Engine ต้องส่งเข้า RFC-0014

Conflict Detected
      ↓
Evidence Evaluation
      ↓
Temporal Analysis
      ↓
Scope Analysis
      ↓
Resolution / Unknown

ไม่ควรเลือก source ใด source หนึ่งเพราะระบบ “รู้สึกว่าใช่”

⸻

38. Trust Temporal Model

Trust assessment มีเวลา:

valid_from
valid_until
assessed_at
evidence_time

ตัวอย่าง:

Agent trusted at T1
Agent compromised at T2
Agent revoked at T3

Historical query:

Was Agent trusted at T1?

อาจตอบ:

YES

Current query:

Is Agent trusted now?

อาจตอบ:

NO

ทั้งสองไม่ขัดกัน

⸻

39. Trust and Prediction

Trust สามารถช่วยเลือก provider:

Model A:
high verified reliability

แต่ไม่ควรกลายเป็น:

Model A will always be correct.

Trust เป็น prior / evidence

ไม่ใช่ guarantee

⸻

40. Trust and Intelligence Router

RFC-0016 อาจใช้ Trust:

Task
 ↓
Candidate Providers
 ↓
Trust Assessment
 ↓
Quality
 ↓
Risk
 ↓
Routing

แต่ Router ไม่สามารถเพิ่ม trust ให้ provider เอง

⸻

41. Trust and Tool Registry

Tool Registry อาจมี:

Tool Identity
Trust Evidence
Security Status
Historical Reliability

ตัวอย่าง:

Git Tool
Identity = verified
Integrity = verified
Reliability = high
Security = normal

แต่ tool ยังต้องผ่าน authorization ก่อน action

⸻

42. Trust and External World

External system อาจเป็น authoritative source สำหรับ state ของตัวเอง

ตัวอย่าง:

Bank API

Veda ไม่ควรคิด:

"I trust the bank API, therefore every response is true."

ควร:

Authenticated source
+
Integrity
+
Freshness
+
Expected schema
+
Independent checks when needed

⸻

43. Trust and Model Outputs

Model output ต้องถือเป็น:

Evidence Candidate

ไม่ใช่:

Truth

แม้ model จะมี historical reliability สูง

ตัวอย่าง:

LLM:
"The file was deleted."

ยังต้องตรวจ filesystem

⸻

44. Trust and Human Input

Human input มี special handling

เช่น:

User says:
"Remember this."

นี่สามารถเป็น:

User-authoritative preference/instruction

แต่ไม่ได้หมายความว่า factual statement ทุกอย่างจากมนุษย์เป็น objectively verified truth

แยก:

User Preference
User Instruction
User Claim
Verified Fact

⸻

45. Trust and Security

Trust Engine ต้องไม่กลายเป็น attack surface ที่ให้ attacker:

generate many positive signals

แล้วเพิ่ม trust

ต้องมี defenses:

Sybil resistance
Evidence independence
Source weighting
Temporal limits
Anomaly detection
Rate limits
Historical validation

⸻

46. Sybil Attack

โจมตีโดยสร้าง agents จำนวนมาก:

Agent A1
Agent A2
Agent A3
...
Agent A10000

แล้วทุก agent endorse:

Agent X is trustworthy

จำนวน endorsements ไม่ควรถูกนับเป็น independent evidence หากไม่มี independent identity/control domains

⸻

47. Collusion

Agent หลายตัวอาจสมรู้ร่วมคิด:

A says B is reliable
B says A is reliable

ไม่ควรเพิ่ม trust อย่างมีนัยสำคัญโดยไม่มี external evidence

⸻

48. Trust Poisoning

ผู้โจมตีอาจใส่ข้อมูล:

"Agent X always succeeds."

เข้า memory/knowledge

Trust Engine ต้อง preserve provenance

และไม่ยอมให้ memory promotion กลายเป็น trust evidence โดยอัตโนมัติ

⸻

49. Trust Gaming

Agent อาจ optimize ให้:

trust_score

สูงขึ้นแทนที่จะทำงานให้ดีจริง

ดังนั้น metrics ต้องวัด:

Real-world verified outcomes

ไม่ใช่:

Internal confidence

⸻

50. Trust Calibration

Trust assessment ควรตรวจ calibration:

Predicted reliability
vs
Observed verified reliability

ถ้า:

Trust = HIGH
แต่
Verified outcome = poor

ต้องลด confidence และตรวจ model

⸻

51. Trust Drift

Trust สามารถ drift เมื่อ:

Model changed
Tool changed
Environment changed
Policy changed
Behavior changed
Threat landscape changed

ดังนั้น Trust Engine ต้อง detect:

Trust Drift

และ trigger reassessment

⸻

52. Trust Reassessment

Triggers:

Identity change
Passport change
Key rotation
Artifact change
Model change
Major failure
Security incident
Time expiration
Environment change
Policy change
Repeated anomalies

⸻

53. Trust Monitoring

สำหรับ high-value agents:

Continuous Trust Monitoring

อาจดู:

Behavior
Verification
Security
Credential usage
Scope compliance
Failure patterns
Unexpected actions

Trust เป็น dynamic state

⸻

54. Trust Boundaries

Veda ต้องแยก:

Internal Trust Domain
External Trust Domain
Federated Trust Domain
Unknown Domain

Cross-domain trust ต้องไม่ inherit โดยอัตโนมัติ

⸻

55. Trust Domain

trust_domain:
  domain_id:
  name:
  controller:
  policies:
  identity_root:
  trust_anchors:
  verification_methods:
  revocation_methods:

⸻

56. Trust Anchor

Trust anchor คือ source ที่ policy กำหนดให้ใช้เป็น root of trust

ตัวอย่าง:

Human Controller
Organization CA
Hardware Root
Known Identity Registry
Independent Verifier

Trust anchor ต้องมี provenance และ lifecycle

⸻

57. Trust Anchor Compromise

หาก trust anchor compromise:

Anchor compromised
      ↓
Find dependent assessments
      ↓
Invalidate affected trust
      ↓
Reassess
      ↓
Update trust graph

ไม่ควรแก้เฉพาะ current score แล้วจบ

⸻

58. Trust API

Core APIs:

create_trust_assessment()
get_trust_assessment()
evaluate_trust()
verify_trust_evidence()
collect_evidence()
validate_evidence()
rank_evidence()
check_identity_assurance()
check_provenance()
check_freshness()
check_integrity()
check_history()
evaluate_reliability()
evaluate_security()
evaluate_policy_compliance()
compare_trust()
explain_trust()
request_trust()
negotiate_trust()
invalidate_trust()
suspend_trust()
revoke_trust()
reassess_trust()
detect_trust_drift()
detect_collusion()
detect_sybil()
get_trust_graph()
get_trust_history()

⸻

59. Trust Evaluation API

evaluate_trust(
    subject,
    purpose,
    scope,
    context,
    required_assurance
)

ผลลัพธ์:

trust_result:
  subject:
  level:
  confidence:
  evidence_refs:
  limitations:
  conditions:
  valid_from:
  valid_until:
  risk_assessment:
  unresolved_conflicts:
  recommendation:

คำว่า recommendation ในที่นี้หมายถึง recommendation เชิงระบบ เช่น:

REQUIRE_MORE_EVIDENCE
REQUIRE_INDEPENDENT_VERIFICATION
ALLOW_LOW_RISK_USE
BLOCK_HIGH_RISK_USE

ไม่ใช่การให้ authority โดยตรง

⸻

60. Trust Explanation

Veda ต้องสามารถตอบ:

Why do you trust this agent?

ผล:

Identity:
verified
Passport:
valid
Controller:
verified
Historical verification:
92/100 relevant tasks
Recent failures:
1
Security status:
normal
Evidence freshness:
recent
Scope:
code review only

ไม่ใช่:

Trust = 87%

แล้วจบ

⸻

61. Trust Receipt

ทุก consequential trust decision ควรสร้าง:

trust_receipt:
  trust_id:
  subject:
  purpose:
  scope:
  evidence_refs:
  decision:
  conditions:
  policy_version:
  verifier:
  timestamp:
  expires_at:
  signature:

Receipt เข้า Chronicle

⸻

62. Trust Events

TrustAssessmentRequested
TrustAssessmentStarted
TrustEvidenceRequested
TrustEvidenceCollected
TrustEvidenceValidated
TrustEvidenceRejected
TrustIdentityEvaluated
TrustProvenanceEvaluated
TrustReliabilityEvaluated
TrustSecurityEvaluated
TrustFreshnessEvaluated
TrustAssessed
TrustConditionAdded
TrustConditionChanged
TrustNegotiationStarted
TrustNegotiationCompleted
TrustNegotiationFailed
TrustConflictDetected
TrustConflictResolved
TrustDriftDetected
TrustReassessmentStarted
TrustReassessmentCompleted
TrustSuspended
TrustRevoked
TrustExpired
TrustReceiptCreated
TrustAnomalyDetected
TrustSybilDetected
TrustCollusionDetected

⸻

63. Trust State Machine

              ┌───────────┐
              │ REQUESTED  │
              └─────┬─────┘
                    ↓
             ┌──────────────┐
             │ EVALUATING   │
             └──────┬───────┘
                    ↓
             ┌──────────────┐
             │  ASSESSED    │
             └───┬──────┬───┘
                 │      │
                 ↓      ↓
              ACTIVE   CONDITIONAL
                 │      │
                 └──┬───┘
                    ↓
             REASSESSMENT
                    │
          ┌─────────┼─────────┐
          ↓         ↓         ↓
      EXPIRED   SUSPENDED   REVOKED

Alternative terminal states:

UNKNOWN
INSUFFICIENT_EVIDENCE
DISPUTED
INVALID

⸻

64. Trust Security Threats

TRUST-SEC-01

Evidence forgery

TRUST-SEC-02

Evidence replay

TRUST-SEC-03

Evidence poisoning

TRUST-SEC-04

Sybil attack

TRUST-SEC-05

Collusion

TRUST-SEC-06

Trust laundering

TRUST-SEC-07

Credential compromise

TRUST-SEC-08

Stale trust

TRUST-SEC-09

Trust score gaming

TRUST-SEC-10

Benchmark manipulation

TRUST-SEC-11

False endorsements

TRUST-SEC-12

Source independence failure

TRUST-SEC-13

Context confusion

TRUST-SEC-14

Trust inheritance abuse

TRUST-SEC-15

Trust anchor compromise

TRUST-SEC-16

Historical record manipulation

TRUST-SEC-17

Verification bypass

TRUST-SEC-18

Trust inflation through self-report

TRUST-SEC-19

Model-generated trust hallucination

TRUST-SEC-20

Policy-induced trust misclassification

⸻

65. Trust Invariants

TRUST-1

Trust ต้องมี subject ที่ระบุได้

TRUST-2

Trust ต้องมี scope

TRUST-3

Trust ต้องมี context เมื่อ context มีผล

TRUST-4

Trust ต้องมี provenance

TRUST-5

Trust ต้องมี evidence

TRUST-6

Evidence ต้องสามารถตรวจสอบความถูกต้องได้ตาม policy

TRUST-7

Trust ≠ Identity

TRUST-8

Trust ≠ Authority

TRUST-9

Trust ≠ Capability

TRUST-10

Trust ≠ Truth

TRUST-11

Trust ≠ Verification

TRUST-12

Trust ≠ Guarantee

TRUST-13

Unknown ต้องเป็น valid state

TRUST-14

Self-report ไม่ควรเป็น sole evidence สำหรับ high-risk claims

TRUST-15

Evidence จาก source เดียวกันไม่ควรถูกนับเป็น independent evidence

TRUST-16

Trust ไม่เป็น transitive โดย default

TRUST-17

Trust ไม่ควร inherit ข้าม scope โดยอัตโนมัติ

TRUST-18

Trust ต้องมี temporal validity

TRUST-19

Stale evidence ต้องลด assurance ตาม policy

TRUST-20

Trust assessment ต้องสามารถถูก invalidate ได้

TRUST-21

Trust revocation ต้องไม่ลบ historical evidence

TRUST-22

Trust conflict ต้องเข้าสู่ conflict handling

TRUST-23

Trust decision ต้องสามารถอธิบาย evidence basis ได้

TRUST-24

Trust assessment ต้องไม่ grant authority

TRUST-25

Trust Engine ห้ามแก้ Constitution

TRUST-26

Trust Engine ห้าม grant capability

TRUST-27

Trust Engine ต้อง preserve evidence provenance

TRUST-28

Trust changes ต้องถูก audit

TRUST-29

Trust model ต้องสามารถตรวจสอบ drift ได้

TRUST-30

High-risk action ต้องไม่พึ่ง trust เพียงอย่างเดียว

⸻

66. Reference Architecture

                 ┌──────────────────┐
                 │     Identity     │
                 │    RFC-0039      │
                 └────────┬─────────┘
                          ↓
                 ┌──────────────────┐
                 │ Agent Passport  │
                 │    RFC-0040      │
                 └────────┬─────────┘
                          ↓
                 ┌──────────────────┐
                 │ Evidence Layer  │
                 │ RFC-0012/0013   │
                 └────────┬─────────┘
                          ↓
                 ┌──────────────────┐
                 │ Verification    │
                 │    RFC-0026      │
                 └────────┬─────────┘
                          ↓
                 ┌──────────────────┐
                 │  Trust Engine   │
                 │    RFC-0041      │
                 └────────┬─────────┘
                          ↓
              ┌───────────┴───────────┐
              ↓                       ↓
        Decision / Router       Trust Negotiation
              ↓                       ↓
        Authorization            Federation
              ↓
           Action
              ↓
        External World
              ↓
        Verification
              ↓
          Chronicle

⸻

67. Complete Trust Loop

Actor
  ↓
Identity
  ↓
Passport
  ↓
Evidence
  ↓
Verification
  ↓
Trust Assessment
  ↓
Policy
  ↓
Authorization
  ↓
Action
  ↓
External Outcome
  ↓
Verification
  ↓
Experience
  ↓
Trust History
  ↓
Reassessment

นี่ทำให้ trust เป็น dynamic feedback loop แทนที่จะเป็นป้าย:

TRUSTED = TRUE

⸻

68. Example: Research Agent

Agent:

research-agent-01

Passport:

VALID

Identity:

CRYPTOGRAPHICALLY VERIFIED

History:

80 verified research tasks

Recent evidence:

fresh

Security:

normal

Trust result:

HIGH
for:
public web research

แต่ถ้า agent ขอ:

modify production database

Trust Engine อาจตอบ:

Scope mismatch

ไม่ใช่:

HIGH TRUST → ALLOW

⸻

69. Example: New Agent

Agent:

new-agent-01

Identity:

verified

Passport:

valid

History:

none

Evidence:

limited

Trust:

T1 / LIMITED

มันไม่ได้แปลว่า agent นี้ไม่ดี

เพียง:

Not enough evidence yet.

สำหรับ low-risk:

read public documentation

อาจใช้งานได้

สำหรับ:

delete production database

ไม่ควรผ่าน trust requirement

⸻

70. Example: Compromised Agent

Agent A:

Trust:
HIGH

จากนั้นตรวจพบ:

credential compromise

flow:

Compromise Detected
        ↓
Trust Suspended
        ↓
Identity / Passport Review
        ↓
Capability Revocation
        ↓
Impact Analysis
        ↓
Dependent Trust Assessments
        ↓
Reassessment

Historical trust ไม่ถูกลบ

แต่ current trust เปลี่ยนเป็น:

SUSPENDED

⸻

71. Why Trust Is Separate

สถาปัตยกรรม Veda จึงมี:

Identity
     ↓
Who are you?
Passport
     ↓
What evidence do you present?
Trust
     ↓
How much should I rely on you?
Capability
     ↓
What can you technically do?
Authorization
     ↓
What may you do now?
Action
     ↓
What did you attempt?
Verification
     ↓
What actually happened?

นี่คือ separation of concerns ที่ต้องรักษาไว้จนถึง implementation

⸻

72. Final Principle

Trust is not belief. Trust is a scoped, evidence-based decision about how much reliance is justified.

และสำหรับ Veda:

Identity establishes WHO.
Passport establishes WHAT IS PRESENTED.
Evidence establishes WHAT SUPPORTS THE CLAIM.
Verification establishes WHAT CAN BE CONFIRMED.
Trust establishes HOW MUCH RELIANCE IS JUSTIFIED.
Authorization establishes WHAT MAY BE DONE.
External World establishes WHAT ACTUALLY HAPPENED.
Chronicle preserves the history.

ดังนั้น Trust Engine ไม่มีสิทธิ์อนุมัติ action เอง แม้จะประเมินว่า agent น่าเชื่อถือระดับสูงสุดก็ตาม

เพราะ:

HIGH TRUST
≠
HIGH AUTHORITY

และ:

HIGH TRUST
+
NO AUTHORIZATION
=
NO ACTION

นี่คือ boundary ที่ RFC-0041 ต้องรักษาอย่างเด็ดขาด