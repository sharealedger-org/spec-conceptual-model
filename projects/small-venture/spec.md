# Small Venture Sample — Specification (working draft)

**Status:** Working draft for discussion. Not a ratified standard. All parties, amounts and
dates below are invented for the fixture. Real records stay in private repositories.

---

## 1. Scenario

A small venture is run by a few **members** who pool resources, with one or more **operators**
who run it day to day, and **customers** who buy from it. A member may be an operator. Members may
live in different places and use different currencies. The venture may have a separate legal entity
or may not; that is out of scope (see README, Boundaries).

The same structure describes:

- a two-person business partnership;
- a cooperative whose members subscribe for shares and pay in installments;
- a cross-border joint venture, where one member contributes in a foreign currency; and
- a member-funded project that sells goods or services.

**First fixture:** members **M1** and **M2** (M2 is the operator), customer **C1**, and a local
currency (LCY) with one foreign currency (FCY) used by M1.

**Second variant:** the same venture with a third member **M3**, to show that rules over
contributions work for any number of members and not only two.

## 2. Participants and partitions

| Partition | Members of the partition | Holds |
|---|---|---|
| Venture shared partition | All members | Contributions, acknowledgments, venture purchases and sales that members agree on, distributions, agreement records |
| Customer shared partition (one per customer relationship) | The operator and that customer | Order, delivery, receipt, payment acknowledgment |
| Member private | Each member | The member's cash records, fees, currency valuation, view of its stake |
| Operator private | The operator | Operating costs not shared, margins, internal classifications |
| Customer private | Each customer | The customer's own classification of the purchase |

Rules the sample must show:

1. A shared partition belongs to an authorized relationship, not to an organization. The
   operator has a separate partition with each customer. Members have no access to those unless
   the relationship grants it.
2. A participant's books combine its authorized shared facts with its private facts and rules. A
   shared partition need not balance on its own.
3. Each participant derives only its own perspective (see
   [`perspective-derivation-and-persistence.md`](../../framework/perspective-derivation-and-persistence.md)).

## 3. Events

Each event has an ID, kind, effective date, recorded time, source, authorizing participant,
status and, where relevant, a link to a Commitment.

| Event | Parties | Shared? | Notes |
|---|---|---|---|
| Agreement recorded | Members | Shared | A versioned agreement record and each party's acknowledgment. The ledger stores the record, not its legal effect |
| Contribution proposed | Member | Shared, as a claim | The member states a contribution: what, how much, when, in what currency |
| Contribution confirmed | Receiving party | Shared | The receiver acknowledges it and records what was received |
| Transfer or conversion fee | Contributing member | **Private to that member** | A cost to the member, not part of the amount the venture received. Whether it counts toward the member's contribution is an agreement term, expressed as a Rule |
| Venture purchase | Operator | Shared (if members are authorized to see it) | Inputs, equipment, stock |
| Sale proposed / accepted | Operator, customer | Customer partition | A Commitment is created on acceptance |
| Delivery / receipt confirmed | Operator, customer | Customer partition | Delivery is the operator's claim; receipt is the customer's confirmation |
| Payment acknowledged | Operator, customer | Customer partition | Recording evidence of payment does not transfer money |
| Distribution | Members | Shared | Allocation of venture results under the declared Rule |
| Withdrawal / exit | Member | Shared | Triggers the exit steps in §7 |
| Reclassification | Any | As the owner chooses | An explicit event; never rewrites history silently |

**Claim versus confirmation.** A unilateral claim ("M1 sent 100 FCY on this date") is a legitimate
record. It becomes a jointly confirmed fact when the other party confirms it. The fixture includes
both a confirmed and an unconfirmed claim and shows that only confirmed contributions feed the
ownership Rule.

**Commitments and pledges.** A member can promise a contribution in installments. For example,
M1 pledges 300 in three installments of 100, and only the first has arrived. The system must show
"100 contributed, 200 still pledged". The 200 is an open commitment. It is not money received, and
it is not a debt unless the agreement says it is. A cooperative member who subscribes for shares
and pays in installments has the same shape. This tests Commitment semantics, which the framework
lists as unresolved (commitment reciprocity).

The pledge is held in a **commitment ledger**, separate from the financial ledger, following the
pattern in [`framework/ledger-types-and-fund-dimensions.md`](../../framework/ledger-types-and-fund-dimensions.md)
(proposed). The pledge event creates a commitment-ledger entry. Each confirmed installment reduces it
and posts to the financial ledger. Whether the unpaid 200 is also a receivable in the financial
ledger depends on the Rule version the agreement declares. The fixture includes the same events under
two Rule versions to show only the Rule changes. The open commitment is the commitment-ledger balance
at the cutoff.

## 4. Shared versus private (fixture design)

| Fact | Shared | Private |
|---|---|---|
| Contribution received, amount and date | Members | |
| Transfer fee and the member's FCY cost basis | | Contributing member |
| Ownership share | Derived from a shared Rule over confirmed contributions | Each member's presentation of its own stake, in its own currency |
| Sale price, quantity, delivery date | Operator, customer | |
| Operator's cost of goods and margin | | Operator |
| Customer's own category for the purchase | | Customer |

**The ownership share is calculated by a Rule and never entered by hand.** The ledger stores the
confirmed contributions and a declared, versioned Rule such as "share = a member's confirmed
contributions ÷ total confirmed contributions". The percentage is derived whenever it is needed. If
the agreement says something else, such as fixed equal shares, that is a different Rule version.
The ledger applies whatever Rule the members' agreement declares. It does not decide the legal
share.

## 5. Perspectives to produce

From the same shared events:

1. **Venture books** (operator): cash, stock, costs, sales, member equity.
2. **Member view**: the member's contributions in FCY and LCY, its calculated share, and its own
   fee and valuation adjustments.
3. **Customer view**: payables and receipts from the customer's side.
4. **Consolidated venture view**, if requested: all members' equity, no double count.

Each view declares its cutoff (as-of date), Rule version and Reference Data generation.

## 6. Currency and amounts

Two currencies appear in the fixture. The framework lists amount/currency representation as an
open question. The sample must:

- record the amount sent in the member's currency and the amount received in the venture's
  currency as separate facts;
- keep the conversion basis as Reference Data with a version and date;
- keep valuation in each participant's books private by default; and
- show positions in each currency without silently recomputing history when the rate reference changes.

## 7. Lifecycle and exit

- **While sharing is active:** shared events live in the shared partition only. No automatic
  duplicate exists in each party's stand-alone books.
- **Copy-on-exit:** when sharing ends, every event is copied into each party's stand-alone ledger,
  the shared partition is marked closed, and both parties receive confirmation before it is
  decommissioned. The storage contract defines this operation. It is a defined service obligation,
  not an optional feature.
- **Non-platform counterparties:** an event with a counterparty who is not on the platform is
  recorded in the participant's stand-alone ledger as that participant's own claim. If the
  counterparty joins later, the record can move to a shared partition by agreement of both.
- **Stand-alone conformance:** after copy-on-exit, each participant's ledger alone must produce
  correct balances and reports.
- **Member withdrawal** in a venture of three or more members does not close the venture. It
  closes that member's access and applies the agreement's Rule for what the member is owed, and
  the remaining members' shared partition continues.

## 8. Storage references

This project does not define storage. It **uses** the operations in
[`projects/shared-storage/`](../shared-storage/README.md) and states what the fixture needs.

| Storage operation | Fixture need |
|---|---|
| Append event | Retry-safe: re-sending the same event ID does not create a duplicate |
| Read events by instrument | Venture events, filtered by partition and effective date |
| Read / write reference datum | Conversion rate, ownership Rule, chart mapping, agreement version, each versioned |
| Check commitment status | Pledge fulfilled or open |
| Read position | Per participant, per cutoff |
| Enumerate participants | Members in the venture partition; the operator and a customer in each customer partition |
| Audit read | Who read what and when |
| **Copy-on-exit** (proposed in the storage log, not yet in the contract) | Complete, atomic, confirmed |

The first binding is a Git-backed file implementation. Git history is the durable event record,
but a commit identifies the technical writer, not necessarily the business authorizer. Events
must therefore carry participant identity and authorization evidence separately.

## 9. Acceptance criteria

The first milestone is complete when an independent implementation can, from the fixture files:

1. load the fixture and reject each deliberately invalid example (duplicate event ID, unknown
   reference, invalid lifecycle step, unconfirmed event treated as confirmed);
2. produce the four perspectives in §5 and match expected outputs and control totals;
3. derive each member's ownership share from the Rule and show it change when a contribution is
   added, and again when the three-member variant adds a member;
4. show the open commitment position for the partly fulfilled pledge;
5. keep a member's fee and the operator's cost of goods out of other parties' visible data;
6. produce identical Positions in each supported mode (derive every time, derive and hold, read
   from a persisted cache), per the conformance test in
   [`perspective-derivation-and-persistence.md`](../../framework/perspective-derivation-and-persistence.md);
7. perform copy-on-exit and show that each participant's stand-alone ledger reproduces its
   perspective without the shared partition; and
8. show the three outcomes of a reference-data change (the roadmap's classification-change test).

## 10. Gaps in the current tool

The planned implementation is `tool-git-ledger`. According to its latest session log, it does
not yet:

- hold one shared event per inter-party transaction with per-party projections derived from it
  (its two legal entities are independent journals linked by `shared_event_id`, which is not
  validated);
- carry rule or reference-data versions in the lineage. Its provenance stamp is the commit
  identifier alone (`git rev-parse HEAD`), which does not detect uncommitted changes;
- model claim versus confirmation, commitments, or open commitment positions;
- handle two currencies or conversion reference data;
- enforce participant visibility, authorization evidence, or read audit; or
- implement copy-on-exit.

These are the first tool-side tasks. They are requirements, not completed work. Only the
provenance-stamp item was checked against the code; the rest come from the session log.

## 11. Out of scope

- Legal form, ownership percentages, tax, land or property rights, and regulatory requirements
- Selecting or drafting agreements (developed in parallel; see README)
- Production hosting, cost and operator assignment
- Scale performance (Ledger Lab)
- Phone or messaging interface design beyond what the fixture needs to show who authorized an event
