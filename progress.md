
## Task 6 (BUG-ACC-005, BUG-ACC-020, BUG-ACC-021)
- Implementer: DONE_WITH_CONCERNS. abs-api f2335c2; abs-ms a25a4e5 (005), 902fb68 (021),
  2ad46e7 (020). `make test` + `go vet` green with a new `TestNextCode_PreviewsWhatCreateWouldAssign`
  and a router-conflict check; abs-ms check:fix / lint / typecheck all clean.
  All three defects confirmed to reproduce before any edit; none already fixed, none differing
  from its description (020 partial exactly as briefed).
- Concern 1: no code RESERVATION, by necessity — `NextCode` derives the running number by
  COUNTING the year's rows, so a held-back number is handed out again. Reservation is impossible
  without replacing the numbering rule and the spec states no replacement (nothing on gaps,
  monotonicity or reservation lifetime). Built a read-only preview endpoint labelled
  `(cấp khi lưu)`. Gapped/reserved numbering would need `spec/ACC-01.html` + PL-C first.
- Concern 2 (DATA LOSS, disclosed): entering Loại 3 coerces every row to value-only and clears
  ĐVT/SL/đơn giá/thành tiền — required by migration 00080's CHECK — so amounts already typed are
  DISCARDED with no undo, and leaving Loại 3 does not restore quantity mode (spec silent; rule
  not invented). Sent to the reviewer to judge whether a confirm-before-clearing is warranted.
- Concern 3: `z.enum([…]).refine()` needed an explicit `: boolean` return annotation — without it
  zod v4 narrows `""` out of the OUTPUT type and cascades into `useForm`'s resolver generics. It
  looks removable and is not; commented in place.
- Concern 6 (out of scope, severity to assess): the proposal DETAIL page still shows all columns
  for a Loại 3 đề xuất; `new_title` is now an unused i18n key, left rather than removed.

## spec/PL-C task: BLOCKED, not dispatched — surfaced to the user instead
The `spec/` repo is on branch `feature/package-code-by-location` with NINE uncommitted modified
files, including **PL-C.html and ACC-08.html** — precisely the two a PL-C task would edit — plus
`tests/` untracked (where the UAT report itself lives). That is the user's in-flight work.
- Ruling: do NOT dispatch an agent to edit it. Committing or branching over uncommitted work in
  a repo the user is actively editing is exactly the irreversible, outward-facing action that
  requires their say-so. The PL-C entries are collected in this ledger and go to the user as a
  list. Cost if wrong: the authored strings stay un-specced one more cycle, which is visible and
  reversible; the alternative risks their uncommitted spec work.

### Task 6 review (spec ✅ all four requirements, quality: changes needed — 1 Important + 10 Minor)
VERIFIED: `supplyUnit` refuses empty at BOTH halves independently — zod refine client-side, and
`validate:"required,oneof=..."` on the DTO -> 400 before service code, a re-check in `Create` ->
apperr, plus NOT NULL + `ck_acc_proposal_supply`. No client-only guard, no 500 path; Update's
`omitempty` means a blank cannot clear a saved value. Row component genuinely NOT rewritten.
Task 5's panel, `routeDraft` and renamed heading all intact. Grid widths are COMPUTED not guessed
(67rem columns + 7 x gap-2 = exactly 70.5rem). The `: boolean` annotation claim is correct —
TS 5.5+ infers the type predicate, zod 4 narrows the output type from it.

I-1 (Important, DATA LOSS on the very rows this fix targets): the coercion effect
(`proposal-form-dialog.tsx:459-469`, deps `[valueOnly, locked]`) ALSO fires on OPEN — `locked`
starts true while `sessionLocked === undefined` then flips, `valueOnly` flips after
`form.reset(...)` — rewriting loaded rows with `shouldDirty: true`. Every Loại 3 đề xuất created
BEFORE this fix has qty rows (that IS BUG-ACC-005), and nothing server-side prevents them:
`ck_acc_proposal_line_mode` constrains mode vs its own fields, not proposal_type vs mode, and
`buildProposalLines` does no cross-check. So opening an existing nháp Loại 3 draft to fix a typo
wipes ĐVT/SL/đơn giá/thành tiền before the user touches anything, collapses the route preview to
null, and one Lưu destroys it — no undo, no message.
- Ruling: fix BOTH halves — a ref guard so it never fires on open, AND confirm-before-clearing
  for a user-initiated switch that would discard non-empty amounts, reverting `proposalType` on
  cancel via the AlertDialog the file already uses. Keep NOT restoring on the way out of Loại 3
  (reviewer agrees that is the right reading of a silent spec). Cost if wrong: one extra
  confirmation step on a deliberate type change.
- Report correction required: §7.4 says the coercion is "required by the CHECK constraint". The
  CHECK forbids a `value` row still carrying the three fields, so clearing must ACCOMPANY
  coercion — it does not COMPEL the coercion. That is a spec reading and must be stated as one.
Also sent: M-1 (the "must not reserve" assertion cannot fail — the fake returns a fixed code, so
a genuinely reserving implementation would pass too), M-2 (nothing guards the `/next-code` before
`/:id` route ordering; a reorder yields a quiet 400, not a 404), M-8 (staged panel reports
"Chưa có tệp đính kèm." for a proposal that now has one, after a partial failure).
Deferred: M-3 (preview vs concurrent save — accepted, the post-save toast makes a mismatch
visible), M-6 (detail page shows all columns but degrades per row to `—`; follow-up so the two
screens agree), M-7, M-9 (UAT-ACC01-8 needs a Xoá dòng click first — pre-existing, note it in the
test notes so it is not filed as new), M-10.

### Task 6 fix round 1 (abs-api 8b3feff, abs-ms f6cbe61)
I-1 half 1: the implementer DEVIATED from my prescribed ref guard and DELETED the effect
entirely, moving the rule onto the loại `<Select>`'s handler so "user-initiated" is STRUCTURAL
rather than inferred — no on-open path left to guard. I verified its load-bearing claim myself:
only two `setValue("proposalType", …)` sites exist (the Select handler at :576 and the cancel
revert at :1005) plus `form.reset`. Sent to the re-reviewer to judge equivalence.
I-1 half 2: `countLinesLosingQuantities` (it counted `uom` too, since that is cleared as well)
plus `AlertDialogValueOnlyConfirm` on the same primitives as the PDF-only dialog, with
cancel/Esc/click-outside all reverting the loại.
M-1, M-2, M-8 done; both new Go assertions NEGATIVE-TESTED (removing the fake's Create bump, and
deleting the route, each produce the intended failure). Report §7.4 corrected.

- IMPLEMENTER CAUGHT AN ERROR IN MY OWN DEFERRAL. I had deferred M-10 saying it "goes away once
  I-1 lands". Wrong premise: I-1 REMOVES on-open coercion, so a legacy Loại 3 phiếu — locked or
  unlocked — would have kept its qty rows but had ĐVT/SL/đơn giá HIDDEN. No data loss, but real
  values invisible to the person opening the form to fix them. It therefore split the predicate:
  `typeValueOnly` (loại -> what a NEW row may be) and `valueOnly` (loại AND every line already
  value -> what the grid can HONESTLY draw). Self-healing; UAT-ACC01-2 unaffected. Sent to the
  re-reviewer for a scope and correctness check.
- Concern 4 (honest): neither I-1 nor M-8 is test-covered — abs-ms has no suite. I-1's argument
  rests on `proposalType` having only those two writers; a third would bring the on-open path
  back. I grepped and confirmed today's state; the re-reviewer is checking for other paths
  (a reset elsewhere, defaultValues, a Controller, an uncontrolled input).
- Deferred: concern 5 — the dialog TITLE has the same staleness as M-8 after a
  create-succeeds/submit-fails retry (`DX-… (cấp khi lưu)` for a row that now has a real code).

### Task 6 fix round 1 re-review: all 5 addressed, 1 NEW Important introduced
DEVIATION VERDICT: deleting the effect is EQUIVALENT and arguably cleaner than my prescribed ref
— with exactly two writers confirmed codebase-wide and `form.reset` never routing through the
handler, the ref would have been redundant. No gap found.
PREDICATE-SPLIT VERDICT: correct and in scope; UAT-ACC01-2 traced end to end as still passing
(fresh form: blank line -> affected=0 -> immediate coerce -> valueOnly true -> columns collapse).
M-1/M-2 mechanisms TRACED not trusted: without the fake's Create bump the assertion genuinely
fails; the route test guards existence, which is the right assertion since gin resolves
static-before-wildcard regardless of registration order.

NEW-1 (Important, introduced by the split): `proposal-form-dialog.tsx:906` passes `valueOnly`
— not `typeValueOnly` — to every row, and it drives `modeChangeable`
(`proposal-line-row.tsx:116`). On a legacy Loại 3 with mixed rows, `valueOnly` stays false until
the LAST qty row converts, unlocking the Chế độ picker on rows that were already correct — so a
user can flip a legitimate chi-phí-thuần line back to "Có số lượng" and type SL/Đơn giá into a
Loại 3 document. Nothing server-side catches it (`ck_acc_proposal_line_mode` checks a row against
its own mode only). A business-rule regression against pre-fix behaviour, which force-locked
every row the instant proposalType===3.
- Ruling: drive `modeChangeable` from `typeValueOnly` — lock the picker whenever the loại says
  value-only, independent of how wide the grid can honestly draw — leaving `valueOnly` to govern
  columns. The two predicates answer different questions and must not share a consumer.
Minor: the `:387-390` comment claims click-outside reverts the loại, but Radix's
`AlertDialogContent` hardcodes preventDefault on onPointerDownOutside/onInteractOutside, so an
outside click never closes it at all. Behaviour right, comment describes an impossible path.
