# Open Accounting Framework

**Status:** Working conceptual proposal for community review. It is not a ratified standard, implementation specification, or product commitment.

## Purpose

This document proposes the minimum conceptual framework needed to discuss an open, reusable accounting model without making one ERP, report, chart of accounts, or execution engine the definition of the business.

The framework separates:

1. economic meaning;
2. logical representation;
3. participant-specific accounting projections; and
4. physical execution and storage.

A proposal should identify which layer it changes and preserve the relationships and invariants at the other layers.

## Core concepts

### Involved Party

An Involved Party is a party that participates in, authorizes, owns, controls, owes, receives, or observes an economic relationship. In REA terms this is related to the Agent concept. An Involved Party may be an individual, organization, legal entity, system, account holder, or other recognized participant.

An identifier for an Involved Party must not be confused with a reporting category or account code. Those are perspective-specific classifications.

### Resource

A Resource is something that can be controlled, exchanged, consumed, produced, promised, measured, or represented as having economic significance. A resource may be tangible, financial, contractual, informational, or a right or obligation recognized by the model.

### Arrangement and Contract

An Arrangement is a governed relationship among Involved Parties that establishes rights, obligations, permissions, constraints, or expected changes in resources. A Contract is the agreement and terms that define an Arrangement. A policy, loan, purchase order, service agreement, or account relationship may be represented by an Arrangement with one or more Contracts over its lifecycle.

An Arrangement or Contract may have terms that generate expected future events. Terms are not the same as realized events; the model must preserve the relationship between them.

### Instrument

An Instrument is a stable, identifiable unit of an Arrangement or economic position whose lifecycle can be followed through events. Examples include a loan, policy, deposit, claim, order, shipment obligation, security, or receivable.

The Instrument ID is a stable join key. It should connect event history with contract terms and reference attributes without encoding every classification used by every participant.

### Commitment

A Commitment is an authorized expected future action, transfer, settlement, or obligation arising from an Arrangement or Contract. It may be created, amended, fulfilled, canceled, expired, or superseded.

A Commitment is not a completed event. A system must not treat a promise as fulfilled merely because it has been recorded.

The reciprocal relationship between commitments made by different Involved Parties needs explicit
research. A buyer's commitment and a seller's corresponding commitment may describe one shared
economic expectation without being identical participant projections.

### Event

An Event is an authorized occurrence that changes, confirms, measures, or relates economic state. An Event should have:

- a stable identifier;
- event type;
- effective time and, where needed, recorded time;
- participating Agents;
- related Arrangement and Instrument identifiers;
- affected Resources or positions;
- quantities, amounts, units, and currency where applicable;
- causal, predecessor, successor, correction, or supersession links;
- source and authorization provenance; and
- lifecycle status.

The definition of an Event must not depend on the account or report into which a participant later projects it.

Events may be represented at several related levels without confusing the levels with separate
economic facts. A Contract or Arrangement can anchor a Group Event such as an order or settlement
cycle; a Group Event can contain Subgroup Events such as order lines; and a Subgroup Event can
contain one or more Event Distributions at the lowest processing granularity. Each level needs an
identity and an explicit relationship to its parent. An event-type code classifies a fact; it does
not replace the fact's identity.

### Movement

A Movement is an economically meaningful change associated with an Instrument or Resource. It is the primary fact from which a position may be derived.

A Movement may represent creation, increase, decrease, transfer, allocation, valuation, settlement, reclassification, reversal, or another explicitly defined change. The model must distinguish a movement from a derived balance and from a display-only adjustment.

### Position

A Position is a derived state at a specified point or interval, calculated from applicable Events or Movements under declared rules and reference-data versions.

A Position is incomplete without its scope. Its identity should include the relevant Instrument or Resource, Agent or participant perspective, time boundary, currency or unit, classification generation, and calculation rule version.

Two positions with the same numeric amount are not necessarily equivalent if their source cutoff, classification, or perspective differs.

The model should distinguish at least:

- **Contract Position:** the state of rights, obligations, or amounts associated with a Contract or Arrangement;
- **Resource Position:** the state of a Resource or Resource Group; and
- **Participant Position:** the state as represented in an Involved Party's accounting perspective.

In financial-services examples, Contract Position and Resource Position may contain the same
amounts because the account balance is the primary tracked resource. That equivalence is a result
to demonstrate, not an assumption to build into the model.

### Perspective

A Perspective is a declared way of interpreting shared and private facts for a participant, purpose, or report. A buyer, seller, lender, regulator, risk group, and management group may derive different legitimate projections from the same shared event.

A Perspective must identify:

- the participating party or reporting authority;
- the source event cutoff;
- the accounting and classification rules;
- the reference-data generation and perspective date;
- the measurement basis, currency, and units; and
- the privacy and sharing boundary.

The definition of an Event, Contract, Commitment, Resource, or Position must remain independent of
which participant is viewing it. A customer may describe a deposit as an asset while a bank
describes the corresponding obligation as a liability; imposing either perspective on the shared
definition would confuse the ledger fact with its accounting projection.

### Reference datum

A Reference Datum is governed information used to classify, interpret, validate, or enrich Events, Instruments, Resources, Agents, or Positions. Examples include account concepts, industry codes, taxonomies, currencies, units, jurisdictions, products, and reporting dimensions.

Reference data is not timeless metadata. It has provenance, valid time, publication time, stewardship, licensing, and lifecycle status.

### Rule

A Rule is an explicit, versioned, testable statement that transforms or validates facts. Accounting rules, classification mappings, allocation rules, validation constraints, and projection logic are all Rules.

Rules should be declarative where practical. The execution engine may compile or optimize them, but compilation must not alter their business meaning.

### Evidence

Evidence is the information needed to support the existence, authorization, interpretation, or calculation of a fact. It may include source records, signatures, approvals, documents, system observations, control totals, and test results.

Evidence links should support tracing from a published Position to the Events, Reference Data, and Rules that produced it.

## Conceptual invariants

These are proposed invariants for community review.

### Identity

An Event, Instrument, Arrangement, Agent, Reference Datum, Rule, and Position must have a stable identity within its declared namespace and versioning scheme.

Display names, account codes, and hierarchy paths may change without silently changing the identity of the underlying concept.

### History

An accepted Event is not deleted or overwritten to make a later interpretation convenient. Corrections, reversals, supersessions, and reclassifications are represented explicitly and remain traceable.

### Time

The model distinguishes at least:

- effective time: when the economic fact applies;
- recorded time: when the system accepted or observed it;
- publication time: when a dataset or rule became available; and
- perspective time: the date used to resolve a requested classification or view.

A single timestamp must not be used to answer all four questions.

### Perspective independence

The underlying Event definition must not be owned by a participant's chart of accounts. Participant-specific entries and reports are projections of shared or private facts.

### Derivation

A Position, report, balance, or journal projection must identify the Events, Reference Data, and Rules on which it depends, either directly or through a reproducible generation record.

### Balance and conservation

Where a Rule claims that a projection is balanced or conserves a quantity, the scope, unit, currency, sign convention, and boundary must be explicit. A local balanced projection does not prove that the underlying source events are complete or correct.

### Authorization

An Event or Reference Datum may become authoritative only under a declared authorization policy. Technical arrival, human approval, counterparty confirmation, and accounting acceptance are distinct states unless a policy explicitly combines them.

### Local extension

A participant may add local Reference Data, mappings, Rules, and private projections without changing the canonical upstream identity or history. An extension must record its relationship to the upstream version.

## Reference-data perspectives

Reference changes require explicit interpretation. The framework adopts the following working vocabulary:

- **Switch:** resolve each Event using the Reference Datum effective at the Event's effective time;
- **Recast:** resolve historical Events using a selected later Reference Datum generation;
- **Reclass:** generate an explicit, traceable Event that moves a prior Position or classification from one basis to another.

These are different operations. A report query must not silently turn a Recast into a Reclass, and a Reference Data update must not silently create accounting Events.

## Accounting projection

A participant may project an Event into one or more accounting entries. The projection may include:

- participant and counterparty roles;
- debit and credit or other sign conventions;
- chart-of-accounts mappings;
- tax, management, risk, or regulatory classifications;
- allocation and valuation outputs; and
- links to the source Event and Rule generation.

The chart of accounts is therefore a governed vocabulary for a Perspective, not the universal ontology of the Event.

Manual adjustments should be anchored to an Instrument, Arrangement, source Event, or explicitly declared control object. An unanchored top-side amount is difficult to explain, reproduce, or share safely.

### Shared and private balance control

Shared and private partitions do not necessarily balance independently. A participant's accounting
edit applies to the participant's complete books, considered as the union of its authorized shared
partition and its private partition. A shared entry may therefore require a participant-private
offset or classification that must not be disclosed to the counterparty.

The implementation must state which entries and attributes are shared, which remain private, and
where the balance control is evaluated. It must not claim that a shared partition is a complete
book merely because some debit and credit lines are visible there.

### Selected sharing and participant reference data

A shared relationship should have its own Shared Participant identity that records the participating
Involved Parties and anchors the shared partitions. The Shared Participant is not a new economic
party; it is the identity of the authorized relationship and its shared data boundary.

The shared layer should use selected sharing, not a share-everything or share-nothing rule. The
participants must agree on which transaction facts, formats, reference values, and direct postings
are shared. They need not disclose every offset or use the same chart of accounts.

Shared codes and titles are not automatically participant accounting codes. Each party may translate
a shared value into its own chart and perspective. For example, a shared value `A` might mean
goods-for-sale inventory to one party and raw materials to another. Translation rules and private
reference data remain local while the shared Event identity remains common.

Private offset journal data may be generated by private Rules that read the shared partition and
route the result into the participant's private partition. The resulting balance control is applied
to each participant's complete shared and private books.

### Small shared-ledger example

A company-issued employee credit card illustrates the boundary without requiring a full ERP
replacement. The employee's personal records, the card company's ledger, and the employer's
reimbursement ledger may each contain different perspectives on the same spending activity. A
selected shared ledger can preserve the common transaction facts once, while each participant keeps
its own analytics and local accounting interpretation.

The useful shared attributes may include who, what, where, amount, currency, and transaction date.
The model should also support prospective payment information: when the commitment was made, when
payment is due, how often it recurs, and when it ends or renews. This allows a payment ledger to
describe both historical activity and expected future obligations.

The value proposition is not only lower transaction or reconciliation cost. A shared ledger should
be evaluated for whether it produces more truthful, trusted, timely, and useful analytics for all
authorized parties.

### Control totals as the authoritative shared fact

**Added October 30, 2026.**

The **control total** — the balance derived from mutually confirmed events at a declared cutoff —
is the central artifact of the shared ledger, not a by-product of it. Every shared event changes
a balance. The control total at any point is the cumulative authoritative state of the
relationship: what was contributed, sold, delivered, paid, and owed. It is derived
deterministically from confirmed events. Both parties agree it is correct because both parties
confirmed the events that produced it.

At period close, the control total becomes a **shared event**: both parties authorize it as the
accepted financial position for the period. This mutual authorization is stronger than a
traditional period-end reconciliation, which checks whether two independently kept records agree.
In the shared ledger, there is no second independent record to check against. Agreement is
structural — it is built into the mechanism of producing the total — not procedural.

The **audit confirmation** that auditors send between counterparties ("please confirm your balance
with us as of December 31") exists because both parties kept independent records of the same
economic relationship and the auditor must check that they agree. In a shared ledger with mutual
event confirmation:

- The counterparty balance is a position derived from facts both parties have already authorized,
  event by event.
- At period close, both parties confirm the total as a shared event.
- The auditor can inspect that shared close event directly, with its event cutoff, rule versions,
  control totals, and authorization timestamps.

The audit confirmation is therefore structurally redundant for confirmed shared relationships. The
system produces a stronger guarantee — mutual authorization at the time of each event and at
close — in place of a retrospective letter.

This is the fundamental claim: a shared ledger with mutual event confirmation produces financial
facts that are **co-authorized by the parties**, not asserted by one and verified expensively
after the fact. The quality of financial truth is categorically different from independent books,
because neither party can produce the control total alone.

## Worked example: a delivered purchase

A simplified purchase can be represented as a sequence rather than as a single invoice balance:

1. Buyer and seller authorize an Arrangement and a purchase Commitment.
2. The seller records a fulfillment Event tied to the Instrument and delivered Resource.
3. The buyer confirms receipt through a related Event.
4. The parties authorize a settlement Event.
5. Each participant derives its own accounting projection from the shared Events and private Rules.
6. A Position is calculated for the buyer, seller, bank, or management perspective using a declared cutoff and Reference Data generation.

The shared facts do not require the buyer's private cost allocation or the seller's private margin calculation to be disclosed. The same Event identity can support both perspectives while preserving the sharing boundary.

## Logical representation questions

The next design work should decide, with examples and tests:

- whether Event, Movement, and Position need separate record types or can be represented through a common lineage model;
- how an Instrument lifecycle handles amendments, novations, splits, merges, and replacements;
- which identifiers are globally stable and which are scoped to a participant or dataset;
- how amount, quantity, unit, currency, valuation, and uncertainty are represented;
- how late-arriving and corrected Events affect snapshots and period-close views;
- how Rules declare dependencies and produce auditable generated Events;
- how shared and private partitions prove common facts without exposing private projections; and
- what minimum conformance suite distinguishes a compatible implementation from a merely similar one.

## Relationship to physical systems

GenevaERS, a database, a stream processor, a file layout, or an ERP application may be a physical realization of part of this framework. Physical concerns such as sorting, buffering, compilation, partitioning, and recovery should be evaluated against the conceptual invariants rather than used to redefine them.

The model may require physical groups for efficient execution. A Contract Group and Resource Group
used by Common Key Buffering are execution arrangements, not additional economic entities. CKB's
hierarchical sort requirement creates a real access-path choice when Contract and Resource groups
need different sort orders:

- resort the files for the next access path, paying additional processing and I/O cost;
- duplicate or materialize the files in both orders, paying storage, update, and reconciliation cost; or
- use an index or lookup structure, paying index-maintenance, random-access, and memory costs.

Events may need to be resorted by the relevant Resource Group or Contract Group. Involved Party
attributes are normally small enough for one enterprise to load into memory and resolve through a
direct lookup, so they are not usually a competing CKB sort path. The logical model must show this
mapping without making the physical CKB groups part of the business definition. A Ledger Lab
workload must record which Contract and Resource access strategy it uses and measure its cost
separately from the conceptual result.

An implementation proposal should provide:

- a mapping from each physical record to the concepts it represents;
- lifecycle and correction behavior;
- reference-data and rule version handling;
- control totals and reconciliation tests;
- performance assumptions; and
- known information that is intentionally not preserved.

## Proposed first conformance example

The first community test should remain deliberately small:

- one Arrangement;
- two Agents;
- one Instrument;
- one Commitment;
- fulfillment and settlement Events;
- one Reference Datum change;
- Switch, Recast, and Reclass outputs;
- participant-specific accounting projections; and
- a trace from each reported amount back to source Events and Rules.

A model that cannot express this example clearly is not ready to claim that it supports a universal ledger.
