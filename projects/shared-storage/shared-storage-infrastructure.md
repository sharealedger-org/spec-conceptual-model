# Shared Storage Infrastructure — Working Analysis

**Status:** Open question — working analysis for community discussion. This is not a
ratified decision, a product commitment, or an architecture mandate.

**Related example:** [`projects/hospiceapp/README.md`](../hospiceapp/README.md)

---

## The question

Sharealedger's conceptual model defines *what* is in the shared partition and *where the
boundary is*. It says nothing about *where the shared partition lives* or *who operates the
storage*. That is deliberate: the conceptual model should remain independent of any one
cloud vendor, database engine, or deployment topology.

But the question cannot stay open indefinitely. Every working example — especially the hospice
caregiver scenario — involves Involved Parties from different organizations who have no shared
IT infrastructure. A hospice nurse works for an agency; family caregivers use personal devices;
a pharmacy is a separate legal entity. To make the shared partition real, someone must operate
the storage, the authorization layer, and the interface through which participants read and write.

This decision record:

1. establishes that shared storage *infrastructure* is outside Sharealedger's conceptual scope;
2. defines what *is* in scope — the interface contract that any conformant provider must satisfy;
3. surveys the candidate provider topologies and their tradeoffs;
4. identifies IBM Cloud as the target for initial prototype and demonstration work; and
5. names the open questions that must be resolved before a prototype can be built.

---

## What is out of scope for Sharealedger

Sharealedger does not:

- mandate a specific cloud provider for production deployments;
- operate storage infrastructure on behalf of participants;
- define a single canonical deployment topology as normative;
- require participants to migrate away from existing IT systems to use the model; or
- compete with cloud providers, ERP vendors, or healthcare IT platforms.

These are physical realization choices. The relationship between cloud provider options and the
conceptual model is analogous to the relationship between database vendors and the relational
model: the relational model specifies tables, keys, and queries; it does not mandate Oracle or
PostgreSQL.

---

## What is in scope for Sharealedger

Sharealedger's conceptual model should define the **interface contract** that a conformant
shared-storage provider must satisfy. Without this contract, an application cannot be written
to work across providers, and "provider independence" is a claim rather than a property.

A conformant shared-storage provider must support the following operations against a shared
partition identified by an Arrangement or Instrument ID:

| Operation | Description |
|---|---|
| **Append event** | Write an authorized Event to the shared partition. The operation must be atomic and return the recorded time. |
| **Read events by instrument** | Retrieve all Events for a given Instrument ID, optionally filtered by event type, effective time range, or recorded time range. |
| **Read reference datum version** | Retrieve the Reference Datum in effect for a given version ID and perspective date. |
| **Write reference datum** | Publish a new Reference Datum version with a stable ID, effective date, and provenance record. |
| **Check commitment status** | Retrieve the current lifecycle status of a Commitment. |
| **Read position** | Retrieve a calculated Position for a given Instrument, participant perspective, and time boundary. |
| **Enumerate participants** | List the Involved Parties authorized to read or write the shared partition for a given Arrangement. |
| **Audit read** | Return the recorded time, participant identity, and event IDs accessed in a prior read operation. |

The contract does not specify the wire protocol, storage format, authentication mechanism, or
hosting model. Those are provider implementation choices. A conformant provider may implement the
contract as a REST API, a message queue, a database view, or any other mechanism, provided the
semantics are preserved.

### Authorization requirements

The interface contract must also define the authorization model for the shared partition:

- **Read authorization:** which participants may read which events and attributes
- **Write authorization:** which participants may append which event types
- **Administrative authorization:** who may add or remove participants from the shared Arrangement
- **Audit authorization:** who may inspect the audit trail

Authorization rules must be expressible in terms of the conceptual model (Involved Party role,
event type, Instrument ID) rather than in provider-specific IAM syntax. A conformant provider
translates those rules into its native authorization mechanism; the rules themselves are portable.

---

## Candidate provider topologies

The following topologies are all valid physical realizations. Sharealedger should accommodate all
of them through the interface contract. No topology is normative for production.

A note on distributed ledger / blockchain topologies: Sharealedger has a developed position on
blockchain and distributed ledgers, established across multiple *Conversations with Kip* episodes
between 2017 and 2022 and summarized in
[`sources/theoretical-foundation.md`](../../sources/theoretical-foundation.md). The short form of that
position is recorded at the end of this section. Any community proposal invoking blockchain as
a candidate implementation for the shared store should engage that analysis before proceeding.

### 1. Managed cloud provider (IBM Cloud, AWS, Azure, GCP)

One participant or a neutral operator provisions storage in a managed cloud. Other participants
access it through the provider's identity and access management.

**Characteristics:**
- Easy to provision; well-understood operational model
- Single custodian raises trust and jurisdiction questions among participants
- HIPAA Business Associate Agreement (BAA) available from major providers including IBM Cloud
- IBM Cloud IAM supports fine-grained resource-level policies, trusted profiles for federated
  users, and cross-account access without requiring all participants to share an IBM account
- IBM Cloud Object Storage provides durable, S3-compatible object storage suitable for event
  append; IBM Cloud Databases for PostgreSQL provides row-level security suitable for
  multi-participant structured event stores

**Tradeoffs:**
- Custody: the operator controls the data; participants must trust the operator
- Regulatory: data residency and jurisdiction must be agreed before provisioning
- Availability: dependent on the chosen cloud region's SLA

### 2. Dedicated Sharealedger-conformant service

A purpose-built SaaS operator runs a shared-partition service that implements the interface
contract. Participants authenticate through the service rather than through a general cloud IAM.

**Characteristics:**
- Cleanest conceptual separation; operator has no business interest in the data
- Requires someone to build, operate, and sustain the service
- Introduces a single point of trust that all participants must accept

**Tradeoffs:**
- Does not exist yet; this is a future build target, not a near-term option
- Regulatory and liability questions require legal analysis

### 3. Participant-hosted federated store

Each participant operates their own node. Events are replicated or federated across nodes under
a shared protocol (e.g., ActivityPub, AT Protocol, or a custom sync protocol).

**Characteristics:**
- No single custodian; each participant owns their own copy
- Matches the shared/private partition model architecturally
- Conflict resolution and authorization enforcement are technically hard
- Eventual consistency is in tension with the event-ordering and correction invariants

**Tradeoffs:**
- Significant protocol complexity before the first working example
- Not appropriate for initial prototyping

### 4. Healthcare network or FHIR-based platform

Use an existing healthcare interoperability network (e.g., CommonWell, Carequality, TEFCA) or
a FHIR R4/R5 server as the shared store.

**Characteristics:**
- Already governed under HIPAA; BAA infrastructure exists
- FHIR R4/R5 has resource types that map directly to several Sharealedger event types:
  `MedicationRequest` (Medication Order), `MedicationDispense` (Medication Dispense),
  `MedicationAdministration` (Medication Administration), `Observation` (symptom and
  comfort assessment events), `DeviceRequest` / `DeviceDelivery` (equipment order and delivery)
- FHIR does not have a standard resource for home delivery confirmation, on-hand
  reconciliation, or equipment retrieval — these would require custom extensions or profiles
- FHIR assumes a single-organization FHIR server exposing data to others; it does not
  natively model a shared partition jointly owned by multiple unrelated parties
- FHIR does not have a declared shared/private partitioning concept; access control is
  managed through SMART on FHIR scopes and server-level consent resources
- Constrained to the healthcare domain; not generalizable to financial or other use cases

**Tradeoffs:**
- Domain-specific; useful for the hospice example but not for Sharealedger generally
- FHIR compatibility is high value for interoperability with existing clinical systems;
  the Sharealedger event model should carry explicit FHIR mappings even if FHIR is not
  the storage layer — see [`projects/hospiceapp/fhir-mapping.md`](../hospiceapp/fhir-mapping.md)
- Mapping layer complexity may obscure the conceptual model if FHIR becomes the primary
  definition rather than a compatibility surface

### 5. Local-device plus sync (consumer model)

Each participant stores data locally (e.g., on their phone); a sync service (iCloud, Dropbox,
or a custom CRDT-based sync) replicates shared facts.

**Characteristics:**
- Familiar consumer pattern; no server provisioning required for each deployment
- Sync conflict handling is well-understood in the consumer space
- Authorization enforcement is weak; sync services do not enforce event-level access control

**Tradeoffs:**
- Not appropriate for HIPAA-governed data without a BAA and enhanced controls
- Not appropriate as a general Sharealedger prototype

### 6. Distributed ledger / blockchain

Use a permissioned distributed ledger (e.g., Hyperledger Fabric) or a public blockchain
as the shared store. Hyperledger Fabric supports multi-organization channels with private
data collections, which map structurally to Sharealedger's shared/private partition model.

**Characteristics:**
- Permissioned blockchain (Hyperledger Fabric) supports multi-organization authorization,
  immutable event history, and private data collections per channel
- Structural fit with shared/private partition model is real: a Fabric channel is
  analogous to a shared Arrangement partition; private data collections are analogous to
  private partitions
- Trustless consensus is not required for Sharealedger Arrangements — participants are
  identified and authorized, not anonymous; the consensus mechanism adds cost without
  adding the security property it was designed for
- Public blockchain (Bitcoin, Ethereum) is not appropriate: throughput, finality latency,
  and per-transaction cost are incompatible with production financial or clinical use

**Why Sharealedger does not select this topology for prototyping:**

Sharealedger's developed position on blockchain, established across thirteen *Conversations
with Kip* episodes between 2017 and 2022, identifies five gaps that distributed ledger
technology does not close:

1. **The posting gap.** Blockchain records a journal — an append-only transaction list.
   A ledger requires a posting process that accumulates transactions into account positions
   under declared rules. Blockchain does not perform this process.

2. **The double-entry model gap.** Blockchain does not generate the debit/credit
   accounting representation. The accounting rules engine that derives participant-specific
   entries from shared events is a separate layer that blockchain does not provide.

3. **The reporting integration gap.** Blockchain does not integrate with reporting
   hierarchies or derive the balance and position structures that downstream finance, risk,
   and management systems consume.

4. **The operational reliability gap.** Financial and clinical systems require zero
   transaction loss and deterministic recovery. Distributed consensus systems are
   designed to tolerate node failure, which is an architecturally incompatible operational
   model. This is not a performance objection; it is a correctness and recovery objection.

5. **The volume economics gap.** A single raw transaction explodes to approximately 200
   derivative records through an accounting rules engine (ARE → Instrument Ledger →
   extracts). Per-transaction consensus cost must be multiplied by this factor. The
   economics are prohibitive at banking scale regardless of base-layer throughput.

Higher throughput (BSV, layer-2 solutions) does not resolve any of these five gaps because
throughput is not the binding constraint in any of them.

**Tradeoffs:**
- Permissioned blockchain remains a structurally interesting topology for the shared/private
  partition model; it is not ruled out permanently, only not selected for first prototyping
- Any future proposal to use Hyperledger Fabric or similar must address all five gaps above
  and demonstrate how the posting process, double-entry derivation, and reporting integration
  are provided by a layer above the blockchain
- Full analysis and episode references: [`sources/theoretical-foundation.md`](../../sources/theoretical-foundation.md)

---

## IBM Cloud as the initial prototype target

Given Sharealedger's IBM affiliations, IBM Cloud is the appropriate target for initial prototype
and demonstration work. This is not a claim that IBM Cloud is the normative production
environment; it is a practical choice to reduce infrastructure negotiation during early
development.

### Rationale

- **HIPAA-eligible:** IBM Cloud offers a HIPAA-eligible infrastructure tier with Business
  Associate Agreement support, which is required for the hospice example to be a real prototype
  and not just a conceptual sketch.
- **IAM maturity:** IBM Cloud IAM supports fine-grained resource-level authorization, trusted
  profiles for federated users from external identity providers, access groups, and cross-account
  access. These capabilities map directly to the conceptual model's authorization requirements:
  participant roles, event-type-level write controls, and read access governed by Arrangement
  membership.
- **Object storage:** IBM Cloud Object Storage is S3-compatible, durable, and available in
  HIPAA-eligible regions. It is a practical append store for event records represented as JSON or
  Parquet files, keyed by Instrument ID and effective date.
- **Managed PostgreSQL:** IBM Cloud Databases for PostgreSQL supports row-level security, logical
  replication with column-level filtering, and fine-grained access control. It is a suitable
  structured store for event queries, Reference Datum versions, and Position calculations that
  require relational semantics.
- **Code Engine / Kubernetes:** IBM Cloud Code Engine (serverless) and IBM Cloud Kubernetes
  Service are both available for hosting the interface contract service layer — the component
  that translates Sharealedger interface operations into provider-native API calls.
- **watsonx.data (future):** For later extensions involving analytics over event history or
  AI training corpus work, IBM watsonx.data provides an open lakehouse architecture on Apache
  Iceberg that could serve as a query layer over event and Reference Datum stores.

### What would be built on IBM Cloud for the hospice prototype

| Component | IBM Cloud service | Role |
|---|---|---|
| Shared event store | IBM Cloud Object Storage (HIPAA-eligible bucket) | Append-only event records keyed by Instrument ID |
| Structured query layer | IBM Cloud Databases for PostgreSQL | Event queries, Position calculations, Reference Datum versions |
| Authorization layer | IBM Cloud IAM (trusted profiles, access groups, resource-level policies) | Participant identity, read/write controls per event type |
| Service API | IBM Cloud Code Engine | Implements the Sharealedger interface contract as a REST API |
| Identity federation | IBM Cloud IAM + external IdP (App ID or SAML) | Nurse authenticates via agency IdP; family via consumer identity |
| Audit log | IBM Cloud Activity Tracker / IBM Cloud Logs | Immutable read/write audit trail |

This is a prototype architecture, not a production recommendation. A production deployment would
require a formal security and compliance review, a HIPAA BAA, and explicit data residency
decisions.

---

## The provider registration problem

An application must know, for a given Arrangement, which shared-storage provider and endpoint
to use. This is analogous to DNS: there must be a way to discover where the shared partition
for a given care episode or financial arrangement lives.

Sharealedger must define a **provider registration model** — a lightweight directory or
manifest that an application can consult to find the endpoint, authentication scheme, and
interface version for a given Arrangement's shared partition.

This may be as simple as a signed JSON manifest stored at a well-known URL associated with the
Arrangement ID. It does not need to be a global registry; it needs to be discoverable by
participants who are already authorized members of the Arrangement.

This question is unresolved and must be addressed before the first prototype can be built.

---

## The portability requirement

If a shared Arrangement migrates from one storage provider to another (e.g., from one IBM Cloud
region to another, or from IBM Cloud to a participant-hosted node), the shared partition must
be exportable in a conformant format that preserves:

- all Event records with their original identifiers, effective times, and recorded times;
- all Reference Datum versions with their provenance and effective dates;
- the Commitment and lifecycle records;
- the authorization history and audit trail; and
- the provider registration manifest updated to the new endpoint.

A participant should be able to pin an export and reproduce all Positions from the exported data
without access to the original provider. This is the storage analog of the reproducibility
invariant in the conceptual model.

---

## Open questions

1. **Who operates the IBM Cloud account for a prototype?** An IBM employee, a Sharealedger
   community member, or a designated operator entity? This has cost, liability, and access
   implications.
2. **HIPAA BAA for prototype:** Does the hospice prototype require a formal BAA, or can it use
   synthetic/de-identified data to avoid that requirement in the first iteration?
3. **Identity federation for family caregivers:** Family members do not have enterprise identities.
   IBM Cloud App ID supports consumer identity (email/password, social login) but connecting it
   to the HIPAA-eligible tier requires configuration. What is the acceptable consumer identity
   model for the prototype?
4. **Interface contract format:** Should the Sharealedger interface contract be specified as an
   OpenAPI document, a JSON Schema set, an AsyncAPI document, or something else?
5. **Provider registration model:** What is the minimum viable manifest format for locating the
   shared partition endpoint for a given Arrangement? Who signs it, and where does it live?
6. **Portability test:** What is the acceptance test for a successful export and reimport of a
   shared partition? Who runs it?
7. **Generalization:** The IBM Cloud prototype is for the hospice example. What is the minimum
   change to the interface contract that makes it usable for a financial example (purchase and
   settlement) as well?
