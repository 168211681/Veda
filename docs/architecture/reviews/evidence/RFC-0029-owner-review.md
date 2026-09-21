# RFC-0029 Owner-Authorized Correction — Independent Review Evidence

สถานะเอกสารนี้: **EVIDENCE ONLY — OWNER REVIEW PENDING**

## Baseline และ source

- Repository: `168211681/Veda`
- Baseline HEAD: `a06d8d643616043fc5505df11a3a64202fc023b6`
- Branch: `main`
- Source: `docs/rfc/RFC-0029-external-world-interface.md`
- Original source SHA-256: `3d0564f15820ac3270d7a12012dabd3f1d3a0662f35d2f5ac3e2431eb5eaab4f`
- Corrected source SHA-256: `149c09e912339a00aa0d63548236dfdcc1225d25320acedd24bbdb4cc520c662`
- Complete diff: `docs/architecture/reviews/evidence/RFC-0029-owner-review.diff`
- Complete diff SHA-256: `6b0c455746ac121fc67a9890425d0cbef35016885c3feae3a1c3f6865cc67b27`

Diff ถูก regenerate จาก `git diff HEAD -- docs/rfc/RFC-0029-external-world-interface.md` และเก็บแบบเต็มโดยไม่ตัดทอนหรือ normalize whitespace

## สถานะและขอบเขต

RFC-0029 ยังคง `Status: Draft` การแก้ไขนี้เป็นการเตรียม corrected Draft text ตาม F-29-01 ถึง F-29-07 เท่านั้น ไม่ใช่ independent approval, owner approval, RFC acceptance, RFC Freeze, SPEC หรือ implementation authorization

## Traceability F-29-01 ถึง F-29-07

| Finding | Source lines | ผล |
|---|---:|---|
| F-29-01 World authority | 116–120, 900–902, 2083 | PASS — External Interface/adapters เสนอหรือ execute external operation ได้ แต่ World Kernel เท่านั้นที่ commit modeled World |
| F-29-02 External commit distinction | 1194–1198, 2026–2037, EXT-31 (2532–2535) | PASS — external `COMMITTED`/external receipt ไม่ใช่ `WORLD_COMMIT_RECEIPT` |
| F-29-03 EffectReceipt linkage | 1200–1207, 1930–1951 | PASS — provider evidence แยกจาก Veda-owned linkage |
| F-29-04 UNKNOWN/reconciliation | 638–649, 653–665 | PASS — ต้อง reconcile, ห้าม blind retry และต้อง fresh verification เมื่อ parent conflict |
| F-29-05 Human approval | 2321–2326 | PASS — เป็น authorization/permission gate ไม่ใช่ World commit authority |
| F-29-06 Event/Chronicle semantics | 896–902, 2223–2233, EXT-34 (2546–2549) | PASS — factual records, atomic commit และ successful outcome แยกกัน |
| F-29-07 Partial/irreversible effects | 1751–1758, EXT-35 (2551–2554) | PASS — failure/partial/UNKNOWN/recovery evidence ต้อง durable และ truthful |

## Acceptance criteria ระดับเอกสาร

| # | ผล | หลักฐาน |
|---:|---|---|
| 01 | PASS | 1194–1198, 2026–2037 |
| 02 | PASS | 1920–1928, 1930–1933 |
| 03 | PASS | 1935–1951 |
| 04 | PASS | 1200–1207, 1947–1951 |
| 05 | PASS | 116–120, 900–902, 2026–2033, 2321–2326 |
| 06 | PASS | 638–643, 1751–1758 |
| 07 | PASS | 638–649, 653–665 |
| 08 | PASS | 645–649 |
| 09 | PASS | 645–649, EXT-33 (2541–2544) |
| 10 | PASS | 2321–2323 |
| 11 | PASS | 2323–2326 |
| 12 | PASS | 896–902, 2223–2233, EXT-34 |
| 13 | PASS | 2016–2033 |
| 14 | PASS | 2018, 2024, 2032–2033, 2225–2227 |
| 15 | PASS | 1751–1758, EXT-35 |
| 16 | PASS | 638–643, 2032–2037 |
| 17 | PASS | บรรทัด 3 ยังคง `Status: Draft`; upstream hashes ไม่เปลี่ยน |
| 18 | PASS พร้อมข้อจำกัด | เอกสารไม่อ้าง runtime proof; การตรวจนี้เป็น document-level เท่านั้น |

## Provider evidence กับ Veda-owned linkage

Provider-supplied `EffectReceipt` ยังคงมีเฉพาะข้อเท็จจริงที่ external provider รู้และรายงานได้ ส่วน Veda อาจเก็บ `ExternalEffectLinkage` แยกต่างหากสำหรับ:

- stable action/idempotency identity
- candidate transition hash
- exact parent World version
- evidence references
- VerificationReceipt reference
- WORLD_COMMIT_RECEIPT reference

Provider receipt ไม่จำเป็นต้องมี candidate hash, parent World version หรือ VerificationReceipt ของ Veda และไม่ต้องมี future VerificationReceipt ตั้งแต่ตอนสร้าง receipt เดิม การขาดข้อมูลยังคงเป็น `UNKNOWN`; ห้ามประดิษฐ์ provider evidence และไม่เกิด circular dependency

## Human authorization

บรรทัด 2321–2326 กำหนดว่า human approval/denial เป็น authorization และ permission gate ของ external effect ก่อน execution อาจ approve, deny หรือ require review แต่ไม่ใช่ authority สำหรับ modeled World commit

World Kernel เท่านั้นที่ทำ modeled-World CAS commitment และ World Kernel commit ห้าม grant permission สำหรับ external action ที่ human/policy authority ปฏิเสธ

## UNKNOWN, stale parent และ reconciliation

บรรทัด 638–649 กำหนด stable action identity, reconciliation กับ external authority, ห้าม infer success จาก request acceptance, ห้าม blind retry และห้ามอ้าง exactly-once หาก external system ไม่รับรอง

เมื่อ parent World version mismatch ต้อง reject stale attempt, receipt เดิมใช้ไม่ได้, ต้อง prepare candidate ใหม่กับ current parent, fresh verification และ revalidate authorization ตาม policy

## Atomic triple และ outcome ordering

บรรทัด 2005–2037 กำหนดลำดับ canonical:

`Authorization → External Execution → Observation/Reconciliation → Evidence → Verification → VerificationReceipt → Exact-parent check → World Kernel CAS atomic commit → Post-commit successful outcome delivery`

World Kernel ต้องทำให้สิ่งต่อไปนี้ visible พร้อมกันหรือไม่ visible ทั้งหมด:

1. resulting authoritative World version
2. immutable `WORLD_COMMIT_RECEIPT`
3. durable committed-event delivery intent

CAS และ atomic triple เป็น commit boundary เดียวกัน; delivery intent อยู่ภายใน boundary และ actual successful outcome delivery เกิดหลัง boundary

## Downstream dependency findings (read-only)

- RFC-0027: ต้องใช้ reconciliation, corrective transition และ fresh verification ที่ผูกกับ current parent
- RFC-0031: ต้องแยก pre-commit factual records, delivery intent และ post-commit successful outcomes
- RFC-0032: Chronicle เป็น historical authority; reconstruction ต้องอาศัย canonical commit receipts ไม่ใช่ external receipt หรือ projection อย่างเดียว
- ไฟล์ dependency ทั้งหมดไม่ได้ถูกแก้ไข

## Findings และ owner decisions

- P0: ไม่พบ authority contradiction ใหม่ใน corrected text
- P1: ต้องมี independent review และ explicit owner review สำหรับ exact corrected bytes
- P1: downstream propagation ไป RFC-0027, RFC-0031 และ RFC-0032 ยังไม่เสร็จ
- P2: physical atomicity, crash injection, runtime idempotency, external reconciliation และ exactly-once execution ยังไม่มีหลักฐาน runtime
- Owner decision ที่ยังต้องการ: ยอมรับ corrected RFC-0029 bytes สำหรับ governance review หรือขอแก้ไขเพิ่มเติม

## Validation

ตรวจสอบแล้ว:

- Original source hash ตรง expected
- Corrected source hash ตรง expected
- Complete diff hash ตรง expected
- `git diff --check` ของ RFC-0029 ผ่าน
- Markdown structure/fence check ผ่าน
- ไม่พบ local Markdown links ที่เสีย
- Secret-pattern scan ไม่พบ credential/private-key value
- RFC-0029 ยังคง Draft
- Approved upstream RFC hashes ทั้ง 7 รายการไม่เปลี่ยน
- Constitution, Governance, accepted ADRs และ RFC-0027/0031/0032 ไม่ถูกแก้โดยงานนี้
- Existing unrelated working-tree changes ถูกเก็บรักษา

ข้อจำกัด: ผลลัพธ์ทั้งหมดเป็น document-level evidence ไม่ใช่การพิสูจน์ physical atomicity, crash recovery, external-effect reconciliation หรือ exactly-once runtime execution

## Governance state

- RFC-0029: Draft
- RFC-0029 text approval: PENDING
- WM-12: OPEN
- RFC Freeze: BLOCKED
- SPEC: NOT AUTHORIZED
- Implementation: NOT AUTHORIZED

ห้ามตีความเอกสารนี้เป็น independent approval หรือ owner approval

