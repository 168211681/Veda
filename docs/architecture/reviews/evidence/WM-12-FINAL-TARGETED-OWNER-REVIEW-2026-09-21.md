# VEDA — Final Targeted Owner Review Delta

Status: REVIEW ONLY — RFCs remain Draft; WM-12 OPEN; RFC Freeze BLOCKED

Baseline evidence commit: `95c956650f54c2a0e435f2dcf6988b93e21149c5`

## Repository baseline

- Branch: `main`
- HEAD: `95c956650f54c2a0e435f2dcf6988b93e21149c5`
- The working tree contains pre-existing uncommitted normalization of RFC-0001 through RFC-0004 and existing untracked architecture/review artifacts. Those files are preserved and are not included in the evidence-only commit.
- Constitution and accepted ADRs are unchanged.

## Exact latest diffs

The following artifacts are complete `git diff HEAD -- <file>` outputs, not summaries:

- [RFC-0001A latest diff](RFC-0001A-latest.diff)
- [RFC-0004 latest diff](RFC-0004-latest.diff)

The diffs include the previously reviewed Alternative A normalization plus the two later targeted corrections. They do not modify the Draft RFC source files in this evidence commit.

## Source hashes

SHA-256 of the five current proposed RFC files:

| File | SHA-256 |
|---|---|
| `docs/rfc/RFC-0001-constitution.md` | `88e36fa61fcc6b26ffc9104c6d0a0f4ef68a5217e71e1a1dac0af11d82b415ed` |
| `docs/rfc/RFC-0001A-permission-matrix.md` | `57f7583ff64a21f5192b4a8a5e090073686d8a4fe1f81ed733de4b510a890fba` |
| `docs/rfc/RFC-0002-world-model.md` | `4db574002195e40ff21eb11a4d82565a58bb1b443a0fd040fbca1bcdec12e6e0` |
| `docs/rfc/RFC-0003-event-model.md` | `8181259ffe6cbeab811c7ed553f4dde9a74e7db618240fe936541b4142ff32a2` |
| `docs/rfc/RFC-0004-state-and-world-transition.md` | `488364d09535c0de039b84046adc20c9f21d645c5b356bfd4fd89771f15cca6c` |

The prior owner-review package recorded the pre-correction worktree hashes `393e8d5933a2859ecf59d13936716636afadf3ddbdf274337ec3acf52bfd299` for RFC-0001A and `ec6b83a60a1c720ac948136bef1706a9a9a22dd4cbd63b503753f82642862e28` for RFC-0004.

## Finding 1 — RFC-0001A

Before: the pre-normalization HEAD pipeline listed `REQUIRE_APPROVAL` but did not make its non-authorizing pending semantics explicit (`HEAD` lines 101–109, 605–612).

After: `REQUIRE_APPROVAL` is explicitly pending and blocks capability grant/execution until valid approval, revalidation, and a durable permitting receipt exist (`docs/rfc/RFC-0001A-permission-matrix.md:117-121`, `:636-645`). `NOTIFY` only executes where policy already permits; `DENY`, expiration, revocation, and out-of-scope results remain non-authorizing (`:119`, `:699`).

This preserves Alternative A: permission facts remain pre-execution factual records, while execution, verification, World commit, and successful outcome remain later lifecycle stages (`RFC-0001A:117-121`, `RFC-0003:795-848`).

## Finding 2 — RFC-0004

Before: HEAD said “Rollback MUST itself create events” without preventing a failed rollback from being interpreted as success (`HEAD:475-486`). The prior Alternative A normalization also required a receipt and successful outcome event too broadly.

After: a rollback/recovery attempt always creates durable factual/audit evidence, but a `WORLD_COMMIT_RECEIPT` and successful outcome event are allowed only after Execution → Observation → Verification, applicable parent-bound verification, and successful World Kernel atomic commit (`docs/rfc/RFC-0004-state-and-world-transition.md:484-502`). Failed, rejected, conflicted, or indeterminate correction produces truthful failure/recovery-required evidence and never successful completion.

This preserves Alternative A's atomic triple and post-commit event ordering (`RFC-0002:678-690`, `RFC-0004:391-417`, `:929-990`).

## Unrelated semantics

The targeted correction changes only the two owner-identified ambiguities. No Constitution, accepted ADR, RFC status, World Kernel authority boundary, verification binding, CAS/stale-verification rule, atomic triple, Chronicle authority, or idempotent delivery rule was changed by this correction. Existing unrelated worktree modifications remain unstaged.

## Validation and limitations

- `git diff --check`: PASS.
- Complete latest RFC-0001A and RFC-0004 diff artifacts generated against HEAD: PASS.
- Current source hashes and referenced paths checked: PASS.
- Constitution and ADR hashes equal their HEAD hashes: PASS.
- No documentation linter/build tool was available; validation is structural/manual.
- No runtime, crash-injection, storage-atomicity, external-effect idempotency, or implementation proof exists in this repository.
- This package is evidence only; it is not owner approval, RFC acceptance, WM-12 closure, or RFC Freeze completion.

## Owner decision required

Choose: approve this exact Draft correction for continued governance review, request further scoped changes, reject the normalization, or defer. No Draft RFC source is staged or pushed by the evidence commit.
