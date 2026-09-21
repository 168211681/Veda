# WM-12 Alternative A — Minimal RFC Normalization Plan

Date: 2026-09-21

Status: PROPOSAL ONLY — OWNER DECISION REQUIRED

Finding: WM-12 (P0 OPEN)

RFC Freeze: BLOCKED

เอกสารนี้เป็นแผน normalization สำหรับ Alternative A เท่านั้น ไม่ใช่ owner approval, RFC acceptance, constitutional amendment หรือหลักฐานว่า WM-12 resolved และไม่ได้อนุญาตให้เข้าสู่ SPEC หรือ implementation

## Executive conclusion

Alternative A สามารถ normalize ภายใน Draft RFC-0001, RFC-0001A, RFC-0002, RFC-0003 และ RFC-0004 โดยไม่เปลี่ยน normative meaning ของ Constitution ได้ **เฉพาะเมื่อ owner ยืนยันการตีความแบบแคบ** ดังนี้:

1. `Commit` ใน canonical constitutional lifecycle หมายถึง authoritative operation/World commitment
2. `Event -> Chronicle` หลัง `Commit` หมายถึง successful outcome Event และการบันทึกผลสำเร็จหลัง commit
3. durable pre-commit factual records อาจบันทึกข้อเท็จจริงที่เกิดขึ้นแล้ว เช่น authorization decision, execution attempt, observation และ verification decision แต่ห้ามอ้างว่า operation สำเร็จ ห้าม advance World version และห้ามมี World authority

ถ้า owner ไม่ยืนยันการตีความนี้ และตีความว่า Constitution ห้าม durable factual persistence ทุกชนิดก่อน `Commit` การใช้ Alternative A จะเปลี่ยน constitutional semantics และต้องทำ constitutional amendment แยกต่างหากก่อนแก้ RFC

## Constitutional interpretation required

### Text ที่ต้องรักษา

- World Kernel เป็น final authority สำหรับ state transitions ตาม `docs/architecture/ARCHITECTURE_CONSTITUTION.md:158-174`
- canonical privileged execution pipeline คือ `Execution -> Verification -> Commit -> Event -> Chronicle` ตาม `docs/architecture/ARCHITECTURE_CONSTITUTION.md:178-204`
- significant actions ต้องมี durable evidence ซึ่งรวมผล, verification และ commit/rollback status ตาม `docs/architecture/ARCHITECTURE_CONSTITUTION.md:267-284`
- Event ต้องแทน fact ที่เกิดขึ้นแล้ว และห้ามแทน unverified intention เสมือน completed action ตาม `docs/architecture/ARCHITECTURE_CONSTITUTION.md:322-338`
- Chronicle เป็น durable historical record แยกจาก Event Fabric ตาม `docs/architecture/ARCHITECTURE_CONSTITUTION.md:342-366`
- verification lifecycle ต้องมี `Execute -> Observe Result -> Verify Expected State -> Commit` ตาม `docs/architecture/ARCHITECTURE_CONSTITUTION.md:414-428`

### Proposed interpretation

คำว่า `Event` ในขั้นหลัง `Commit` ต้องหมายถึง **successful outcome event** ไม่ใช่ record ทุกชนิดที่มีรูปแบบ event หรือถูกเก็บใน Chronicle

pre-commit record ที่ยอมรับได้ต้องเป็น factual record ของสิ่งที่เกิดขึ้นแล้ว เช่น:

- permission evaluation produced `DENIED`
- execution attempt started
- external effect was observed
- verification returned `FAILED`
- candidate transition conflicted with a newer parent World version

record เหล่านี้:

- มี historical/audit authority เฉพาะข้อเท็จจริงที่ record ระบุ
- ไม่มี authority ในการ commit World State
- ห้ามใช้ชื่อหรือ payload ที่สื่อว่า operation สำเร็จ
- ห้ามใช้แทน `WORLD_COMMIT_RECEIPT`
- ห้ามทำให้ post-commit consumer เชื่อว่า World transition สำเร็จ

การตีความนี้รักษา Event Integrity เพราะแต่ละ record เกิดหลัง fact ที่ record นั้นแทน และรักษา Verification Before Commitment เพราะยังไม่มี authoritative success หรือ World commit ก่อน verification

### Constitutional amendment trigger

ถ้า owner ตีความ `Event -> Chronicle` ว่าห้าม durable pre-commit fact ทุกประเภท Alternative A ขัดกับ `docs/architecture/ARCHITECTURE_CONSTITUTION.md:178-204` โดยตรง การแก้ขั้นต่ำที่ต้องเสนอแยกต่างหากคือข้อความเชิง normative เช่น:

> Durable factual evidence MAY be recorded after the represented fact occurs and before authoritative commitment. Such evidence MUST NOT represent successful completion or authorize World State mutation.

ข้อความนี้เป็น constitutional amendment และต้องผ่าน `Constitutional Review` ตาม `docs/adr/GOVERNANCE.md:416-423` พร้อม owner approval แยกจาก RFC normalization แผนนี้ไม่อนุมัติและไม่แก้ Constitution

## Canonical concepts

### 1. Pre-commit factual record

`PRE_COMMIT_FACT_RECORD` คือ immutable Chronicle record ของ fact ที่เกิดขึ้นแล้วก่อน authoritative World commit

ตัวอย่างประเภทที่อนุญาต:

- `PROPOSED`
- `AUTHORIZED`
- `DENIED`
- `EXECUTION_STARTED`
- `OBSERVED`
- `VERIFICATION_PASSED`
- `VERIFICATION_FAILED`
- `REJECTED`
- `FAILED`
- `PARENT_VERSION_CONFLICT`

ข้อบังคับ:

- record MUST ระบุ `transition_attempt_id` หรือ stable operation identity
- record MUST ไม่อ้าง successful outcome
- record MUST ไม่เลื่อน authoritative World version
- record MUST ไม่ authorize transition ด้วยตัวเอง
- record MUST immutable; correction, invalidation และ recovery ใช้ record ใหม่
- durable audit failure สำหรับ consequential decision MUST fail closed

### 2. Post-commit successful outcome event

`POST_COMMIT_SUCCESSFUL_OUTCOME_EVENT` คือ Event ที่ materialize หลัง atomic World commit และประกาศว่า verified operation ถูก commit สำเร็จแล้ว

ข้อบังคับ:

- MUST reference exact `WORLD_COMMIT_RECEIPT`
- MUST ระบุ resulting World version
- MUST ไม่ valid ถ้า receipt ไม่มีหรือไม่ตรงกับ transition
- duplicate delivery MAY เกิดได้ แต่ consumer MUST process แบบ idempotent
- Event Fabric delivery ไม่ใช่ World authority และไม่ใช่หลักฐาน commit โดยลำพัง

### 3. Committed-event delivery intent

`COMMITTED_EVENT_DELIVERY_INTENT` คือ durable outbox intent ที่สร้างใน atomic boundary เดียวกับ World commit ไม่ใช่ successful outcome event ที่เผยแพร่แล้ว

หน้าที่คือทำให้ relay สามารถ materialize หรือ redeliver post-commit outcome Event หลัง crash โดยไม่ต้อง execute operation หรือ mutate World ซ้ำ

## Proposed canonical ordering

```text
ACTION / EVENT CANDIDATE CREATED
        ↓
VALIDATE INPUT AND AUTHORITY
        ↓
PRE-COMMIT FACT RECORDED
        ↓
TRANSITION PREPARED AGAINST EXACT PARENT
        ↓
EXECUTION
        ↓
OBSERVATION
        ↓
OBSERVATION FACT RECORDED
        ↓
VERIFICATION
        ↓
BOUND VERIFICATION RECEIPT RECORDED
        ↓
COMPARE-AND-SWAP EXPECTED PARENT
        ↓
ATOMIC VISIBILITY BOUNDARY:
  - WORLD VERSION
  - WORLD_COMMIT_RECEIPT
  - COMMITTED-EVENT DELIVERY INTENT
        ↓
POST-COMMIT SUCCESSFUL OUTCOME EVENT
        ↓
IDEMPOTENT EVENT FABRIC DELIVERY
        ↓
CHRONICLE / QUERY PROJECTION
```

`Execution -> Observation -> Verification` เป็น lifecycle ที่บังคับสำหรับ consequential operation ที่ verification ทำได้ และตรงกับ `docs/architecture/ARCHITECTURE_CONSTITUTION.md:414-428` และ `docs/rfc/RFC-0001-constitution.md:224-238`

## Atomic boundary

การเปลี่ยน authoritative state ต้องทำให้สามสิ่งนี้ visible แบบ all-or-none:

1. new canonical `WORLD_VERSION`
2. matching `WORLD_COMMIT_RECEIPT`
3. `COMMITTED_EVENT_DELIVERY_INTENT`

ห้ามมีกรณีที่ World version visible แต่ receipt หรือ delivery intent สูญหาย ถ้า storage topology ไม่รองรับ distributed atomic transaction implementation ต้องใช้ protocol ที่ให้ผลเชิงตรรกะเทียบเท่า เช่น single authoritative transaction/outbox boundary โดยยังรักษา World Kernel เป็น sole commit authority

`WORLD_COMMIT_RECEIPT` ต้องอ้างถึง:

- `transition_id`
- exact input/factual record identifiers
- applicable authorization decision
- exact verification receipt identifier/hash
- exact parent World version
- resulting World version
- committed-event delivery intent identifier

## Verification evidence binding

verification receipt ขั้นต่ำต้องมี:

```text
verification_receipt_id
transition_attempt_id
parent_world_version
candidate_transition_hash
evidence_set_ids_or_hash
verification_policy_id
verification_policy_version
verdict
verified_at
```

invariants:

- receipt ใช้ commit ได้เฉพาะ candidate hash และ parent version ที่ตรงกันทุก field
- evidence set ต้องระบุตัวตนหรือ hash แบบ immutable
- policy version ต้อง pin ณ เวลาตรวจ ไม่ใช่อ้าง policy ล่าสุดแบบ mutable
- `WORLD_COMMIT_RECEIPT` ต้อง bind exact verification receipt ไม่ใช่เพียง `PASSED` flag
- in-memory verification ที่ยังไม่ durable ห้าม authorize commit

## Stale verification rejection

เมื่อ compare-and-swap พบว่า authoritative parent ไม่ตรงกับ `parent_world_version` ใน verification receipt:

1. MUST NOT commit World version, receipt หรือ delivery intent
2. MUST append durable `PARENT_VERSION_CONFLICT` fact
3. verification receipt เดิมยัง immutable แต่ MUST ถือว่า inapplicable ต่อ parent ใหม่
4. transition MUST re-prepare จาก current canonical parent
5. candidate MUST ได้ hash ใหม่ตามผลที่ prepare ใหม่
6. transition attempt MUST ใช้ attempt identity ใหม่และผ่าน observation/verification ใหม่ตาม applicability
7. logical action/idempotency key เดิม MUST ป้องกัน external effect หรือ World effect ซ้ำ

ห้าม rebind receipt เดิมกับ parent หรือ candidate ใหม่ และห้ามแก้ receipt เดิมย้อนหลัง

## Failure and recovery invariants

| Failure point | Persisted evidence | World authority | Required recovery |
|---|---|---|---|
| ก่อน durable pre-commit fact | อาจไม่มี canonical evidence | World เดิม | reevaluate/retry ด้วย stable identity; ห้ามอ้าง final decision |
| หลัง execution ก่อน observation | execution-attempt fact; external effect อาจเกิด | World เดิม | observe/reconcile ก่อน retry; ห้าม blind re-execution |
| หลัง observation ก่อน verification | observation fact | World เดิม | verify จาก durable evidence |
| verification failed | failure receipt | World เดิม | stop; append rejection/failure; compensation เป็น operation ใหม่ |
| verification passed แต่ parent conflict | verification receipt + conflict fact | World เวอร์ชันล่าสุดของ concurrent commit | re-prepare และ re-verify; ห้าม reuse stale receipt |
| crash ระหว่าง atomic boundary | ต้องเห็น none หรือครบทั้งสาม | World เดิมหรือ World ใหม่หนึ่งครั้ง | query ด้วย `transition_id`; retry commit request แบบ idempotent |
| หลัง atomic commit ก่อน delivery | World + receipt + outbox intent | World ใหม่ | relay/materialize outcome Event จาก intent |
| หลัง delivery ก่อน acknowledgement | outcome Event อาจถูกส่งแล้ว | World ใหม่ | redeliver; consumer deduplicate ด้วย stable event identity |
| projection partial | canonical ledger/receipt ครบ; projection อาจล้าหลัง | World ใหม่ | rebuild projection จาก ordered authoritative receipts/facts |
| Chronicle/audit persistence unavailable | ไม่มี durable prerequisite ใหม่ | World ไม่เปลี่ยน | fail closed และบันทึก durability incident เมื่อระบบกลับมา |

recovery MUST append new conflict, recovery, correction หรือ compensation facts และ MUST NOT rewrite committed history

## Minimum normative changes by file

### `docs/rfc/RFC-0001-constitution.md`

1. ที่ `docs/rfc/RFC-0001-constitution.md:140-146` แยกนิยาม pre-commit factual record ออกจาก post-commit successful outcome Event และระบุว่า factual record ไม่มี World authority
2. ที่ `docs/rfc/RFC-0001-constitution.md:224-255` รักษา `Execute -> Observe -> Verify -> Commit` และเพิ่มว่าหลักฐาน factual อาจ append ระหว่างขั้นได้โดยไม่ถือเป็น commitment
3. ที่ `docs/rfc/RFC-0001-constitution.md:307-334` ทำ authority pipeline ให้จบด้วย `Verification -> World Commit -> Successful Outcome Event -> Audit/Chronicle Projection`
4. ที่ `docs/rfc/RFC-0001-constitution.md:421-449` แยก lifecycle state `EVENT_RECORDED` และ `WORLD_COMMITTED`; เพิ่ม denied/rejected/failed terminal paths ที่ไม่เลื่อน World
5. ที่ `docs/rfc/RFC-0001-constitution.md:625-641` บังคับ recovery ผ่าน append-only correction/recovery records และห้าม rewrite history

### `docs/rfc/RFC-0001A-permission-matrix.md`

1. ที่ `docs/rfc/RFC-0001A-permission-matrix.md:83-109` แยก permission decision receipt ก่อน execution ออกจาก execution/observation/verification audit
2. ที่ `docs/rfc/RFC-0001A-permission-matrix.md:689-715` บังคับ durable records สำหรับ `ALLOWED`, `DENIED`, `EXPIRED`, `REVOKED`, execution result และ verification result
3. กำหนด decision visibility กับ audit receipt เป็น atomic; ถ้า receipt persist ไม่ได้ decision ห้ามมีผลต่อ consequential execution
4. ที่ `docs/rfc/RFC-0001A-permission-matrix.md:798-810` ทำ PM-5 ให้ชัดว่า audit durability failure ต้อง fail closed

### `docs/rfc/RFC-0002-world-model.md`

1. ที่ `docs/rfc/RFC-0002-world-model.md:414-424` ระบุว่า failed, rejected, denied, conflict และ recovery records เป็น durable historical facts แต่ไม่ใช่ successful World changes
2. ที่ `docs/rfc/RFC-0002-world-model.md:676-678` แทน unresolved atomic-ordering clause ด้วย atomic triple: World version + World commit receipt + committed-event delivery intent
3. ที่ `docs/rfc/RFC-0002-world-model.md:761-776` เพิ่ม conformance requirements สำหรับ bound verification, stale-verification rejection, triple atomicity, receipt-based replay selection และ rejected-path auditability
4. ที่ `docs/rfc/RFC-0002-world-model.md:809` หลัง owner decision เท่านั้น ให้แทน unresolved-conflict paragraph ด้วย canonical ordering โดยคง RFC status เป็น Draft

### `docs/rfc/RFC-0003-event-model.md`

1. ที่ `docs/rfc/RFC-0003-event-model.md:475-487` แทน lifecycle ที่กำกวมด้วย candidate validation, factual recording, quarantine/rejection และ post-World-commit outcome materialization
2. ที่ `docs/rfc/RFC-0003-event-model.md:558-573` ระบุ Event Store เป็น Chronicle-owned durable historical ledger ไม่ใช่คู่แข่งของ World authority
3. ที่ `docs/rfc/RFC-0003-event-model.md:789-801` เปลี่ยน verification link เป็น MUST สำหรับ consequential transition และเพิ่ม binding fields ทั้งหมด
4. ที่ `docs/rfc/RFC-0003-event-model.md:819-833` เปลี่ยน rejected consequential attempt audit จาก `SHOULD` เป็น `MUST`
5. ที่ `docs/rfc/RFC-0003-event-model.md:931-955` แยก Chronicle durable ledger ออกจาก human-readable/query projection; projection เป็น derived view แต่ durable Chronicle ไม่ใช่เพียง view
6. ที่ `docs/rfc/RFC-0003-event-model.md:1268-1300` แทน reference lifecycle ด้วย canonical ordering ในเอกสารนี้และระบุ delivery idempotency

### `docs/rfc/RFC-0004-state-and-world-transition.md`

1. ที่ `docs/rfc/RFC-0004-state-and-world-transition.md:71-132` เพิ่ม explicit `Execution -> Observation -> Verification`; นิยาม input `E` เป็น validated immutable input fact ไม่ใช่หลักฐานว่า World committed แล้ว
2. ที่ `docs/rfc/RFC-0004-state-and-world-transition.md:259-275` เปลี่ยน `World Transition Engine` ให้เป็น controlled subcomponent/function ภายใต้ World Kernel เพื่อไม่สร้าง authority ที่สอง
3. ที่ `docs/rfc/RFC-0004-state-and-world-transition.md:399-419` เปลี่ยน atomicity จาก `SHOULD` เป็น `MUST` สำหรับ atomic triple
4. ที่ `docs/rfc/RFC-0004-state-and-world-transition.md:911-929` เพิ่ม bound verification receipt และ expected-parent compare-and-swap
5. ที่ `docs/rfc/RFC-0004-state-and-world-transition.md:933-954` กำหนด recovery ให้ตรวจ World version, commit receipt และ outbox intent แบบ none-or-all; ห้าม infer commit จาก delivery
6. ที่ `docs/rfc/RFC-0004-state-and-world-transition.md:957-971` แยก write-ahead factual intent จาก post-commit successful outcome Event
7. ที่ `docs/rfc/RFC-0004-state-and-world-transition.md:1279-1307` replace final sequence ด้วย canonical ordering ที่รวม observation, bound verification, atomic commit และ post-commit outcome delivery

## Accepted ADR compatibility

แผนนี้ไม่ต้องแก้ accepted ADR semantics ถ้า RFC normalization รักษาข้อต่อไปนี้:

- World Kernel เป็น sole authoritative owner และ sole committer ของ current modeled World State ตาม `docs/adr/ADR-0001-world-kernel.md:79-105`
- Event/Chronicle เป็น historical record และไม่ใช่ canonical current World ตาม `docs/adr/ADR-0001-world-kernel.md:154-168`
- Event Fabric รองรับ duplicate delivery และ World Kernel มี idempotency mechanism ตาม `docs/adr/ADR-0002-event-fabric-chronicle.md:502-523`
- correction/recovery ใช้ append-only history ไม่ rewrite committed Event ตาม `docs/adr/ADR-0002-event-fabric-chronicle.md:591-624`
- Chronicle, Event Fabric และ World Kernel รักษา authority boundaries ตาม `docs/adr/ADR-0002-event-fabric-chronicle.md:741-759`

ถ้า patch Draft RFC ใดทำให้ข้อความเหล่านี้เปลี่ยนความหมาย ต้องหยุดและขอ owner decision แทนการแก้ accepted ADR โดยเงียบ

## Dependency order

1. owner ตัดสิน constitutional interpretation
2. normalize RFC-0001 vocabulary และ lifecycle
3. normalize RFC-0001A permission/audit durability
4. normalize RFC-0002 World authority และ atomic triple
5. normalize RFC-0003 factual record/outcome Event/Chronicle semantics
6. normalize RFC-0004 transition, atomicity, CAS และ recovery protocol
7. ทำ cross-document audit และตรวจ P0 contradiction ใหม่
8. ขอ owner approval สำหรับ Draft RFC changes ตาม governance
9. สร้าง ADR ใหม่เฉพาะเมื่อ RFC ได้รับ approval; ห้ามแก้ accepted ADR-0001/ADR-0002 semantics
10. เข้า SPEC/implementation ได้เมื่อ freeze gates ที่เกี่ยวข้องผ่านแล้วเท่านั้น

## Acceptance tests and validation checklist

### Repository and status

- [ ] RFC-0001, RFC-0001A, RFC-0002, RFC-0003 และ RFC-0004 ยังคง `Draft` จนมี owner approval
- [ ] Constitution ไม่มี diff
- [ ] accepted ADR-0001 และ ADR-0002 ไม่มี semantic diff
- [ ] WM-12 ยังคง `P0 OPEN` จน cross-document evidence ผ่าน
- [ ] RFC Freeze ยังคง `BLOCKED` จนไม่มี unresolved P0

### Semantic checks

- [ ] ทุกเอกสารแยก `PRE_COMMIT_FACT_RECORD`, `WORLD_COMMIT_RECEIPT` และ `POST_COMMIT_SUCCESSFUL_OUTCOME_EVENT`
- [ ] ทุก consequential success path มี `Execution -> Observation -> Verification`
- [ ] ไม่มี successful outcome Event ก่อน matching World commit receipt
- [ ] World Kernel เป็น sole authoritative World committer
- [ ] atomic boundary ครบ World version + receipt + delivery intent
- [ ] crash หลัง World commit ไม่ทำให้ committed outcome สูญหาย
- [ ] duplicate delivery ไม่ทำให้ external execution หรือ World mutation ซ้ำ
- [ ] replay ไม่ apply factual/verification records ที่ไม่มี matching World commit receipt เป็น World transition
- [ ] verification receipt bind exact parent, candidate hash, evidence set และ policy version
- [ ] parent conflict บังคับ re-prepare และ re-verify
- [ ] failed, rejected, denied และ recovery paths มี durable audit evidence
- [ ] audit persistence failure สำหรับ consequential operation fail closed
- [ ] recovery append record ใหม่และไม่ rewrite history
- [ ] Chronicle มี historical authority เท่านั้น ไม่ถือ authority เหนือ external reality หรือ current World
- [ ] RFC-0003 ไม่เรียก durable Chronicle ledger ว่าเป็นเพียง derived view
- [ ] RFC-0004 ใช้ `MUST` สำหรับ atomic commit invariant

### Mechanical validation

- [ ] `git diff --check` ผ่าน
- [ ] ตรวจ full diff ของ RFC ทั้งห้าไฟล์
- [ ] term scan ไม่พบ `COMMITTED` ที่ไม่ได้ qualify ว่า Event lifecycle หรือ World lifecycle
- [ ] source-reference line numbers ถูก refresh หลัง patch
- [ ] cross-document contradiction matrix ไม่มี P0 ที่ unresolved ก่อนเสนอ WM-12 resolution

## Outstanding owner decisions

owner ต้องตัดสินอย่างชัดเจนก่อนแก้ Draft RFC:

1. ยืนยันหรือปฏิเสธว่า constitutional post-commit `Event -> Chronicle` หมายถึง successful outcome path
2. ยืนยันหรือปฏิเสธว่า truthful pre-commit factual records ไม่ใช่ constitutional commitment
3. ยืนยัน atomic triple: World version + World commit receipt + committed-event delivery intent
4. ยืนยัน stale-verification rule ว่า parent mismatch บังคับ re-prepare และ re-verify
5. หากปฏิเสธข้อ 1 หรือ 2 ให้ตัดสินว่าจะอนุญาตให้เตรียม constitutional amendment proposal แยกต่างหากหรือไม่

การยอมรับแผนนี้ “เพื่อ review ต่อ” ไม่เท่ากับ formal owner approval หรือ RFC acceptance

## Next permitted action

ก่อน owner decision ทำได้เฉพาะ review และแก้ proposal/plan นี้ ห้ามแก้ Constitution, RFCs หรือ accepted ADRs

หลัง owner decision ที่ชัดเจน การกระทำถัดไปที่อนุญาตคือสร้าง scoped patch สำหรับ Draft RFC-0001, RFC-0001A, RFC-0002, RFC-0003 และ RFC-0004 ตาม dependency order แล้วทำ independent cross-document audit โดยยังคง WM-12 `OPEN` และ RFC Freeze `BLOCKED` จน evidence ครบ
