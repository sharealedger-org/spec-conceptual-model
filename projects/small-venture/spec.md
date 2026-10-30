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

## 12. Agents, delegation, and cross-partition roles

**Status:** New section added October 30, 2026. Proposed for discussion; not ratified.

### 12.1 The agent concept

The participant model in §2 defines members, operators, and customers — all external parties to
each other, each acting in their own interest. A real venture also has **agents**: people or
systems that act on behalf of a participant within a declared, bounded scope.

An agent is not itself a participant. The agent does not own a stake or hold a partition. The
agent's actions are attributed to the participant on whose behalf they act, under a written
delegation that is itself a versioned record in the shared partition.

**Examples in the venture fixture:**
- A bookkeeper who prepares journals for M2 (the operator) but does not set operating policy.
- A platform service that writes events on behalf of the operator under an authorized API key.
- A co-founder who can propose contribution events but whose proposals require M1's confirmation
  before they become confirmed shared facts.

The delegation record must state: the agent's identity, the participant on whose behalf they act,
the permitted event kinds and spending limits, the effective period, the confirmation requirements,
and the revocation event type.

### 12.2 Cross-partition roles

An agent may hold delegations from more than one participant. This does not breach the privacy
boundary between those participants: the agent acts under each delegation separately, with only
the access each delegation grants, and cannot combine private information across partitions.

**Example:** A CFO contractor holds a delegation from the LLC (operator) to prepare LLC books and
from the NFP (member) to prepare NFP books. The CFO sees LLC books under the LLC delegation and
NFP books under the NFP delegation. The LLC's vendor pricing is not visible under the NFP
delegation. The two delegations are separate versioned records, each confirmed by the respective
participant.

The framework already distinguishes the technical writer of a commit from the business authorizer
of an event. An agent occupies the space between those two: more than a technical writer (they
exercise financial judgment), less than a participant (they do not own the relationship). The
delegation record makes that scope explicit.

### 12.3 Proposal and approval as event sub-states

The standard event lifecycle in §3 moves from claim to confirmed fact. For governance events —
period close, new agreements, large expenditures — an intermediate state is needed:

| Sub-state | Meaning |
|---|---|
| `proposed` | The agent has prepared and submitted the event for review |
| `approved` | The delegating participant has reviewed and authorized it |
| `confirmed` | The counterparty has confirmed it (or it is an internal event requiring no counterparty) |

A `proposed` event is a legitimate record. It is not a confirmed shared fact. It must not feed
into position calculations or published books. The fixture must include at least one proposed
event that is approved and at least one that is rejected, showing that rejection is itself a
traceable event rather than a deletion.

### 12.4 Delegation as an input to rules

A rule that reads the shared partition may condition on the agent's delegated scope. An event
prepared by an agent outside their declared spending limit must be rejected by the system before
it reaches `proposed` status, not after. The delegation record is therefore reference data with a
version: changing the agent's scope is a new delegation version, effective from a declared date.

---

## 13. Period close as a shared event

**Status:** New section added October 30, 2026. Resolves the open question in
[`perspective-derivation-and-persistence.md`](../../framework/perspective-derivation-and-persistence.md)
for this project's use. Proposed for discussion; not ratified.

### 13.1 The audit reliability problem

The generate-on-demand model (§§1–11) is correct for the **live period**: shared events are
stored once; perspectives are derived as needed. The risk for ongoing relationships is **temporal
drift**: over months and years, rule versions change, reference data is updated, and the
projection engine itself may be replaced. An auditor cannot assess whether today's engine
faithfully reproduces what last year's engine produced from the same events under the same rules.
Auditors require inspectable, authorized records — not a promise that regeneration will reproduce
them.

### 13.2 Period close is a new authoritative fact

At the end of each accounting period, the following sequence produces a **period-close event**
that is stored as a new, authoritative shared fact — not as a cache:

1. **Final derivation.** The projection is run one final time under declared rule and reference
   data versions, against all events up to and including the declared cutoff.
2. **Agent preparation.** The authorized agent (e.g., bookkeeper or CFO) reviews the output and
   proposes it with sub-state `proposed`.
3. **Participant approval.** Each participant with a stake in the period reviews and approves,
   moving the event to `approved`. For a two-member venture, both M1 and M2 must approve.
4. **Shared event recorded.** The `approved` close event is written to the shared partition as a
   confirmed fact, with sub-state `confirmed`.

The close event carries:
- the **event cutoff** — the ID of the last event included in this period
- the **rule versions** in effect at close — ownership formula, chart mappings, reference data
  generation, identified by version and effective date
- the **control totals** — the agreed balances for each party, each account, each currency
- the **confirming participants** and their authorization timestamps
- a **link to the generated journal** — the complete set of entries both parties accept as their
  books for the period

### 13.3 The control total is the system

The control total is not a check on the system. It is the authoritative shared fact produced by
the close event. Both parties agree the total is correct because both parties confirmed the events
that produced it and then authorized the close.

This resolves the audit confirmation problem: an auditor does not send a letter asking "do you
owe M1 X as of December 31?" The balance for that period is the control total in the close event,
confirmed by both parties in the shared partition. The audit confirmation is structurally
replaced by mutual authorization at the time of close — a stronger guarantee than a retrospective
letter, because it was agreed at the time rather than recalled after the fact.

### 13.4 Immutability and corrections

A confirmed close event is immutable. It cannot be invalidated by later events, rule changes,
or software updates. The stored control totals govern; regeneration from events is a **verification
tool**, not the source of authority after close.

If a correction is needed after close:
- A new correcting event is recorded in the **next** period with explicit links to the prior
  period's close event.
- The prior close event is never modified.
- The audit trail is: current period's close references prior close; the chain of close events
  from the first period to the present is the complete, authorized financial history.

### 13.5 Close events chain

Over the life of a relationship, close events form a **chain**:
- Each period's opening balance is derived from the prior period's close event.
- The audit trail for any current balance traces through N close events, each confirmed by both
  parties, back to the first event in the relationship.
- Software replacement or rule version updates cannot silently alter historical closes, because
  the close events carry the control totals that were agreed at the time.

### 13.6 Period close as the termination of generate-on-demand

Within a period, positions are derived on demand and may be cached for performance. At period
close, the final derivation is authorized and stored as a new authoritative fact. This is the
**generate-to-store transition** for normal operations — distinct from copy-on-exit (§7), which
is the generate-to-store transition when the relationship itself ends.

The fixture must include at least one complete period with a close event, showing the chain of
events → derivation → proposed close → approved close → confirmed close event stored in the
shared partition, with the next period's opening balance derived from it.

---
