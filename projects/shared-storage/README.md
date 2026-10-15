# Shared Storage — Project Overview

**Status:** Active — interface contract and topology analysis in progress. No prototype
has been built. This is not a ratified standard, implementation specification, or product
commitment.

---

## Purpose

Sharealedger's conceptual model defines *what* belongs in a shared partition and *where the
boundary is* between shared and private state. It deliberately says nothing about *where the
shared partition lives* or *who operates the storage*. That separation is a feature: the
conceptual model should remain independent of any cloud vendor, database engine, or deployment
topology.

But the question cannot stay open indefinitely. Every working example — the hospice caregiver
scenario, a financial purchase-and-settlement example, any multi-party Arrangement — involves
Involved Parties from different organizations who have no shared IT infrastructure. To make the
shared partition real, someone must operate the storage, the authorization layer, and the
interface through which participants read and write.

This project owns:

1. The **interface contract** that any conformant shared-storage provider must satisfy —
   the operations, authorization model, and portability requirements that make provider
   independence a property rather than a claim.
2. The **topology catalogue** — a maintained survey of candidate provider topologies with
   their tradeoffs, not as a normative selection but as a reference for community proposals.
3. The **provider registration model** — how an application discovers the shared-partition
   endpoint for a given Arrangement.
4. **Concrete prototype instances** — implementations of the interface contract on specific
   platforms, starting with IBM Cloud. Each prototype is an instance; none is normative for
   production.

---

## Scope boundary

**In scope for this project:**

- Interface contract definition (operations, semantics, authorization model, portability)
- Provider topology analysis and tradeoffs
- Provider registration model (manifest format, discovery mechanism)
- Prototype implementation plans and acceptance criteria, per platform
- Generalization analysis: what changes (if anything) to the interface contract across
  use cases (hospice vs. financial vs. other)

**Out of scope for this project:**

- Mandating a specific cloud provider for production deployments
- Operating storage infrastructure on behalf of participants
- Defining a single canonical deployment topology as normative
- Application-level logic (event semantics, accounting perspectives, FHIR mappings) —
  those belong in the relevant application project

---

## Relationship to other projects

| Project | Relationship |
|---|---|
| **Hospice App** | First concrete use case for the interface contract; drives the HIPAA and consumer-identity requirements; IBM Cloud prototype serves this use case first |
| **Ledger Lab** | Reference implementation will need to target a shared-storage provider; interface contract conformance tests live here |
| **Framework** | Conceptual model defines shared/private partition semantics; this project maps those semantics to an implementable interface contract |

---

## Interface contract summary

A conformant shared-storage provider must support the following operations against a shared
partition identified by an Arrangement or Instrument ID. Full specification lives in
[`shared-storage-infrastructure.md`](shared-storage-infrastructure.md).

| Operation | Description |
|---|---|
| **Append event** | Write an authorized Event to the shared partition, atomically, returning recorded time |
| **Read events by instrument** | Retrieve Events for a given Instrument ID, optionally filtered by type, effective time, or recorded time |
| **Read reference datum version** | Retrieve the Reference Datum in effect for a given version ID and perspective date |
| **Write reference datum** | Publish a new Reference Datum version with stable ID, effective date, and provenance |
| **Check commitment status** | Retrieve the current lifecycle status of a Commitment |
| **Read position** | Retrieve a calculated Position for a given Instrument, participant perspective, and time boundary |
| **Enumerate participants** | List the Involved Parties authorized to read or write the shared partition for a given Arrangement |
| **Audit read** | Return recorded time, participant identity, and event IDs accessed in a prior read operation |

---

## Prototype instances

### IBM Cloud (first prototype)

IBM Cloud is the target for the first concrete prototype, driven by Sharealedger's IBM
affiliations and IBM Cloud's HIPAA-eligible infrastructure tier. This is a practical starting
point, not a normative production recommendation. See
[`shared-storage-infrastructure.md`](shared-storage-infrastructure.md) for the component
stack and open questions.

Other prototype instances may be added as the community identifies them. Each should be
documented with its topology, implementation plan, acceptance criteria, and any use-case-specific
constraints.

---

## Key documents

- [`shared-storage-infrastructure.md`](shared-storage-infrastructure.md) — full topology
  analysis, IBM Cloud prototype stack, provider registration problem, portability requirement,
  and open questions
- [`todo.md`](todo.md) — active task list
- [`log.md`](log.md) — session log
