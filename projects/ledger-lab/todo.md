# Ledger Lab — Active Task List

Items are tracked here by priority. Move completed items to `log.md` when done.

---

## HIGH — Project scoping (do first)

- [ ] **Define minimum viable first milestone** — what does ledger-lab implement
  first? Which framework concepts does the first milestone target?

- [ ] **Synthetic dataset** — what dataset drives the initial conformance fixtures?
  The purchase/settlement example from the framework is the candidate first fixture.
  Ledger Lab owns execution; this repo owns the fixture definitions.

- [ ] **Acceptance criteria** — what tests must pass for the first milestone to be
  declared complete?

- [ ] **Environment** — who operates the implementation environment? What platform?

## MEDIUM

- [ ] **Conformance fixture data files** — plain JSON/CSV files representing parties,
  agreements, instruments, events, reference data, rules, and expected outputs.
  This repo defines the fixtures; ledger-lab implements against them.

- [ ] **Decision records for open framework questions** — the framework
  (`framework/open-accounting-framework.md`) names several unresolved questions.
  Each should become a scoped item here or in the framework layer:
  - Event / Movement / Position as separate record types vs. common lineage model
  - Global vs. scoped identifiers
  - Commitment reciprocity
  - Amount / quantity / unit / currency / uncertainty representation
  - Late-arriving and corrected events
  - Rules declaring dependencies and producing auditable generated events
  - Minimum conformance suite definition

## RESEARCH

- [ ] **Apache OFBiz as CKB proof vehicle** — OFBiz is an Apache 2.0 full ERP
  (GL, AR, AP, inventory, order management, HR) with a well-documented data model
  and a complete demo dataset (multiple companies, orders, payments, GL entries)
  that loads from structured XML seed files. The mapping target is well-specified:
  `AcctgTrans` / `AcctgTransEntry` is a degenerate Universal Journal; `GlAccount`,
  `Party`, `Product`, `CostCenter` are natural CKB dimensions. A CKB transformation
  against OFBiz demo data would demonstrate that:
  - chartfields can be derived bottom-up from observed event data rather than a
    pre-specified schema (complements the VA data model-generation idea in ledger-lab);
  - the Event → Movement → Position model in the framework subsumes the OFBiz GL
    entry structure without loss of information;
  - the same processing rules apply to commercial ERP data as to VA government data,
    proving the broader ERP generality of the CKB method.
  The AP/AR cycle, inventory receipt-to-payment, and payroll stubs in the demo data
  provide enough domain variety to make the proof non-trivial. OFBiz seed data lives
  under `framework/accounting/data/` in the OFBiz source tree (Apache license,
  publicly available). Evaluate alongside the VA fixture work in ledger-lab.
