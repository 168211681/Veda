# RFC-0032 Chronicle Contract Review Plan

สถานะ: **READ-ONLY REVIEW PLAN — ไม่ใช่การอนุญาตแก้ RFC-0032**

## 1. Baseline and source

- Repository: `168211681/Veda`
- Baseline HEAD: `f49b92725472e321ea6684d1618849fce4c209e6`
- Source: `docs/rfc/RFC-0032-chronicle.md`
- Source SHA-256: `822c76209eac7f0efab3f3ecc9ca4449c21700eaa790ccbd63df97f3eea726d2`
- Status: `Draft`
- Scope: independent read-only audit; RFC-0032 source was not modified

สิบ RFC ที่เป็น protected owner-approved sources ตรวจ hash แล้วตรงกับ approval records รวม RFC-0031 ที่ `33e73175439c62172106764f77760f9d2de41e64efdf4d23b84bc88f405eac68` ทุก source อื่นและ Constitution/ADR/Governance ยังคงอยู่นอกขอบเขตการแก้ไขของงานนี้

## 2. Controlling contracts

ข้อกำหนดที่ควบคุมการแก้ไขขั้นต่ำมาจาก:

- RFC-0002 lines 135–151, 426, 680–688: World Kernel เป็นผู้ commit เพียงผู้เดียว; pre-commit facts ไม่ advance World; atomic triple และ receipt lineage
- RFC-0003 lines 24–38, 493, 578–624, 822–830, 848, 997, 1063: factual records แยกจาก successful outcomes; Chronicle เป็น historical authority; canonical receipts ใช้ reconstruction
- RFC-0004 lines 269, 283–289, 414, 500, 947–967: exact-parent verification, one CAS boundary, recovery และ no-history-rewrite
- RFC-0026 lines 1256–1290, 1718–1722: verification ไม่ commit World; receipt distinction และ post-commit gating
- RFC-0027 lines 648–657, 1093–1107, 1536–1559: corrective transition, RecoveryReceipt และ truthful failure
- RFC-0029 lines 2023–2035, 2223–2235: external commit ไม่ใช่ World commit; Chronicle เป็น historical record
- RFC-0031 lines 258–271, 315–324, 2059–2064, 2931–2941: event transport/Chronicle ไม่มี commit authority; conditional receipt lineage
- RFC-0043 lines 1159–1170: factual `DeltaApplied` แยกจาก `DeltaCommitted`

## 3. Findings F-32-XX

### F-32-01 — Timeline ใช้ World Update แบบไม่ระบุ commit authority

- Severity: **P1**
- Evidence: RFC-0032 lines 351–368 (โดยเฉพาะ line 367 `09:07 World Update`)
- Problem: Timeline หลัง Verification ไม่ระบุ exact-parent check, World Kernel CAS, atomic triple หรือ post-commit outcome จึงอาจถูกอ่านว่า Chronicle/Timeline เป็นผู้ทำ World update
- Controlling contract: RFC-0002 lines 680–688; RFC-0031 lines 258–271
- Minimal correction: เปลี่ยน label เป็น historical observation ของ `World Kernel CAS commit` และแสดงผลลัพธ์เป็น `World version + WORLD_COMMIT_RECEIPT + delivery intent` ตามด้วย post-commit delivery; ย้ำว่า timeline เป็น projection
- Owner decision: ไม่ต้องการ decision ใหม่; เป็นการ propagate contract เดิม

### F-32-02 — Reconstruction จาก Snapshot + Validated World Events กว้างเกินไป

- Severity: **P1**
- Evidence: RFC-0032 lines 372–388
- Problem: `snapshot + events = world` และ `Validated World Events` ไม่บังคับ canonical `WORLD_COMMIT_RECEIPT` กับ bound inputs; factual/validated event อาจถูกยกระดับเป็น authoritative World
- Controlling contract: RFC-0002 lines 680–688; RFC-0003 lines 599–624, 997
- Minimal correction: แยก historical replay/projection ออกจาก authoritative reconstruction; authoritative reconstruction ใช้ valid canonical commit receipts และ bound transition inputs เท่านั้น; snapshot เป็น materialized historical state
- Owner decision: ไม่ต้องการ decision ใหม่

### F-32-03 — `world_effect=COMMITTED` ไม่มี receipt gate

- Severity: **P1**
- Evidence: RFC-0032 lines 392–408
- Problem: สถานะ `COMMITTED` ใน Chronicle record อาจถูกตีความว่า Chronicle record เองเป็น World commit โดยไม่มี genuine receipt หรือ resulting World version
- Controlling contract: RFC-0003 lines 493, 1235–1236; RFC-0031 lines 315–324
- Minimal correction: กำหนดว่า `COMMITTED` ใช้ได้เมื่ออ้างอิง valid `WORLD_COMMIT_RECEIPT` และ resulting World version; factual `OBSERVED`/`VERIFIED` ไม่ใช่ commitment
- Owner decision: ไม่ต้องการ decision ใหม่

### F-32-04 — Historical state machine ไม่แยก World commit กับ recovery result

- Severity: **P1**
- Evidence: RFC-0032 lines 1147–1169
- Problem: `VERIFYING → COMMITTED` และ `RECOVERY → RECOVERED` ไม่มี World Kernel/receipt gate; `RECOVERED` อาจถูกอ่านเป็น modeled-World recovery success ทั้งที่ยังไม่ commit
- Controlling contract: RFC-0004 lines 500, 947–967; RFC-0027 lines 1093–1107
- Minimal correction: เพิ่ม exact-parent applicability → one World Kernel CAS atomic triple → post-commit outcome ใน successful branch; ระบุว่า recovery result เป็น factual/recovery evidence จนกว่าจะมี commit receipt
- Owner decision: ไม่ต้องการ decision ใหม่

### F-32-05 — `ChronicleReceipt` อาจสับสนกับ `WORLD_COMMIT_RECEIPT`

- Severity: **P1**
- Evidence: RFC-0032 lines 1826–1849
- Problem: ชื่อ receipt และการออกเมื่อ persist สำเร็จยังไม่ระบุว่าเป็น storage acknowledgement เท่านั้น; อาจถูกใช้เป็นหลักฐาน World commit
- Controlling contract: RFC-0002 lines 682–688; RFC-0003 lines 493, 1063; RFC-0031 lines 315–320
- Minimal correction: นิยาม `ChronicleReceipt` เป็น historical persistence receipt ไม่ใช่ `VerificationReceipt` หรือ `WORLD_COMMIT_RECEIPT`; ห้ามสร้าง successful outcome จาก receipt นี้
- Owner decision: ไม่ต้องการ decision ใหม่

### F-32-06 — Historical reconstruction state machine อาจถูกอ่านเป็น mutation

- Severity: **P1**
- Evidence: RFC-0032 lines 2114–2130 (`EVENTS_APPLIED → STATE_VERIFIED → WORLD_RECONSTRUCTED`)
- Problem: คำว่า applied/reconstructed ไม่แยก projection ของอดีตจาก authoritative current World mutation และไม่บังคับ canonical receipt chain
- Controlling contract: RFC-0004 lines 179–181; RFC-0031 lines 2059–2064
- Minimal correction: ระบุว่า state machine เป็น read-only historical projection; authoritative reconstruction ใช้ canonical receipts/bound inputs; ห้ามสร้าง commit authority หรือ execute external effects
- Owner decision: ไม่ต้องการ decision ใหม่

### F-32-07 — Reference flow และ File Deletion example ใช้ Chronicle → World Update แบบกำกวม

- Severity: **P1**
- Evidence: RFC-0032 lines 2270–2305 และ 2309–2341 (โดยเฉพาะ line 2335 `World Update`)
- Problem: Diagram/example ไม่แสดงว่า World Model เป็น historical projection และไม่แสดง atomic triple/post-commit ordering
- Controlling contract: RFC-0031 lines 258–271, 2970–3022; RFC-0043 lines 1159–1170
- Minimal correction: เปลี่ยน flow เป็น Chronicle historical records → candidate context/query; commit เกิดได้เฉพาะ World Kernel; เพิ่ม receipt/intent/delivery labelsใน consequential example โดยไม่สร้าง second commit path
- Owner decision: ไม่ต้องการ decision ใหม่

### F-32-08 — Chronicle API/diagram authority boundary ต้องระบุให้ชัด

- Severity: **P2**
- Evidence: RFC-0032 lines 2028–2049 (`reconstruct_world`, `restore_snapshot`) และ lines 2270–2305 (`HISTORICAL WORLD → World Model`)
- Problem: API/diagram มีความหมายสอดคล้องกับ historical tooling แต่ยังไม่ระบุ read-only/projection boundary ทำให้เกิด implementation ambiguity
- Controlling contract: RFC-0002 lines 745, 778–789; RFC-0031 lines 1309–1312, 2059–2064
- Minimal correction: ระบุว่า APIs เหล่านี้คืน historical projection หรือ candidate context; ไม่ mutate current modeled World และไม่แทน World Kernel commit
- Owner decision: ไม่ต้องการ decision ใหม่

## 4. Compatible contracts to preserve

ต้องรักษา append-only history, correction events, provenance, temporal/bitemporal queries, UNKNOWN/history gaps, replay no-side-effect, selective disclosure, retention, access audit, hash/checkpoint integrity, federation provenance และ distinction ระหว่าง recorded/observed/believed/verified ตาม sections 6, 9–12, 17, 23–28, 48–54, 58–59, 68–78, 97–110, 117 ของ RFC-0032

ส่วนที่ไม่ใช่ defect: sections 9–10, 17, 50, 58–59, 68–72 และ 98 ระบุการไม่ rewrite history, UNKNOWN และ no-side-effect replay อยู่แล้ว และควรรักษาไว้

## 5. Minimal section-by-section correction plan

1. Sections 14–16: ปรับ World Update, reconstruction equation และ `world_effect` ให้ผูกกับ canonical commit receipt
2. Sections 62–63: เพิ่ม commit/recovery gating และแยก factual recovery จาก committed modeled World
3. Sections 95–96: ติดป้าย World history เป็น historical projection; เพิ่ม World Kernel authority note
4. Section 102: แยก `ChronicleReceipt` จาก `WORLD_COMMIT_RECEIPT`
5. Sections 111 และ 115–116: จำกัด reconstruction APIs/state machine เป็น read-only historical reconstruction
6. Sections 118–120: แก้ diagram และ File Deletion flow ให้แสดง exact-parent → one CAS atomic triple → post-commit delivery; รักษา UNKNOWN/CONFIRMED ตามลำดับและไม่ claim external exactly-once
7. Sections 125 และ conformance invariants: เพิ่ม traceability note โดยไม่เปลี่ยน historical semantics

ไม่ควรเพิ่ม protocol ใหม่, transactional rollback ข้ามระบบ หรือ exactly-once external effect

## 6. Proposed document-level acceptance criteria

1. Chronicle persistence/ack ไม่ใช่ World commit authority
2. Pre-commit factual records ไม่ต้องมีและไม่สร้าง commit receipt
3. Successful modeled-World outcome อ้าง genuine validated receipt เท่านั้น
4. `ChronicleReceipt` แตกต่างจาก `VerificationReceipt` และ `WORLD_COMMIT_RECEIPT`
5. Authoritative reconstruction ใช้ canonical receipts และ bound inputs
6. Snapshot/replay/projection ไม่ mutate current World
7. Exact-parent applicability precedes one World Kernel CAS boundary
8. Atomic triple คือ World version + commit receipt + delivery intent แบบ all-or-none
9. Actual delivery อยู่หลัง boundary และ retry ไม่ execute World ซ้ำ
10. UNKNOWN, failed, partial และ recovery-required history คงอยู่แบบ truthful
11. Replay ไม่ execute external effects โดยอัตโนมัติ
12. Irreversible external effects ไม่ถูกอ้างว่าถูก rollback ด้วย history rewrite
13. Diagrams/examples/State machines ใช้ semantics เดียวกับ normative prose
14. ChronicleReceipt/ProofOfInclusion ไม่ confer commit authority
15. Existing append-only, provenance, retention, security และ visibility contracts ยังอยู่
16. ไม่มี claim runtime atomicity, crash recovery, delivery reliability หรือ external exactly-once โดยไม่มีหลักฐาน

## 7. Dependency implications

- RFC-0031: ต้องคง factual pre-commit events, post-commit outcomes และ Chronicle historical authority
- RFC-0002/RFC-0004: เป็น authority สำหรับ canonical receipt reconstruction และ atomic triple
- RFC-0026/RFC-0027/RFC-0029: verification, recovery และ external reconciliation records ต้องเข้า Chronicle โดยไม่ถูกยกระดับเป็น World commit
- Downstream RFCs ที่อ้าง `reconstruct_world`, `World Update` หรือ `ChronicleReceipt` ต้องรับ terminology ใหม่หลัง RFC-0032 patch

## 8. Governance gate matrix

| Gate | สถานะ | หลักฐาน/เงื่อนไขที่ขาด | ผู้ตัดสินใจ | การกระทำถัดไป |
|---|---|---|---|---|
| RFC-0032 source identification | PASS | Source/hash/status ตรวจแล้ว | — | ใช้ plan นี้ review ต่อ |
| Cross-document contract consistency | BLOCKED | ต้องแก้ F-32-01 ถึง F-32-07 | Owner + RFC governance | อนุมัติ patch scope แล้วแก้ Draft |
| Historical authority boundary | BLOCKED | ต้องระบุ Chronicle ไม่ commit World | Owner + RFC governance | Patch sections 14–16, 95–96, 118 |
| Receipt lineage | BLOCKED | ต้องแยก ChronicleReceipt/commit receipt | Owner + RFC governance | Patch sections 16, 102, 111, 115 |
| Replay/recovery safety | BLOCKED | ต้องผูก canonical receipts และ no-side-effect | Owner + RFC governance | Patch sections 48–53, 62, 115 |
| Runtime/physical atomicity proof | NOT APPLICABLE | ยังไม่มี implementation authorization | — | ห้ามอ้างเป็น runtime PASS |
| RFC-0032 acceptance | BLOCKED | Source correction + independent review ยังไม่มี | Veda Project Owner | Prepare owner-authorized correction |
| WM-12 closure | BLOCKED | Downstream contracts/gates ยังไม่ครบ | Governance/Owner | ดำเนิน RFC-0032 และ audit ต่อ |
| RFC Freeze | BLOCKED | RFC-0032 และ downstream propagation ยังไม่ปิด | Governance/Owner | ห้ามประกาศ Freeze |
| SPEC / implementation | NOT AUTHORIZED | ต้องผ่าน acceptance/freeze ก่อน | Owner/Governance | ห้ามเข้า SPEC หรือ implementation |

## 9. Owner decisions and next action

ไม่พบ choice ใหม่ที่จำเป็น: findings F-32-01 ถึง F-32-07 แก้ได้ด้วยการ propagate contracts ที่อนุมัติแล้ว; F-32-08 เป็น clarification ระดับ P2

**Exact next permitted action:** ขอ Owner authorization แก้เฉพาะ `docs/rfc/RFC-0032-chronicle.md` ตามแผนนี้ จากนั้นทำ independent review/evidence gate ใหม่ โดยยังคง RFC-0032 เป็น `Draft`, WM-12 `OPEN`, RFC Freeze `BLOCKED`, SPEC และ implementation ไม่ได้รับอนุญาต
