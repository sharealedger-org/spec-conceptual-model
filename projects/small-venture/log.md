# Small Venture Sample — Session Log

Running log of work done on this project. Compress older entries to 3–5 bullets
when the log grows long. Each entry closes with a **State** line.

---

### Session: 2026-10-28 — Project created and generalized

Discussion with Kip; recorded by Bob.

**Trigger.** A review of the 2021 concepts deck (slides 32–56) raised whether a system should
generate both sides of a shared transaction or store them. Storing independent perspectives means
reconciliation and redundant data. The `tool-git-ledger` session log supplied a small-volume
working example: the ledger is re-derived every run, and a provenance snapshot with a commit stamp
is written when journal size reaches a fraction of available memory.

**Findings used.**
- The generate-versus-store question recurs across git-ledger, hospice, Ledger Lab's access-path
  comparison and the deck, so the rule goes in `framework/`, not in one project.
- The git-ledger books hold two entities as independent journals; `shared_event_id` exists but is
  not validated. This is the independent-record case the framework record warns about.
- The roadmap's Scala-mainline rule does not cover a small-volume Python implementation.

**Decisions.**
1. Create `framework/perspective-derivation-and-persistence.md` as a proposed decision record.
2. Create this project as separate from Hospice, Ledger Lab and Shared Storage. It specifies a
   small venture and references the storage contract without defining it.
3. Python is acceptable for this sample. Scale testing follows in Ledger Lab on the same fixture.
4. The first draft was written around one specific farm partnership. It was then generalized to a
   small venture (partnership, cooperative, cross-border joint venture, member-funded project).
   The project was renamed from `farm-sample` to `small-venture`.
5. Real names and records stay private. Private notes were used only to shape the idea and are not
   committed. Agreements for the real case are developed in parallel and are referenced as recorded
   inputs; this repo does not define them.

**Created.** `README.md`, `spec.md`, `todo.md`, this log.

**State:** Spec drafted and generalized. No fixture files written, no tool changes made, nothing
committed.

---

## 2026-10-28 (continued) — Pledges and the commitment ledger

**Trigger.** Pledges behave like fund accounting in government: a separate set of books. Rather than
invent a model, external guidance was checked first.

**Findings.**
- The VA workbook already uses a separate ledger ID (FIN vs. CMT) for commitment books.
- The U.S. Treasury standard general ledger separates budgetary from proprietary accounts. One event
  (for example delivery) writes entries to both, and releases the commitment as it recognizes the payable.
- Nonprofit and government standard-setter sites were not reachable, so pledge recognition rules are
  unverified.

**Decisions.**
1. Created `framework/ledger-types-and-fund-dimensions.md` (proposed) as the general home, since the
   topic is wider than this project.
2. Pledge in this sample is held in a commitment ledger; recognition in the financial ledger is a
   Rule version tied to the agreement. Spec §3 updated; a two-Rule-version pledge fixture added to the todo.

**State:** Spec and todo updated. Framework record drafted, unverified on recognition rules. Nothing committed.
