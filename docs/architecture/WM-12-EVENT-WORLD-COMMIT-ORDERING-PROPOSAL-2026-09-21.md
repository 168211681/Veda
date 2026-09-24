# ข้อเสนอการจัดลำดับ Event / Verification / World Commit / Chronicle สำหรับ WM-12

วันที่: 2026-09-21

สถานะ: PROPOSED — OWNER DECISION REQUIRED

Finding: WM-12

Severity: P0

สถานะ finding ปัจจุบัน: OPEN

ขอบเขต: semantic ระดับสถาปัตยกรรมเท่านั้น ไม่มีการเปลี่ยนสถานะ RFC, ADR, SPEC หรือ implementation

Baseline: `main` ที่ `55aaf27ad016495d47b7f49c297f3dd265c7f41a`

## ขอบเขตการตัดสินใจ

Owner ระบุว่า RFC-0002 v0.2.0 ยอมรับได้ในหลักการเพื่อทบทวนต่อ ข้อความดังกล่าวไม่ใช่ formal owner approval, RFC acceptance หรือการอนุมัติข้อเสนอนี้

เอกสารนี้เสนอแนวทางแก้ WM-12 แต่ไม่ได้:

- เปลี่ยน WM-12 เป็น resolved;
- แก้ Architecture Constitution;
- แก้ semantic ของ Accepted ADR;
- เปลี่ยน RFC-0002 ออกจาก Draft;
- เข้าสู่ SPEC หรือ implementation;
- อนุญาต commit, push, merge หรือ deployment

## ฐานอำนาจทางสถาปัตยกรรม

ข้อเสนอนี้อยู่ภายใต้ข้อกำหนดต่อไปนี้:

- Constitution อยู่เหนือ RFC และ ADR และ artifact ระดับล่างห้ามขัด artifact ระดับสูง: `docs/architecture/ARCHITECTURE_CONSTITUTION.md:28-56`
- World Kernel เป็นเจ้าของ commit state และเป็น authority สุดท้ายของ state transition: `docs/architecture/ARCHITECTURE_CONSTITUTION.md:158-174`
- Verification ที่จำเป็นต้องเกิดก่อน commitment: `docs/architecture/ARCHITECTURE_CONSTITUTION.md:414-428` และ `docs/rfc/RFC-0001-constitution.md:224-238`
- Event สำคัญต้องมี durable evidence และ consequential action ต้องมี audit trace: `docs/architecture/ARCHITECTURE_CONSTITUTION.md:267-284` และ `docs/rfc/RFC-0001-constitution.md:242-255`
- Event Fabric transport ไม่เท่ากับ durable historical storage; Chronicle เป็นเจ้าของ durable history: `docs/architecture/ARCHITECTURE_CONSTITUTION.md:342-366`
- มีเพียง World Kernel ที่ commit authoritative modeled World State ได้: `docs/adr/ADR-0001-world-kernel.md:79-105` และ `docs/adr/ADR-0001-world-kernel.md:276-324`
- ความหมายของ Accepted ADR เปลี่ยนไม่ได้โดยปริยาย; semantic change ต้องมี ADR ใหม่: `docs/adr/GOVERNANCE.md:55-74`, `docs/adr/GOVERNANCE.md:340-358` และ `docs/adr/GOVERNANCE.md:485-489`
- ห้ามเลื่อนไป architecture phase ถัดไปขณะที่ยังมี P0 contradiction: `docs/adr/GOVERNANCE.md:427-461`

## การแยกความหมายของคำ

คำต่อไปนี้ห้ามใช้เป็นคำพ้องความหมายกัน:

| คำ | ความหมายที่เสนอ | ผลต่อ authority |
|---|---|---|
| Event creation | การกำหนด `event_id` และสร้าง candidate envelope แบบ in-memory หรือ staged เพื่ออธิบาย fact, observation, proposal, execution result, verification result หรือ decision | ไม่มี historical authority หรือ World authority |
| Event validation | การตรวจ schema, identity, provenance, authorization reference, causal consistency และ policy ตามชนิด Event; ไม่ใช่การพิสูจน์ external outcome | อาจรับหรือปฏิเสธ candidate สำหรับ durable history; ไม่มีสิทธิ์แก้ World |
| Durable event persistence | การ append validated record พร้อม lifecycle/status ลงใน historical ledger ที่ Chronicle เป็นเจ้าของ; staged recovery record ไม่ใช่ committed canonical event โดยอัตโนมัติ | ให้ historical durability เท่านั้น; ไม่มี World authority |
| Event lifecycle commit | จุดที่ validated Event กลายเป็น immutable canonical historical event ใน Chronicle เรียกว่า `EVENT_COMMITTED` ไม่ใช่ `WORLD_COMMITTED` | เป็น authority ว่า Veda บันทึกอะไรในประวัติ แต่ไม่มีอำนาจแก้ current World |
| Outcome verification | การเทียบ observed result และ candidate World State กับ expected state, postconditions, evidence และ policy | สร้าง verification decision; pass เป็นเงื่อนไขเมื่อจำเป็น แต่ไม่ commit World State |
| Authoritative World commit | การตัดสินใจของ World Kernel ที่ติดตั้ง canonical World version ใหม่หนึ่ง version จาก expected parent แบบ atomic เรียกว่า `WORLD_COMMITTED` | เปลี่ยน authoritative current modeled World State |
| Chronicle projection | human-readable หรือ query-oriented view ที่ derive จาก canonical Chronicle records แยกจาก durable ledger ที่ Chronicle เป็นเจ้าของ | ไม่มี historical-write authority หรือ World authority |
| Audit receipt | immutable, tamper-evident decision record ที่เชื่อม actor, authority, input events, verification, transition, World version, outcome และ failure state | เป็นหลักฐานของ decision แต่ไม่ให้ authority |

การแยก Chronicle ledger ออกจาก Chronicle projection แก้ naming collision ปัจจุบัน: Constitution ให้ Chronicle ดูแล durable history (`docs/architecture/ARCHITECTURE_CONSTITUTION.md:356-366`), ADR-0002 ให้ Chronicle ดูแล event persistence และ replay (`docs/adr/ADR-0002-event-fabric-chronicle.md:120-143`) แต่ RFC-0003 อธิบาย Chronicle เป็น human-readable view (`docs/rfc/RFC-0003-event-model.md:931-955`)

## Contradiction matrix

| ID | Normative requirement A | Normative requirement B | ข้อขัดแย้งหรือความกำกวม | ผลกระทบ P0 |
|---|---|---|---|---|
| WM12-C01 | Constitution กำหนด `Verification -> Commit -> Event -> Chronicle`: `docs/architecture/ARCHITECTURE_CONSTITUTION.md:178-204` | ADR-0002 กำหนดให้มี durable Chronicle record ก่อน authoritative commit สำหรับ operation ที่ต้องมี audit durability: `docs/adr/ADR-0002-event-fabric-chronicle.md:655-721` และวาง Chronicle ก่อน World Kernel: `docs/adr/ADR-0002-event-fabric-chronicle.md:76-102` | `Commit` และ `Event` ไม่ระบุชนิด หากอ่านเป็น World commit และ event persistence ครั้งแรก ลำดับจะตรงข้ามกัน | Crash อาจทำให้มี World state ที่ไม่มี audit หรือ history ที่ผูกกับ commit decision ไม่ได้ |
| WM12-C02 | RFC-0003 ใช้ lifecycle `CREATED -> RECORDED -> VALIDATED -> COMMITTED`: `docs/rfc/RFC-0003-event-model.md:475-487` | Invalid Event ห้ามเข้า authoritative World history: `docs/rfc/RFC-0003-event-model.md:487`; committed Event เป็น authoritative historical source: `docs/rfc/RFC-0003-event-model.md:558-573` | ไม่ระบุว่า `RECORDED` ก่อน `VALIDATED` เป็น quarantine/staging หรือ authoritative history | Invalid input อาจถูกตีความเป็น canonical Event หรือหายไปโดยไม่มี audit evidence |
| WM12-C03 | RFC-0004 กำหนด input `E` เป็น committed Event: `docs/rfc/RFC-0004-state-and-world-transition.md:89-106` | RFC-0004 ยังตรวจ schema, identity, authorization และ validation อื่นก่อน World commit: `docs/rfc/RFC-0004-state-and-world-transition.md:110-132` | ไม่ชัดว่า event validation เสร็จก่อน `EVENT_COMMITTED` หรือเป็น transition-admissibility validation คนละชั้น | Replay และ rejection implement แบบ deterministic ไม่ได้จนกว่าจะระบุเจ้าของแต่ละ validation decision |
| WM12-C04 | RFC-0003 ระบุว่า committed Event อาจสร้าง World transition และ rejected Event ควรถูกบันทึกเมื่อ policy ต้องการเท่านั้น: `docs/rfc/RFC-0003-event-model.md:805-833` | RFC-0002 กำหนดให้ consequential failure, denial, rejected transition และ recovery attempt ต้อง auditable แบบ durable: `docs/rfc/RFC-0002-world-model.md:414-424`; conformance rule กำหนด audit สำหรับ commit, reject, fail และ recovery: `docs/rfc/RFC-0002-world-model.md:761-776` | RFC-0003 ใช้ `SHOULD` แบบมีเงื่อนไข แต่ RFC-0002 proposal ใช้ `MUST` สำหรับ consequential path | Rejected state-changing attempt อาจไม่มี durable audit trail |
| WM12-C05 | RFC-0003 วาง `Event Committed -> World Updated`: `docs/rfc/RFC-0003-event-model.md:1268-1300` | RFC-0004 บังคับ transition และ outcome verification ก่อน World commit: `docs/rfc/RFC-0004-state-and-world-transition.md:71-85`, `docs/rfc/RFC-0004-state-and-world-transition.md:381-395` และ `docs/rfc/RFC-0004-state-and-world-transition.md:911-929` | ไม่ระบุว่า committed input Event แยกจาก World-commit outcome Event และ receipt หรือไม่ | Implementation อาจใช้ `EVENT_COMMITTED` เป็นสิทธิ์แก้ World หรือข้าม verification |
| WM12-C06 | RFC-0004 ระบุว่ามีเพียง World Transition Engine ที่ commit authoritative World State: `docs/rfc/RFC-0004-state-and-world-transition.md:259-270` | RFC-0002 และ Accepted ADR-0001 ระบุว่ามีเพียง World Kernel: `docs/rfc/RFC-0002-world-model.md:676-678`; `docs/adr/ADR-0001-world-kernel.md:79-105` | ไม่กำหนดว่า World Transition Engine อยู่ภายใน World Kernel หรือไม่ | เกิด commit authority สองตัว ขัด single-owner invariant |
| WM12-C07 | RFC-0004 แนะนำ `Event Record -> State Change -> Commit Marker`: `docs/rfc/RFC-0004-state-and-world-transition.md:957-971` | Constitution วาง `Event -> Chronicle` หลัง `Commit`: `docs/architecture/ARCHITECTURE_CONSTITUTION.md:182-202` | RFC ใช้ write-ahead record ก่อน commit แต่ constitutional diagram ดูเหมือนวาง event creation/persistence หลัง commit | การทำตาม diagram ใด diagram หนึ่งแบบ literal จะขัดอีกอัน หากไม่แยก pre-commit input record กับ post-commit outcome record |
| WM12-C08 | ADR-0002 กำหนด Chronicle เป็น durable historical record และ replay source: `docs/adr/ADR-0002-event-fabric-chronicle.md:201-229`, `docs/adr/ADR-0002-event-fabric-chronicle.md:527-587` | RFC-0003 กำหนด Event Store เป็น authoritative source ของ committed Event และ Chronicle เป็นเพียง view: `docs/rfc/RFC-0003-event-model.md:558-573`, `docs/rfc/RFC-0003-event-model.md:931-955` | ไม่ normalize ownership ระหว่าง Event Store กับ Chronicle และใช้ `Chronicle` หมายถึงทั้ง ledger กับ projection | Recovery อาจเลือก historical authority คนละตัวและสร้าง World projection ต่างกัน |
| WM12-C09 | RFC-0004 กำหนด logical transaction atomic ด้วย `SHOULD`: `docs/rfc/RFC-0004-state-and-world-transition.md:399-419` | RFC-0002 บังคับ fail-closed และห้าม partial/unaudited authoritative commit: `docs/rfc/RFC-0002-world-model.md:676-678`; ADR-0001 ห้าม partial authoritative commit: `docs/adr/ADR-0001-world-kernel.md:314-324` | Modality ใน RFC-0004 อ่อนเกินไปสำหรับ P0 invariant | Implementation อาจ conform ทั้งที่ atomicity เป็น optional |
| WM12-C10 | RFC-0001A วาง Audit หลัง Verification: `docs/rfc/RFC-0001A-permission-matrix.md:83-109` | ทุก permission evaluation รวม denial ต้องสร้าง audit record: `docs/rfc/RFC-0001A-permission-matrix.md:689-714` | Audit stage เดียวท้าย pipeline แทนทั้ง pre-execution denial และ execution/verification outcome ไม่ได้ | Denied และ interrupted path อาจ reconstruct แบบ durable ไม่ได้ |

## ทางเลือกการจัดลำดับ

### Alternative A — Chronicle-first และ verified World commit พร้อม atomic commit receipt

ลำดับเชิงตรรกะ:

```text
1. EVENT_CREATED
2. EVENT_VALIDATED
3. EVENT_COMMITTED_TO_CHRONICLE          # immutable transition input or observed fact
4. TRANSITION_PREPARED                   # expected parent + candidate state/delta
5. OUTCOME_VERIFIED
6. VERIFICATION_EVENT_COMMITTED          # durable verification evidence
7. WORLD_COMMIT + WORLD_COMMIT_RECEIPT   # one atomic visibility boundary
8. COMMITTED_EVENT_DELIVERY              # outbox/fabric, retryable
9. CHRONICLE_PROJECTION_UPDATED           # derived, idempotent, rebuildable
```

สำหรับ rejected หรือ failed path หลังขั้น 1-2 ให้สร้าง durable rejection/failure audit receipt และห้ามเกิดขั้น 3-7 ที่อ้าง successful transition

ข้อเสนอนี้ตีความ constitutional `Commit -> Event -> Chronicle` เป็นด้าน outcome ของ privileged action: หลัง verification ผ่าน World Kernel ตัดสิน authoritative commit, ส่ง committed outcome ผ่าน Event Fabric และ Chronicle projection แสดงผล ส่วน Chronicle record ก่อนหน้านั้นคือ fact ของ proposal, authorization, execution, observation และ verification ไม่ใช่คำกล่าวเท็จว่า World transition commit แล้ว

`WORLD_COMMIT` และ receipt ต้องอยู่ใน atomic durability/visibility boundary เดียวกัน โดย append receipt ลง Chronicle-owned ledger ภายใน boundary นั้น การแยก logical component ไม่บังคับให้ใช้คนละ physical transaction; MVP ใช้ transactional store เดียวได้ ส่วน distributed implementation ต้องใช้ consensus-backed transaction หรือ protocol เทียบเท่าที่ไม่อาจ expose World State โดยไม่มี receipt

### Alternative B — Unified atomic event-and-World commit bundle

ลำดับเชิงตรรกะ:

```text
1. EVENT_CREATED
2. EVENT_VALIDATED
3. RECOVERY_INTENT_STAGED                # non-canonical
4. TRANSITION_PREPARED
5. OUTCOME_VERIFIED
6. ATOMIC COMMIT BUNDLE:
     - EVENT_LIFECYCLE_COMMIT
     - VERIFICATION_RECEIPT_COMMIT
     - WORLD_VERSION_COMMIT
     - AUDIT_RECEIPT_COMMIT
7. COMMITTED_EVENT_DELIVERY
8. CHRONICLE_PROJECTION_UPDATED
```

ห้าม component ใดเห็นเพียงบางส่วนของขั้น 6 ส่วน rejected attempt ใช้ rejection-receipt transaction แยกและไม่เข้า success bundle

ทางเลือกนี้ให้ all-or-nothing boundary แข็งแรงที่สุด แต่ผูก Chronicle durability กับ World Store commit ใน transaction protocol เดียว อีกทั้งเปลี่ยน failure model ของ Accepted ADR-0002 ซึ่งอนุญาตให้ Chronicle history มีอยู่ขณะที่ World projection stale และ rebuild ได้ (`docs/adr/ADR-0002-event-fabric-chronicle.md:655-737`) การเลือกทางนี้จึงต้องมี superseding ADR ใหม่ ห้ามแก้ ADR-0002 ในที่เดิม

## การประเมินทางเลือก

เกณฑ์: `PASS` ตรงข้อกำหนดปัจจุบัน; `CONDITIONAL` ต้องมี design constraint หรือคำยืนยันชัดเจน; `FAIL` ขัด authority ปัจจุบันจนกว่าจะผ่าน governance change

| เกณฑ์ | Alternative A | Alternative B |
|---|---|---|
| Constitutional hierarchy | CONDITIONAL: ไม่แก้ Constitution แต่ต้องให้ owner ยืนยันว่า post-commit `Event -> Chronicle` หมายถึง outcome delivery/projection และไม่ห้าม truthful fact records ก่อน commit | CONDITIONAL: ใกล้ literal `Verification -> Commit -> Event -> Chronicle` ที่สุด แต่ต้องแยกชื่อ Event ใน atomic bundle กับ delivered outcome Event |
| Verification-before-commit | PASS: required outcome verification และ durable receipt มาก่อน World commit | PASS: verification มาก่อน atomic bundle |
| World Kernel authority | PASS: มีเพียง World Kernel ที่ authorize ขั้น 7; Chronicle record ไม่ self-authorize | PASS: World Kernel เป็น sole coordinator/authorizer ของ bundle; storage participant ไม่มี semantic authority |
| Crash consistency | PASS เมื่อ World version กับ commit receipt atomic; Chronicle-first record ช่วย resume อย่างปลอดภัย | PASS เมื่อมี atomic transaction/consensus จริง มิฉะนั้น FAIL เพราะห้าม partial visibility |
| Atomicity | CONDITIONAL: World version + commit receipt ต้อง atomic; input history ก่อนหน้านั้นตั้งใจให้อยู่ได้โดยไม่มี World commit | PASS ทาง semantic แต่ต้นทุนสูงที่สุดเพราะ success artifacts ทั้งหมดอยู่ boundary เดียว |
| Rejected-action auditability | PASS: append rejection/failure receipt โดยไม่มี successful transition Event | PASS: ต้องมี rejection transaction แยก |
| Deterministic replay | PASS: replay ใช้ ordered input Event พร้อม World commit receipt; input ที่ไม่มี receipt ไม่เลื่อน World version | PASS: bundle sequence เป็น replay unit ที่สมบูรณ์ |
| Idempotency | PASS ด้วย stable `event_id`, `transition_id`, `idempotency_key`, expected parent และ receipt uniqueness | PASS ด้วย unique bundle/transaction ID และ identity constraints เดียวกัน |
| Recovery | PASS: resume จาก durable input/verification หรือ replay จาก committed receipt; projection lag rebuild ได้ | CONDITIONAL: recovery manager ต้อง resolve prepared bundle โดยไม่เดาสถานะ |
| Implementation feasibility | PASS: MVP ใช้ database transaction เดียวครอบ logical Chronicle/World tables และค่อยกระจายภายหลังได้ | CONDITIONAL: ทำได้ใน colocated database แต่ยากขึ้นมากเมื่อแยก store/service |
| Accepted ADR-0002 compatibility | PASS: รักษา Chronicle-first history และ rebuildable World projection | FAIL หากไม่มี superseding ADR ใหม่ เพราะเปลี่ยน Chronicle-first/projection-recovery boundary |

## Proposed canonical ordering

ข้อเสนอแนะคือ **Alternative A** เพื่อให้ owner พิจารณาเป็นฐานของ RFC normalization

ทางเลือกนี้รักษา constitutional authority boundary และความหมายของ Accepted ADR-0001/ADR-0002 พร้อมรองรับ implementation เริ่มต้นขนาดเล็กโดยไม่ทำให้ physical database กลายเป็น architectural authority

### Canonical success path

1. Producer สร้าง candidate พร้อม stable `event_id`, `trace_id`, `action_id` และ `idempotency_key`
2. Runtime ตรวจ schema, identity, provenance, authorization reference และ event-class rules
3. Chronicle commit validated input fact; `EVENT_COMMITTED` หมายถึง durable immutable history เท่านั้น
4. Transition function ภายใน World Kernel เตรียม candidate delta จาก `expected_parent_world_version` ที่แน่นอน
5. Verification ตรวจ observed outcome, postconditions, evidence และ candidate delta
6. Chronicle commit `VERIFICATION_PASSED` และ receipt; ถ้า required verification unavailable, stale, ambiguous หรือ failed ให้ fail closed
7. World Kernel commit World version ใหม่และ append immutable `WORLD_COMMIT_RECEIPT` ลง Chronicle-owned ledger แบบ atomic ห้ามเห็นเพียงอย่างใดอย่างหนึ่ง
8. Transactional outbox หรือ durable relay เทียบเท่า publish committed outcome; delivery ซ้ำได้แต่ semantic mutation ซ้ำไม่ได้
9. Chronicle projection และ consumer อื่น update แบบ idempotent; projection lag ไม่สร้างหรือยกเลิก World authority

### Canonical rejection/failure path

1. สร้างหรือเก็บ candidate identity ขั้นต่ำเพื่อ correlation
2. Validate metadata เท่าที่จำเป็นต่อการ classify failure อย่างปลอดภัย
3. Commit audit receipt ชนิด `REJECTED`, `DENIED`, `FAILED`, `CONFLICT`, `EXPIRED` หรือ `RECOVERY_REQUIRED` ตามกรณี
4. ห้ามสร้าง success Event, ห้ามเลื่อน canonical World version และห้ามอ้าง outcome success
5. หาก audit receipt ที่จำเป็นยัง durable ไม่ได้ ให้ fail closed และเปิด durability incident; ห้าม authoritative World commit

## Proposed invariants

| ID | Invariant |
|---|---|
| WM12-I01 | `EVENT_COMMITTED` และ `WORLD_COMMITTED` เป็นคนละ lifecycle decision และห้ามใช้ label `COMMITTED` ที่ไม่ระบุชนิดใน normative text/interface |
| WM12-I02 | Event creation, delivery หรือ durable persistence ไม่ให้อำนาจแก้ World State |
| WM12-I03 | มีเพียง World Kernel ที่ authorize และ expose authoritative World commit; หากคง World Transition Engine ไว้ ต้องเป็น deterministic function หรือ controlled subcomponent ภายใน World Kernel |
| WM12-I04 | Required verification ทุกตัวต้อง pass และมี durable reference ก่อน authoritative World commit |
| WM12-I05 | Authoritative World version กับ `WORLD_COMMIT_RECEIPT` ต้อง durable และ visible แบบ atomic |
| WM12-I06 | Consequential rejection, denial, failure, conflict, rollback หรือ recovery attempt ต้องมี durable audit receipt แต่ห้ามแสดงเป็น successful state change |
| WM12-I07 | Replay เลื่อน canonical World State เฉพาะ ordered input Event ที่มี valid World commit receipt; historical fact ที่ไม่มี receipt ยังคงเป็น history แต่ไม่เลื่อน canonical state |
| WM12-I08 | Non-root World commit ทุกตัวต้องระบุ expected canonical parent หนึ่งตัว; mismatch ให้ผล `CONFLICT` ไม่ใช่ overwrite |
| WM12-I09 | หนึ่ง `idempotency_key` ภายใน transition scope สร้าง canonical semantic effect, successful `transition_id` และ committed World version ได้อย่างละไม่เกินหนึ่ง |
| WM12-I10 | Event Fabric delivery และ Chronicle projection retry/idempotent ได้; ทั้งสองไม่ใช่หลักฐานของ durable event commit หรือ World commit |
| WM12-I11 | Recovery decision ใช้ durable lifecycle state และ receipts ห้ามใช้ message arrival order หรือ execution acknowledgement เพียงอย่างเดียว |
| WM12-I12 | Chronicle ledger เป็น durable historical authority; Chronicle projection เป็น derived/rebuildable; ทั้งสองไม่เป็นเจ้าของ current World State |

## Crash/failure analysis

| Failure point | Durable facts หลัง crash | Recovery action | ผลลัพธ์ที่ห้ามเกิด |
|---|---|---|---|
| ก่อน event persistence | Candidate อาจไม่มีหรือมีเพียง staged record | Retry ด้วย idempotency identity เดิม หรือบันทึก rejection แบบ durable เมื่อ policy กำหนดและมี identity | World commit หรือ success claim |
| หลัง input event commit ก่อน transition prepare | Canonical input fact มีอยู่; ไม่มี World receipt | Resume หรือ expire แบบ deterministic จาก `event_id` และ policy | Replay เลื่อน World เพียงเพราะ Event มีอยู่ |
| ระหว่าง transition prepare | Input มีอยู่; candidate delta ยัง provisional | Recompute แบบ deterministic จาก parent version และ committed input | ถือ provisional state เป็น authoritative |
| Verification failed | Input และ failure receipt มีอยู่; ไม่มี World receipt | หยุด, compensate external effect เมื่อได้รับ authorization แยก หรือเข้า recovery flow | World version advance |
| Verification passed ก่อน World commit | Input และ verification receipt มีอยู่; ไม่มี World receipt | ตรวจ expected parent ซ้ำและ retry `transition_id` เดิมแบบ idempotent | ถือว่า commit จาก verification เพียงอย่างเดียว |
| ระหว่าง World commit + receipt | Atomic transaction ต้องไม่มีหรือครบทั้งชุด | ตรวจ transaction/commit marker; retry เฉพาะ IDs และ expected parent เดิม | World visible โดยไม่มี receipt หรือ receipt อ้าง World version ที่ไม่มีอยู่ |
| หลัง World commit ก่อน Event Fabric delivery | World version และ receipt มีอยู่; outbox pending | Relay ซ้ำจน acknowledged; consumer deduplicate | Apply transition ซ้ำจาก delivery retry |
| ระหว่าง Chronicle projection | Ledger, World version และ receipt มีอยู่; projection อาจ stale | Rebuild จาก ordered Chronicle records และ receipts | ถือ projection lag เป็น history loss หรือแก้ World จาก projection |
| Chronicle unavailable ก่อน required durable record | ยังไม่มี durable prerequisite ที่ยืนยันแล้ว | Fail closed, เปิด durability incident และ retry ตาม bounded policy | Authoritative World commit |
| Parent World version เปลี่ยนจาก concurrency | Input/verification อาจอิง stale parent | Emit `CONFLICT`; re-prepare และ re-verify เมื่อ policy อนุญาต | Silent last-write-wins overwrite |

External action อาจเกิดขึ้นแล้วก่อน Veda verify หรือ commit modeled state ข้อเสนอนี้ไม่อ้างว่าสามารถ rollback external reality ได้ กรณีดังกล่าวต้องบันทึก observed external result และเข้าสู่ compensation/reconciliation ภายใต้ authorization แยก ห้าม bypass World verification หรือทำ audit trail เท็จ

## การเปลี่ยนเอกสารที่จำเป็นหลัง owner approval

รายการต่อไปนี้ยังไม่ได้รับอนุญาตให้แก้เพียงเพราะมี proposal นี้

### RFC changes

1. `docs/rfc/RFC-0001-constitution.md`
   - นิยาม `Commit` ใน P6 เป็น commitment ของ operation outcome/authoritative state decision ไม่ใช่ event delivery
   - ระบุว่า durable pre-commit fact และ audit receipt มีอยู่ได้โดยไม่แปลว่า success
   - คง P6 และ I6; ห้ามลด verification-before-commit
2. `docs/rfc/RFC-0001A-permission-matrix.md`
   - แยก terminal `Audit` เดิมเป็น permission-decision receipt และ execution/verification/commit receipts ภายหลัง
   - คงข้อกำหนดว่าทุก permission evaluation ต้อง auditable
3. `docs/rfc/RFC-0002-world-model.md`
   - แทน unresolved WM-12 paragraph ที่ line 809 ด้วย ordering และ invariants ที่ owner อนุมัติ
   - คง Draft จนกว่า independent RFC acceptance gate ผ่าน
4. `docs/rfc/RFC-0003-event-model.md`
   - แทน `COMMITTED` ที่ไม่ระบุชนิดด้วย `EVENT_COMMITTED`
   - นิยาม staged/quarantine record แยกจาก canonical Chronicle Event
   - Validate ก่อน canonical event commit และแยก transition-admissibility validation จาก event validation
   - บังคับ durable audit สำหรับ consequential reject/failure
   - กำหนด Event Store เป็น Chronicle-owned durable ledger หรือระบุ containment relationship ให้ชัด
   - แยก Chronicle ledger จาก Chronicle projection
   - แก้ reference lifecycle/reference event ไม่ให้ `world_version_after` หรือ verification data อ้างผลก่อนเกิดจริง
5. `docs/rfc/RFC-0004-state-and-world-transition.md`
   - นิยาม `E` เป็น `EVENT_COMMITTED` และอธิบาย revalidation เป็น World-transition admissibility
   - ระบุ World Transition Engine อยู่ภายในหรือทำงานภายใต้ sole commit authority ของ World Kernel
   - แทน `COMMIT` ที่ไม่ระบุชนิดด้วย `WORLD_COMMIT` เมื่อเปลี่ยน authoritative state
   - ยกระดับ atomic World version + commit receipt จาก `SHOULD` เป็น `MUST`
   - Normalize commit protocol, write-ahead strategy, rollback, replay และ crash recovery ตาม receipt model ที่อนุมัติ
6. Downstream RFC impact review
   - Audit และ align `RFC-0026`, `RFC-0027`, `RFC-0031` และ `RFC-0032` หลัง RFC-0001 ถึง RFC-0004 ได้รับอนุมัติ
   - ห้ามเริ่ม SPEC หรือ implementation จาก proposal นี้

### ADR changes

1. `docs/adr/ADR-0001-world-kernel.md`
   - งานนี้ไม่ต้องและไม่อนุญาต semantic change
   - Traceability correction เป็นอีก gate หนึ่งและห้ามดำเนินต่อในงานนี้
2. `docs/adr/ADR-0002-event-fabric-chronicle.md`
   - ห้ามแก้ความหมาย Accepted ในที่เดิม
   - Alternative A รักษา Event Fabric / Chronicle / World Kernel boundary และ projection-recovery semantics
3. ADR ใหม่หลัง RFC approval
   - สร้าง ADR ใหม่โดยให้ governance กำหนด identifier เพื่อบันทึก ordering, lifecycle vocabulary, atomic receipt boundary และ failure model ที่อนุมัติ
   - ถ้าเลือก Alternative A ให้ ADR ใหม่ extend ADR-0001/ADR-0002 แทนการ supersede
   - ถ้าเลือก Alternative B ต้อง supersede semantic ส่วนที่ได้รับผลใน ADR-0002 อย่างชัดเจน พร้อม migration/compatibility impact

### Constitution

ไม่เสนอแก้ Constitution การ normalize RFC ต้องระบุว่า constitutional `Event` หลัง `Commit` คือ committed outcome event/delivery path ส่วน durable Event ก่อนหน้านั้นเป็น truthful fact ของ proposal, authorization, execution, observation และ verification หาก owner ไม่ยอมรับการตีความนี้ WM-12 จะปิดไม่ได้โดยไม่มี constitutional amendment ที่ผ่าน governance แยกต่างหาก

## หลักฐานที่ต้องมีเพื่อปิด WM-12

WM-12 เปลี่ยนจาก `OPEN` เป็น `RESOLVED` ได้ต่อเมื่อมีหลักฐานครบทุกข้อ:

1. Owner เลือก ordering alternative อย่างชัดเจนหรืออนุมัติ canonical ordering ฉบับแก้
2. คำทั้งแปดใน proposal นี้มีนิยามโดยไม่มี `commit` ที่กำกวม
3. RFC-0001, RFC-0001A, RFC-0002, RFC-0003 และ RFC-0004 มี normative order เดียวกัน
4. World Kernel ยังคงเป็น sole authoritative World commit owner
5. Required verification มี durable reference ก่อน World commit
6. World version และ World commit receipt มี mandatory atomic visibility boundary
7. Consequential reject/failure path มี mandatory durable audit receipt
8. Replay, idempotency, conflict และ crash-recovery ผ่าน cross-document audit
9. Semantic ของ Accepted ADR-0001/ADR-0002 ไม่เปลี่ยน หรือมี ADR ใหม่ที่อนุมัติ/supersede อย่างชัดเจน
10. Owner ยืนยันการตีความ constitutional post-commit `Event -> Chronicle` ตามข้อเสนอ หรือ constitutional amendment แยกแก้เรื่องนี้
11. Governance validation รายงานว่าไม่มี unresolved P0 ordering contradiction
12. การเปลี่ยน RFC status เกิดผ่าน approval process ของแต่ละ RFC เท่านั้น และ RFC-0002 คง Draft จนกว่า process นั้นเสร็จ

## Owner decision required

Owner ต้องเลือกหนึ่งทาง:

- อนุมัติ Alternative A เป็นฐาน RFC normalization โดยคง WM-12 เป็น OPEN จนแก้เอกสารและ validate ครบ;
- เลือก Alternative B และอนุญาตให้เตรียม superseding ADR proposal ใหม่ โดยไม่แก้ Accepted ADR เดิม;
- ขอ alternative ฉบับแก้; หรือ
- ปฏิเสธ proposal

Silence, continued review หรือ acceptance in principle ไม่ใช่ owner approval

## Next permitted action

ก่อน owner decision อนุญาตเพียง review proposal นี้และแก้ proposal-only defect ห้ามเปลี่ยน RFC/ADR status, แก้ Accepted ADR, เริ่ม SPEC/implementation, commit หรือ push

หลัง owner เลือกอย่างชัดเจน ขั้นถัดไปที่อนุญาตคือ scoped patch proposal สำหรับ Draft RFC-0001, RFC-0001A, RFC-0002, RFC-0003 และ RFC-0004 แล้วทำ independent cross-document governance audit งาน ADR ยังคง gated จน RFC layer ได้รับอนุมัติ
