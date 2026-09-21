# WM-12 Alternative A — Final Design Review

Date: 2026-09-21

Status: REVIEW ONLY — OWNER DECISION REQUIRED

Finding: WM-12 (P0 OPEN)
RFC Freeze: BLOCKED

เอกสารนี้เป็นผลการทบทวน Alternative A โดยไม่ถือเป็น owner approval, RFC acceptance หรือหลักฐานว่า WM-12 resolved

## Executive conclusion

Alternative A ทำได้ทางเทคนิค แต่ยังไม่พร้อมถือว่า conform โดยไม่มีเงื่อนไข และ WM-12 ต้องคง `OPEN`

มีเงื่อนไขค้างอยู่ 4 ข้อ:

1. Constitution วาง `Verification -> Commit -> Event -> Chronicle` ตามตัวอักษรที่ `docs/architecture/ARCHITECTURE_CONSTITUTION.md:178-204` แต่ Alternative A commit input/verification Event เข้า Chronicle ก่อน World commit ที่ `docs/architecture/WM-12-EVENT-WORLD-COMMIT-ORDERING-PROPOSAL-2026-09-21.md:77-98`
2. Alternative A ไม่แสดง `Execution -> Observation` อย่างชัดเจน ทั้งที่เป็น constitutional lifecycle
3. Atomic boundary ครอบเฉพาะ World version กับ receipt แต่ยังไม่บังคับให้ delivery outbox อยู่ใน boundary เดียวกัน
4. Verification receipt ยังไม่บังคับให้ผูกกับ exact parent version, candidate hash และ verification-policy version จึงเสี่ยงนำผล verification ที่ stale กลับมาใช้

## Authority interpretation

- Chronicle เป็น authority ของประวัติว่า Veda บันทึกอะไรไว้ ไม่ใช่ authority ว่า external reality จริงหรือไม่ และไม่ใช่ current World authority ตาม `docs/adr/ADR-0002-event-fabric-chronicle.md:201-229` และ `docs/adr/ADR-0002-event-fabric-chronicle.md:741-759`
- `EVENT_COMMITTED` หมายถึง immutable historical fact
- `WORLD_COMMITTED` หมายถึง authoritative modeled-state transition ที่ World Kernel เท่านั้นทำได้ ตาม `docs/adr/ADR-0001-world-kernel.md:79-105`
- Input Event ที่ไม่มี valid World commit receipt ต้องอยู่ใน history ได้ แต่ห้ามเลื่อน canonical World State
- การแก้ข้อเท็จจริงย้อนหลังต้องใช้ correction/invalidation Event ใหม่ ห้าม rewrite ตาม `docs/adr/ADR-0002-event-fabric-chronicle.md:591-624`

## Failure-boundary review

| Failure boundary | Persisted state | Authoritative World version | Recovery action / audit evidence | Duplicate execution | Required invariant |
|---|---|---|---|---|---|
| หลัง create ก่อน durable write | ไม่มีหรือมี non-canonical staging | `Wn` | Retry ด้วย identity เดิม; ยังห้ามอ้าง success | Candidate ซ้ำได้ | Creation ไม่มี authority |
| หลัง validation ก่อน input commit | ไม่มี canonical Event | `Wn` | Validate และ conditional append ใหม่ | Validation ซ้ำได้ | Unique `event_id`; validation result ที่ยังไม่ durable ใช้ authorize ไม่ได้ |
| หลัง input Event commit | Immutable input fact | `Wn` | Resume หรือ append `EXPIRED/REJECTED` | Transition computation ซ้ำได้ | `EVENT_COMMITTED != WORLD_COMMITTED` |
| ระหว่าง transition prepare | Input Event; candidate อาจสูญหาย | `Wn` | Recompute จาก input และ exact parent | Computation ซ้ำได้ | Candidate เป็น provisional และ deterministic |
| หลัง external execution ก่อน observation | Input และ execution identity; external effect อาจเกิดแล้ว | `Wn` | Observe/reconcile ก่อน retry; append result/failure | External effect อาจซ้ำ | Stable `action_id`; tool idempotency หรือ explicit compensation policy |
| หลัง observation ก่อน verification | Input และ observed-result fact | `Wn` | Verify จาก durable observation | Verification ซ้ำได้ | Observation ไม่เท่ากับ verified outcome |
| Verification fail | Input + durable failure receipt | `Wn` | Stop; compensation เป็น action ใหม่ที่ต้อง authorize | ห้าม retry execution แบบ blind | Failure receipt ห้ามถูกตีความเป็น success |
| Verification pass ใน memory ก่อน receipt | Input เท่านั้น | `Wn` | Re-verify; ห้ามใช้ in-memory result | Verification ซ้ำได้ | Verification ใช้ commit ได้เมื่อ durable เท่านั้น |
| หลัง verification receipt ก่อน World commit | Input + verification receipt | `Wn` | CAS parent; retry transition เดิม หรือ emit conflict | Commit request ซ้ำได้ | Receipt ผูก `parent_version + candidate_hash + policy_version` |
| Parent เปลี่ยนก่อน commit | Input + stale verification + newer World | เวอร์ชันของ concurrent commit | Append `CONFLICT`; re-prepare และ re-verify | ห้าม reuse stale candidate | Parent mismatch ห้าม commit |
| ระหว่าง World commit | ต้องเห็นไม่มีทั้งหมดหรือครบทั้งหมด | `Wn` หรือ `Wn+1` | Query ด้วย `transition_id`; retry identity เดิม | Request ซ้ำได้ แต่ commit ซ้ำไม่ได้ | World version + receipt + delivery intent atomic |
| หลัง World commit ก่อน delivery | World, receipt และ outbox ต้อง durable | `Wn+1` | Relay จาก outbox/receipt | Delivery ซ้ำได้ | Crash ห้ามทำให้ committed outcome สูญหาย |
| หลัง delivery ก่อน acknowledgement | World + receipt + pending/uncertain delivery | `Wn+1` | Redeliver | Delivery ซ้ำได้ | Consumer inbox/`processed_event_id` ป้องกัน semantic execution ซ้ำ |
| ระหว่าง projection update | Ledger และ World commit ครบ; projection อาจ partial | `Wn+1` | ล้าง/rebuild derived projection จาก ordered receipts | Projection write ซ้ำได้ | Projection ไม่มี authority และต้อง idempotent |
| Rejection/denial ก่อน audit append | อาจไม่มี durable decision | ไม่เปลี่ยน | ต้อง reevaluate; ห้ามถือ decision final | Evaluation ซ้ำได้ | Decision visibility กับ audit receipt ต้อง atomic |
| Chronicle unavailable | ไม่มี durable prerequisite ใหม่ | ไม่เปลี่ยน | Fail closed; durability incident | Retry ได้ | `NO DURABLE RECORD -> NO AUTHORITATIVE COMMIT` |

## Crash, replay, and recovery conclusions

- Replay ต้องใช้ `WORLD_COMMIT_RECEIPT` เป็นตัวเลือก transition ที่ authoritative ไม่ใช่ apply ทุก Chronicle Event
- Receipt ต้อง uniquely map `transition_id -> input event(s) -> verification receipt -> parent -> resulting version`
- ถ้าพบ input หรือ verification โดยไม่มี World receipt ให้ถือเป็น incomplete/rejected historical path ไม่ใช่ state mutation
- ถ้าพบ World version โดยไม่มี receipt ถือเป็น invariant violation ระดับ P0
- Recovery ต้อง append `CONFLICT`, `RECOVERY_REQUIRED`, correction หรือ compensation Event ใหม่ ห้ามแก้ Event เดิม
- Idempotency ของ Event Fabric ตาม `docs/adr/ADR-0002-event-fabric-chronicle.md:502-523` ป้องกัน World mutation ซ้ำได้ แต่ไม่ป้องกัน external tool execution ซ้ำโดยอัตโนมัติ

## Exact Constitution consistency

Alternative A สามารถสอดคล้องได้เฉพาะเมื่อมี authoritative interpretation ว่า:

- constitutional `Commit` คือ authoritative operation/World commitment;
- constitutional post-commit `Event -> Chronicle` คือ successful outcome Event และการเผยแพร่ผล;
- pre-commit records เป็น truthful proposal, authorization, execution, observation หรือ verification facts และไม่กล่าวอ้างว่า action สำเร็จ

การตีความนี้สอดคล้องกับ Event Integrity ซึ่งอนุญาตให้ Event แทน fact ที่เกิดจริง แต่ห้ามแทน intention เป็น completed action ที่ `docs/architecture/ARCHITECTURE_CONSTITUTION.md:322-338`

อย่างไรก็ตาม Constitution ไม่ได้เขียน distinction นี้ไว้ชัดเจน จึงยังไม่สามารถถือว่าการตีความดังกล่าวได้รับอนุมัติแล้ว

## Accepted ADR compatibility

Alternative A ไม่จำเป็นต้องเปลี่ยน semantic ของ Accepted ADR หากรักษาเงื่อนไขต่อไปนี้:

- World Kernel เป็น sole World authority
- Chronicle เป็น immutable historical authority
- Chronicle ไม่มีอำนาจตัดสิน external truth หรือ commit World
- Event Fabric delivery อาจซ้ำ สูญหาย หรือ reorder ได้โดยไม่ทำให้ state mutation ซ้ำ
- World/projection recovery ใช้ Chronicle และ receipts โดยไม่ rewrite history

เงื่อนไขเหล่านี้ตรงกับ `docs/adr/ADR-0001-world-kernel.md:276-324` และ `docs/adr/ADR-0002-event-fabric-chronicle.md:655-759`

## Minimum normative corrections

1. เพิ่ม explicit `EXECUTION -> OBSERVATION` ใน Alternative A
2. เปลี่ยน atomic invariant เป็น `World version + WORLD_COMMIT_RECEIPT + committed-event delivery intent`
3. บังคับ verification receipt ให้ผูกกับ exact parent, candidate hash, evidence set และ policy/version
4. บังคับ permission/rejection decision visibility กับ audit append ให้ atomic
5. กำหนดว่า stale verification ห้าม reuse หลัง parent conflict
6. Normalize Draft RFC-0003/RFC-0004 ให้แยก Event commit, World commit และ Chronicle authority อย่างชัดเจน

## Owner decision required

Owner ต้องตัดสินอย่างชัดเจนว่า constitutional `Event -> Chronicle` หมายถึง post-commit outcome path และอนุญาต pre-commit truthful fact records หรือไม่

- ถ้าใช่: Alternative A สามารถเดินหน้าสู่ Draft RFC normalization หลังแก้ invariants ข้างต้น โดยยังคง WM-12 `OPEN`
- ถ้าไม่ใช่ และ Constitution หมายถึงห้าม Event persistence ทุกชนิดก่อน commit: Alternative A ขัด Constitution และต้องใช้ constitutional amendment ผ่าน governance แยกต่างหาก

ยังไม่ควร mark WM-12 resolved หรือเข้าสู่ SPEC/implementation
