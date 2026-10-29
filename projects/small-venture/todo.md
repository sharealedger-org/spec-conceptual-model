# Small Venture Sample — Active Task List

Items are tracked here by priority. Move completed items to `log.md` when done.

---

## HIGH — Required before the fixture can be written

- [ ] **Review `spec.md`** with Kip; confirm the scenario, partitions and event list.
- [ ] **Synthetic fixture files** — invented members, operator and customer; an amount in a
  foreign currency and its converted local amount; one confirmed and one unconfirmed contribution;
  one partly fulfilled pledge; one sale to a customer; one reference-data change; a three-member
  variant. Plain JSON/CSV in a fixture package, with expected outputs and control totals. No real
  names or amounts.
- [ ] **Pledge fixture under two Rule versions** — same synthetic events; one version leaves the
  unpaid part only in the commitment ledger, the other also recognizes a receivable. Expected
  output shows only the Rule version changed. Depends on
  `framework/ledger-types-and-fund-dimensions.md` (proposed).
- [ ] **Shared-event representation** — decide how one shared event carries per-party
  projections (relationship to `shared_event_id` in `tool-git-ledger`). Record the decision.
- [ ] **Storage contract gap list** — confirm the operations in `spec.md` §8 against
  `projects/shared-storage/todo.md`.

## MEDIUM

- [ ] **Agreement records** — decide how a versioned agreement and its acknowledgments are
  recorded as inputs, and which Rules read them. Agreements themselves are developed in parallel,
  outside this repo.
- [ ] **Currency representation** — decide how the amount sent, amount received, fee and
  conversion basis are recorded (open framework question).
- [ ] **Ownership Rule format** — a declared, versioned formula over confirmed contributions;
  define inputs, outputs and what happens when contributions are restated.
- [ ] **Period-close record** — decide whether a close snapshot is a derived cache or a new
  authoritative fact (open question in the framework record).
- [ ] **Implementation task list for `tool-git-ledger`** — turn §10 of the spec into issues in
  that repo.
- [ ] **Hand off the fixture to Ledger Lab** — once the Python implementation passes, define the
  scale run and the crossover measurement.

## LOWER

- [ ] **Visibility rules for members over customer partitions** — default is none; define how a
  relationship grants it.
- [ ] **Customer onboarding path** — a customer joining after earlier unilateral claims
  (migration from stand-alone claim to shared fact).
- [ ] **Member exit in larger ventures** — payment Rule on withdrawal, and how the remaining
  partition continues.
- [ ] **Interface notes** — only what the fixture needs to show who authorized an event; broader
  phone/messaging design belongs elsewhere.
