# Ledger Types and Fund Dimensions

**Status:** Proposed. Not ratified. This record collects external evidence and proposes how
the framework could represent pledges, commitments and restricted funds. It does not change any
existing framework definition.

**Layer changed:** Conceptual and logical. It touches the accounting projection (which set of
books an entry lands in) but not the shared event ontology.

---

## 1. The question

A member promises 300 in three installments of 100, and 100 has arrived. The system should show
"100 contributed, 200 still pledged". Where does the 200 live?

It is not money received. It may or may not be a debt, depending on the agreement. Government
accounting, nonprofit accounting and the earlier VA work in this repo all handle this kind of item.
This record asks what those sources do, so that Sharealedger adopts a known pattern rather than
inventing one.

## 2. Three separate axes

The sources below keep three things apart. Mixing them is the main risk.

| Axis | Question it answers | Example values |
|---|---|---|
| **Ledger type** | Which set of books does this entry belong to? | Financial; Commitment (budgetary) |
| **Book / basis** | Under which accounting rules is it measured? | USGAAP, cash basis, a member's own basis |
| **Fund / restriction** | Which pool of resources is it, and what limits its use? | General; restricted to a purpose; held for others |

A pledge is first a **ledger type** question (is it in the financial books yet?). A restricted gift is a
**fund** question (who may spend it, and on what?). A pledge can be both: a restricted pledge.

## 3. Evidence

### 3.1 The VA workbook (our own prior work)

`sources/VA Data Samples and Specs.xlsx` already separates these axes in the Universal Journal:

- `ujLedgerID`: "FIN" for payment data, "CMT" for commitment books built from purchase-order data.
- `ujBookCodeID`: the accounting basis (for example USGAAP).
- `ujCommitmentID`: links entries to the commitment.
- A purchase order creates a commitment event (EV01, "Non Fin Transaction, Ledger = CMT") with
  commitment T-accounts. Later events recognize payables and expenses in the financial ledger.
- Budgets are loaded the same way (EV09).

So the idea "a commitment book is a ledger type, separate from the financial ledger" is already in the
model. It is not new.

### 3.2 U.S. Treasury Standard General Ledger (verified)

The USSGL is published by the U.S. Treasury as a machine-readable file (FY2026, Bulletin 2026-05;
692 accounts, 851 transaction codes). Each account is flagged **Budgetary** or **Proprietary**.
Budgetary accounts track authority to spend and commitments. Proprietary accounts hold the
ordinary financial statements. The file is at
`https://fiscal.treasury.gov/system/files/2026-03/ussgl_tfm_data_part_1.json`.

The commitment chain in the budgetary accounts runs in stages:

| Stage | Account (title) | Meaning in plain terms |
|---|---|---|
| Available | 461000 Allotments - Realized Resources | Money you may commit |
| Committed | 470000 Commitments - Programs Subject to Apportionment | Set aside, not yet promised to anyone |
| Ordered | 480100 Undelivered Orders - Obligations, Unpaid | Promised; goods or services not yet delivered |
| Delivered | 490100 Delivered Orders - Obligations, Unpaid | Delivered; not yet paid |
| Paid | 490200 Delivered Orders - Obligations, Paid | Paid |

How the transaction codes behave (read from the file):

- **B302** (record a commitment): debit 461000, credit 470000. Budgetary accounts only.
- **B306** (record an undelivered order, no advance): debit the available and commitment accounts,
  credit 480100. Budgetary accounts only. Nothing touches the financial statements yet.
- **B308** (undelivered order **with** an advance payment): the same budgetary entries **plus**
  proprietary entries (credit cash, debit advances). One event writes to both sets of books.
- **B402** (delivery of goods or services): debit 480100 and credit 490100 in the budgetary books,
  **and** debit asset or expense and credit accounts payable in the proprietary books.

Two points follow:

1. **One event, entries in both ledger types.** The commitment ledger and the financial ledger are
   not separate processes. A single event produces entries in each, by rule.
2. **The commitment is released at the moment of financial recognition.** On delivery, the
   undelivered order (480100) is reduced while the payable appears in the financial books. The open
   commitment is the balance still sitting in the commitment accounts.

This is the Treasury's pattern for government, where the commitment books exist to enforce a spending
limit. Our purpose is different (showing what is still promised), but the mechanism is the same.

### 3.3 Fund accounting (secondary source only)

The general description comes from a public encyclopedia article, a secondary source. It describes a
fund as a self-balancing set of accounts, used where a donor, grant authority, governing agency or law
limits how resources may be used. Funds are reported as unrestricted or restricted. It lists
general, designated, endowment, current-restricted and agency (custodian) funds. It notes that the
nonprofit net-asset classes were reduced from three to two in a 2016 FASB update.

### 3.4 Not verified

The authoritative nonprofit and government standard-setter sites (FASB, GASB) returned access
errors from this environment, so **the following are not verified against primary sources**:

- how nonprofit standards treat unconditional versus conditional promises to give;
- how government standards classify funds and encumbrances.

Everything in section 5 on recognition is therefore a proposal for discussion. It is general
accounting practice as we understand it, and it must be checked against the standards before it is
relied on.

## 4. Mapping to existing framework concepts

| Source concept | Framework concept | Note |
|---|---|---|
| Commitment account / undelivered order | **Commitment** | Framework: "not a completed event". The commitment ledger is where its open balance is held. |
| Ledger type (CMT, FIN) | A property of a **generated entry**, not of the shared event | Keeps the shared event perspective-independent. |
| Book code (USGAAP) | Existing book dimension | Orthogonal to ledger type. |
| Fund / restriction | A new **dimension on the entry**, set by Rule from the Arrangement | Not a ledger type. |
| Event that writes to both ledgers | **Event** with several **generated entries** | Same Rule, several outputs. |
| Delivery or payment releasing the commitment | **Position** change | The open commitment is a Position derived at a cutoff. |

## 5. Proposed rules

These are candidates. They would be adopted only through community review.

1. **Ledger type is an attribute of a generated entry.** The shared event does not say "commitment" or
   "financial". A declared Rule decides which ledger type each generated entry belongs to.
2. **One event can generate entries in more than one ledger type.** Receiving an installment can
   reduce the commitment ledger and increase the financial ledger in the same Rule application.
3. **Recognition in the financial ledger is a Rule version tied to the Arrangement.** A pledge that
   the agreement makes binding may be recognized as a receivable. A pledge that depends on a
   condition may stay only in the commitment ledger until the condition is met. The ledger applies
   the Rule the agreement declares. It does not decide the legal answer. (To be checked against
   the standards, see 3.4.)
4. **The open commitment is a derived Position.** It equals the balance of the commitment ledger at
   the declared cutoff, not a stored field.
5. **Fund / restriction is a separate dimension.** It is set by Rule from the Arrangement and
   carried on entries in either ledger type. Releasing a restriction is an Event, not an edit.
6. **A commitment is never silently treated as received.** This restates the existing framework rule
   and becomes a test: no Rule may move a commitment balance to the financial ledger without a
   fulfilling Event.

## 6. Application to the Small Venture Sample

The pledge case in `projects/small-venture/spec.md` becomes:

| Step | Shared event | Commitment ledger | Financial ledger |
|---|---|---|---|
| Pledge made (300) | Pledge accepted | +300 pledged by M1 | None, if the Rule version says not yet recognized |
| First installment (100) | Contribution received and confirmed | -100 | +100 contribution |
| Position at cutoff | | 200 open | 100 contributed |

If the agreement makes the full pledge binding, a different Rule version also recognizes a
receivable for the unpaid 200. The shared events and the confirmed contributions stay the same.
Only the Rule version differs. This is the test of rule 3.

## 7. Open questions

1. Is the commitment ledger one ledger per Arrangement, per participant, or per fund?
2. Does the shared partition hold only the Pledge and Contribution events, with each participant
   deriving its own commitment-ledger entries? (Consistent with
   [`perspective-derivation-and-persistence.md`](perspective-derivation-and-persistence.md).)
3. How does a pledge by one party appear as an expected receipt for the other? This is the
   commitment reciprocity question already listed in the framework.
4. Cancellation, forgiveness and expiry of a pledge: are they Events that reverse commitment
   entries?
5. Which fund classes does the small-venture fixture need? Possibly none beyond one general fund.
6. What is the smallest verified source set for the nonprofit and government standards, given that
   the standard-setters' sites were not reachable?

## 8. Next steps

- Check section 5 rule 3 and section 3.4 against primary sources, ideally with a practitioner.
- Add a pledge fixture (synthetic) with two Rule versions to the small-venture project.
- Add the commitment-ledger Position to the Ledger Lab derivation tests.
- Decide whether the VA workbook's `ujLedgerID` values become the starting vocabulary.

## 9. Sources

- `sources/VA Data Samples and Specs.xlsx`: Universal Journal attributes, T Accounts sheets.
- U.S. Treasury, U.S. Standard General Ledger, FY2026 machine-readable file (URL in 3.2).
  Public U.S. government material.
- General description of fund accounting: public encyclopedia article (secondary).
- [`open-accounting-framework.md`](open-accounting-framework.md): Commitment, Event, Position.
