# Practical Implementation Roadmap

**Status:** Working implementation plan derived from the Sharealedger vision and research papers.

The papers explain why an event- and instrument-based architecture matters. This repository should define the smallest useful version; [Ledger Lab](../../ledger-lab) should make it concrete through Scala processing, data, measurements, and tests.

## Guiding rule

A concept belongs in the implementation work only when it helps us do one of these things:

- represent a real business fact;
- calculate a useful result;
- validate or reconcile that result;
- preserve lineage and authorization; or
- exchange data with another system.

The papers remain the place for the broader history, theory, terminology, and argument. This roadmap uses only the minimum vocabulary needed to build and test a working example.

## First deliverable: one complete example

Build one small example that can be read by a person and processed by a program:

- two parties;
- one agreement;
- one instrument;
- one commitment;
- fulfillment and settlement events;
- one reference-data change; and
- two participant views of the result.

The example should answer practical questions:

- What happened?
- Who authorized it?
- Which instrument does it affect?
- What position does each party have now?
- Which data and rule produced that position?
- What changes when the classification changes?

## Implementation sequence

### 1. Use plain data files

Start with small JSON or CSV files in a conceptual fixture package. Ledger Lab can translate or stage those fixtures into its declared Scala input contracts. Do not begin with a database, distributed ledger, ERP replacement, or AI model.

The first files should represent:

- parties;
- agreements and instruments;
- events and movements;
- reference data;
- rules; and
- expected outputs.

Each record should have an ID, source, effective date, and status where those fields matter.

### 2. Define schemas

Add machine-readable schemas for the first example. The schemas should reject missing identifiers, invalid dates, unknown references, duplicate event IDs, and invalid lifecycle transitions.

The schemas should be understandable without a particular runtime. JSON Schema is a practical starting point; CSV representations can support analysts and simple tools.

### 3. Write a deterministic calculator

Implement the smallest calculation in Ledger Lab's mainline Scala path that derives a position from events and a selected reference-data version. The same inputs must produce the same output. Python may prepare fixtures or inspect evidence, but must not replace the measured financial path.

A separate small-volume implementation in Python is permitted as a second, independent check of the same fixture (see [Small Venture Sample](../projects/small-venture/README.md)). It is a conformance check, not a performance claim; scale is measured afterward in Ledger Lab. Where an implementation switches between processing modes, [Perspective Derivation and Persistence](perspective-derivation-and-persistence.md) defines the equivalence test.

The calculator should emit:

- the resulting position;
- the input high-water mark or snapshot;
- the rule version;
- the reference-data version; and
- links to the source events.

### 4. Add participant views

Use the same shared events to produce two different participant views. Keep at least one mapping private to each participant so the example demonstrates that shared facts do not require identical private books.

### 5. Add classification change behavior

Demonstrate three explicit outcomes from one reference-data change:

- the historical view using the classification effective at the event date;
- the historical view using the later classification; and
- an explicit reclassification event.

Do not make a classification update silently rewrite an accounting fact.

### 6. Add tests and control totals

Test identity, event ordering, lifecycle rules, reference-data validity, position calculations, participant projections, and lineage. Include expected control totals and a deliberately invalid example for each major validation rule. Use Ledger Lab's evidence and interpretation contracts for measured results.

### 7. Publish a small data package

Package the example data, schemas, rules, expected outputs, validation results, and a manifest containing versions, licenses, sources, and checksums. A bot should be able to download the package and reproduce the Ledger Lab outputs without reading a paper.

### 8. Measure competing access paths

For the same example, Ledger Lab should load the small Involved Party reference data for direct
lookup, then document and, where practical, compare the two material CKB access paths:

- arrangement-ordered input followed by a resource-file resort;
- duplicated arrangement- and resource-ordered files; and
- an indexed or lookup-based Resource access path, if the resource data cannot be handled through
	the primary sorted-file strategy.

The comparison must separate the accounting result from the physical cost of obtaining the
required order: elapsed time, bytes written, retained copies, sort or index work, memory, and
reconciliation obligations. The conceptual model does not choose one strategy in advance.

## Later extensions

Only after the first package works should we add:

- more instruments and lifecycle events;
- currencies and units;
- effective-dated industry and account classifications;
- contract terms and future commitments;
- shared/private authorization protocols;
- mappings to ERP and XBRL structures;
- a high-throughput execution engine; and
- AI retrieval or sequence-model experiments.

## What does not belong in the first implementation

The first implementation should not attempt to solve:

- every accounting standard;
- every ERP business function;
- a universal chart of accounts;
- public blockchain consensus;
- production-scale performance;
- a complete contract language; or
- general-purpose AI reasoning.

Those may become later research or implementation tracks. They should not prevent a small, verifiable example from working.

## Repository boundary

The conceptual-model repository owns the business example, logical contracts, expected behavior, and unresolved decisions. Ledger Lab owns the executable calculator, Scala processing path, fixture execution, control evidence, and measured performance. GenevaERS remains a candidate or later execution realization, not a prerequisite for the first proof.

## Definition of useful

The first implementation is useful when an independent developer can download the conceptual fixture and Ledger Lab instructions, understand the records, run the Scala calculator, reproduce the expected outputs, introduce a valid reference-data change, see the three declared classification outcomes, and trace each result back to the input events.
