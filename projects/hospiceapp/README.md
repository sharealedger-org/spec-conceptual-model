# Hospice Family Caregiver Shared Ledger

**Status:** Working example for conceptual-model development. This is not a product
specification, a clinical protocol, a HIPAA compliance guide, or a product commitment.

---

## Purpose

This example illustrates the Sharealedger conceptual model in a non-financial, human context:
coordinated care for a hospice patient across a hospice nurse, rotating family caregivers, and
the hospice agency. It is intended to:

- demonstrate that the shared/private partition model applies outside of enterprise finance;
- provide an emotionally accessible illustration of the value proposition for non-technical audiences;
- serve as a second conformance scenario alongside the purchase/settlement example in the
  framework; and
- surface the shared-storage infrastructure question that the conceptual model must eventually address.

The hospice setting was chosen because it makes the reconciliation problem concrete and painful.
Family members coordinate care through text messages — rich, timely, and ephemeral — while the
clinician maintains a formal medical record that is controlled, governed, and inaccessible to the
family in real time. The same economic event (a symptom observation, a medication administration,
a nurse visit) may be partially recorded in three or four separate private places with no
authoritative shared version. A shared ledger can reduce that fragmentation without requiring
anyone to expose information they are not authorized to share.

The medication supply chain is a second and distinct reconciliation problem within the same
episode. A prescriber writes an order. A pharmacy dispenses against that order. A delivery
service or pharmacy courier brings the medication to the home. A family member receives it.
The nurse, arriving for a visit, must reconstruct from memory, paper notes, and verbal reports
what was ordered, what was delivered, what is on hand, and what has been administered — across
multiple medications, potentially from multiple pharmacies, with no single shared record
connecting the chain. That is the problem this example addresses on the resource side.

---

## The parties

| Involved Party | Role | Private partition | What they may contribute to the shared partition |
|---|---|---|---|
| Hospice patient | Care subject / instrument anchor | Personal history, preferences | Consent authorizations |
| Family caregiver(s) | Lay observer, rotating care providers; receives and holds medications in the home | Personal communications, financial/insurance concerns | Symptom observations, comfort assessments, medication receipt acknowledgments, administrations they perform, questions for nurse |
| Hospice nurse | Licensed clinician, visit provider; reconciles on-hand quantities against the order and administration history on each visit | Full clinical notes, billing codes, agency scheduling | Visit time, clinical summary, on-hand counts per medication, reconciliation results, next visit time |
| Hospice agency | Care plan and prescribing authority | Internal staffing, routing, reimbursement, controlled-substance regulatory logs | Care plan goals, authorized services, medication orders |
| Pharmacy | Medication dispenser; fulfills individual orders and delivers to the home | Pricing, insurance adjudication, internal dispensing workflow | Medications dispensed per order, quantities, lot numbers, delivery confirmation |
| Delivery service | Transports medications from pharmacy to home (may be pharmacy staff, courier, or agency driver) | Internal routing, delivery cost | Delivery confirmation, time, recipient |

The shared ledger is not the full medical record. It is the designated shared layer: the facts
all participating parties have consented to see and that together constitute the coordinated
care record. Each party's private partition remains under their own governance.

---

## Mapping to the conceptual framework

### Arrangement

The **Arrangement** is the hospice enrollment: the governed relationship among the patient,
family, agency, and clinical team that establishes rights, obligations, permissions, and
expected care events over the care episode.

The governing terms — care goals, authorized services, medication schedule, visit frequency, and
the expected lifecycle of the episode from admission through discharge — are expressed in the
**care plan**, which is the governing document of the Arrangement. The care plan is versioned
reference data: it has an effective date, and amendments create new versions rather than
overwriting the prior terms.

> **Terminology note:** the conceptual framework uses "Contract" as one possible governing
> document within an Arrangement, and retains "Contract Position" and "Contract Group" as
> technical terms in the physical access-path model (CKB). Those terms appear later in this
> document in their technical senses. They do not imply that the hospice care relationship is a
> commercial contract.

### Resource

A **Resource** is something that can be controlled, exchanged, consumed, promised, measured, or
represented as having economic significance. In this example the resources are the **individual
medications** in the patient's home — each one tracked separately from order through delivery
through administration through disposal.

In practice, hospice medications are not delivered as a single kit. Each medication has its own
order, its own dispensing event at the pharmacy, its own delivery to the home, and its own
administration and disposal history. The nurse arriving for a visit must reconstruct the current
on-hand quantity for each medication by asking: what was ordered? what was actually delivered?
what has been administered since the last visit? what, if anything, was wasted or destroyed?

Without a shared record, the nurse is working from:

- the prescriber's order, which lives in the agency's EMR;
- the pharmacy's dispensing record, which the pharmacy holds privately;
- a delivery record, which may be a paper receipt, a text message, or nothing;
- the family's verbal account of what arrived and what was given; and
- a physical count of what is actually in the home at the time of the visit.

None of these are the same record. The reconciliation the nurse has to perform on every visit —
matching the physical count against a chain of events that was never written down in one place —
is the resource tracking problem this example addresses.

Each medication in the home is a Resource with:

- a **stable identity** linking it to the prescribing order, the dispensing event, and the
  delivery confirmation;
- a **position** (on-hand quantity) that changes with each delivery, administration, and waste
  event; and
- a **chain of custody** running from the pharmacy through the delivery service to the family
  caregiver, and from there through each administration to exhaustion or disposal.

The **Resource Position** for each medication at any moment is:
quantity delivered (confirmed), minus quantity administered, minus quantity wasted or destroyed.
That arithmetic must match the physical count when the nurse arrives. A discrepancy is not a
data-quality problem; for controlled substances it is a potential diversion event with mandatory
reporting consequences. For non-controlled medications it is at minimum a patient safety concern.

The key difference from a kit model is that **delivery confirmation is a separate event** from
dispensing, and its absence is itself a meaningful fact. If a medication was ordered and dispensed
but no delivery confirmation exists in the shared partition, the nurse knows before arriving that
the supply chain has an open loop — not after counting.

**Administration as implicit delivery confirmation.** A medication administration event is proof
of physical possession. If a family caregiver records an administration event for a medication
that has no prior delivery confirmation in the shared partition, the model treats administration
as an implicit delivery confirmation: the medication was present and used. This is a declared
inference rule, not a silent assumption. The inference must be recorded — the delivery status
position is resolved by the administration event, not left open. The inferred delivery quantity
is the administered dose; any additional quantity present but not yet administered remains
unconfirmed until a formal delivery confirmation or a nurse's on-hand reconciliation is recorded.
This rule degrades gracefully in offline conditions: a family caregiver with no connectivity who
administers a dose and records the administration event later will implicitly close an otherwise
open delivery loop.

### Medical equipment

Medical equipment is a second category of Resource in the home, categorically different from
medications. Hospice commonly provides durable medical equipment (DME) including hospital beds,
mattress overlays, bedside commodes, wheelchairs, walkers, oxygen concentrators, oxygen tanks,
suction machines, and IV infusion pumps. This equipment is:

- **owned by the agency or DME supplier**, not consumed by the patient — the family has physical
  custody but not ownership;
- **durable** — it is not consumed with use; it is delivered, used over the episode, maintained
  or swapped if it fails, and retrieved at end of episode;
- **operationally critical** — a failed oxygen concentrator or a missing hospital bed is an
  immediate patient safety issue, not an administrative discrepancy; and
- **subject to retrieval** at end of episode — an unretrieved piece of equipment is a financial
  liability and an inventory management problem for the agency or supplier.

The equipment lifecycle differs from medications:

| Phase | Medication | Equipment |
|---|---|---|
| Authorization | Prescription order | DME authorization / prior authorization |
| Supply event | Dispense | Delivery and setup |
| Use | Administration (consumption) | Use (non-consuming) |
| Maintenance | Waste / replacement | Swap or repair event |
| End of episode | Disposal | Retrieval |

Equipment events that belong in the shared partition:

| Event type | Effective time | Contributing party | Key attributes in shared partition |
|---|---|---|---|
| Equipment order | Order time | Agency / prescribing clinician | Equipment type, model, quantity, order ID, care plan version |
| Equipment delivery and setup | Setup time | DME supplier | Equipment type, serial number, order ID, setup confirmed by |
| Equipment malfunction | Report time | Family caregiver | Equipment type, serial number, malfunction description, urgency |
| Equipment swap | Swap time | DME supplier | Equipment replaced, replacement serial number, reason |
| Equipment retrieval | Retrieval time | DME supplier | Equipment type, serial number, retrieved by, condition noted |

The **Equipment Position** for the episode is the set of authorized equipment currently in the
home: ordered minus retrieved, with delivery confirmation as the signal that the item is on-site.
An equipment order with no delivery confirmation is an open Commitment. An equipment malfunction
with no swap event is an open patient safety issue. Both are visible pre-visit facts in the shared
ledger, not on-arrival discoveries.

Equipment and medications share the same event file but carry different Resource type codes.
They require different reference data (equipment catalog vs. medication catalog) and different
position calculations (set membership vs. quantity arithmetic). This difference is significant
for the access-path discussion below.

### Instrument

The **Instrument** is the care episode — a stable, identifiable unit whose lifecycle can be
followed through events from admission to discharge. The Instrument ID is the join key that
connects every care event, observation, and position to the same care context.

> **Note:** The patient is not the instrument; a person is an Involved Party. The care episode
> is the instrument — the governed arrangement whose lifecycle is being tracked. This distinction
> matters when a patient has more than one episode, or when an episode is transferred between
> providers.

> **Note on individual medications:** each medication in the home is a Resource associated with
> the care episode Instrument. A medication is not a separate Instrument — it does not have its
> own lifecycle independent of the episode. The order ID and dispense record are attributes of
> the resource position, not a new join key at the instrument level. Open question 7 below
> asks whether this holds for controlled substances, where the regulatory tracking burden may
> justify a stronger identity.

> **Note on medical equipment:** each piece of DME is similarly a Resource associated with the
> care episode Instrument. Unlike medications, equipment has a serial number that persists across
> episodes if the same unit is reused. Whether that serial number constitutes a Resource-level
> stable identity that survives across episodes (analogous to a loan ID surviving across
> accounting periods) is an open question. For the purposes of this example it is treated as an
> attribute of the delivery event, not as a separate Instrument.

### Commitments

| Commitment | Made by | Expected future event |
|---|---|---|
| Scheduled nurse visit | Agency | Nurse visit event on the committed date/time |
| Medication order | Prescribing clinician / agency | Dispense event at the pharmacy |
| Medication delivery | Pharmacy | Delivery confirmation event (or administration event as implicit confirmation) at the home |
| Medication schedule | Agency / prescribing clinician | Medication administration event per schedule |
| Equipment order | Agency / prescribing clinician | Equipment delivery and setup event at the home |
| Equipment retrieval | Agency / DME supplier | Equipment retrieval event at end of episode |
| Caregiver shift | Family | Caregiver presence and observation events during shift |
| Reimbursement authorization | Payer | Payment event following authorized service |

A Commitment is not a completed event. A medication order that has not produced a dispense event,
or a dispense that has not produced a delivery confirmation, are open Commitments — the expected
future event has not occurred. The shared ledger makes these open loops visible to the nurse
before the visit rather than discoverable only on arrival.

### Events

Events are the primary facts. Representative care events:

| Event type | Effective time | Contributing party | Key attributes in shared partition |
|---|---|---|---|
| Nurse visit | Visit time | Nurse | Duration, clinical summary, per-medication reconciliation results, next visit |
| Symptom observation | Observation time | Family caregiver | Symptom type, severity rating, context notes |
| Comfort assessment | Assessment time | Family caregiver | Comfort level (standard scale), sleep, food/fluid intake |
| Medication order | Order time | Prescribing clinician / agency | Medication, dosage, route, frequency, order ID, linked care plan version |
| Medication dispense | Dispense time | Pharmacy | Medication, quantity dispensed, lot number, order ID reference, dispense authority |
| Delivery confirmation | Delivery time | Delivery service; acknowledged by family caregiver | Medication, quantity, lot number, recipient, delivery method |
| Medication administration | Administration time | Nurse or family caregiver | Medication, dosage, route, administered by, order and lot reference |
| Medication waste | Waste time | Nurse (witness required for controlled substances) | Medication, amount wasted, witness ID, reason, lot reference |
| On-hand reconciliation | Reconciliation time | Nurse | Per-medication: order reference, expected on-hand, physical count, discrepancy flag |
| Medication disposal | End-of-episode or order-close time | Nurse + agency | Medication, quantity disposed, method, witness, regulatory reference |
| Question submitted | Submission time | Family caregiver | Question text, urgency flag |
| Question answered | Response time | Nurse | Response text, linked question ID |
| Care plan amendment | Effective date | Agency | Change summary, affected medications, new orders |
| Episode transition | Effective date | Agency | Transition type (admission / phase change / discharge) |

The definition of each event is independent of the reporting format used by any participant's
system. A delivery confirmation is what happened at the home; what the pharmacy's dispensing
system records internally is a projection into their private partition, not the shared fact.

Note the explicit separation of **order**, **dispense**, and **delivery confirmation** as three
distinct events. In the current state of hospice practice these are often conflated — the order
is assumed to have been filled, the filling is assumed to have been delivered, and the delivery
is assumed to have been received. Each assumption is a potential open loop. Making them separate
events in the shared partition is what closes the nurse's pre-visit visibility gap.

### Movements and Positions

A **Movement** in the care context is a clinically meaningful change in the patient's status or
in a Resource quantity. Patient-status movements (comfort, fluid intake, sleep) and medication-
supply movements (delivery, administration, waste) are both tracked, though they belong to
different Resource domains and use different reference data.

**Positions** relevant to this example:

| Position type | Derived from | Perspective | Time boundary |
|---|---|---|---|
| Current comfort level | Comfort assessment events | Family / nurse shared view | As of latest assessment |
| Open questions | Question submitted minus question answered events | Family caregiver | As of now |
| Next scheduled visit | Committed nurse visit events | Family caregiver | Next future commitment |
| Care episode phase | Episode transition events | Agency | As of latest transition |
| **Medication order fulfillment status** | Medication order minus dispense events | Nurse / agency | As of now — open if no dispense event exists |
| **Medication delivery status** | Dispense minus delivery confirmation (or earliest administration) events | Nurse / family | As of now — open if neither confirmation nor administration exists |
| **Medication on-hand quantity** (per medication) | Delivery confirmations (or implied by administration) minus administrations minus waste | Nurse / agency / regulatory | As of last reconciliation |
| **Equipment on-site status** (per item) | Equipment order minus delivery-and-setup events | Family / nurse / agency | As of now — open if no setup confirmation exists |
| **Equipment retrieval status** (per item) | Delivery-and-setup minus retrieval events | Agency / DME supplier | As of end of episode — open if retrieval not confirmed |

The **on-hand quantity** is a **Resource Position** — the current quantity of each individual
medication present in the home. It is calculated per medication, per order reference, from the
chain of delivery, administration, and waste events.

Crucially there are now three positions in the medication supply chain and two for equipment,
none of which currently exist as shared visible facts:

1. **Medication order fulfillment status** — has the pharmacy dispensed? Open = not yet filled.
2. **Medication delivery status** — has the dispensed medication reached the home? Closed by a
   delivery confirmation event or, under the administration-implies-delivery rule, by the first
   administration event for that medication.
3. **Medication on-hand quantity** — what should physically be present? Verified on nurse arrival.
4. **Equipment on-site status** — has each ordered item been delivered and set up? Open = not yet
   on site. A hospital bed ordered but not delivered is an immediate care gap.
5. **Equipment retrieval status** — at end of episode, has each item been retrieved? Open = the
   agency still has equipment in the field it hasn't recovered.

Each open position is a visible, actionable fact in the shared ledger before the nurse walks
through the door — or, for equipment retrieval, before the episode is formally closed.

This is also the clearest demonstration of the framework's distinction between **Contract
Position** (the obligation under the care plan — the Arrangement's governing terms — to have
medication available and administered on schedule) and **Resource Position** (the actual physical
quantity present in the home). They should converge; when they do not, the gap is a fact to
record and explain, not a number to correct.

A Position is incomplete without its scope. The comfort level as of Monday morning and the
comfort level as of Monday evening are different positions even if the number is the same.

### Reference data

Reference data governs interpretation without being part of the event definition:

- **Symptom vocabulary:** a shared classification of observable symptom types (e.g., pain level
  scale, dyspnea scale, agitation scale) that family caregivers use to describe observations in
  a consistent way the nurse can interpret
- **Medication catalog:** standard medication names, dosage units, and route codes
- **Equipment catalog:** DME types, models, and capability classifications (e.g., oxygen flow
  rates, bed configurations) used to match equipment to care plan requirements
- **Care phase classifications:** hospice phase designations (e.g., routine home care, continuous
  home care, general inpatient, respite) used by the agency and payer
- **Care plan version:** the current authorized care plan as a versioned reference datum — events
  must be related to the care plan generation in effect at the time

Reference data has valid time. A medication substitution, a care plan amendment, or a change in
symptom scale must be represented as a reference data change with an effective date, not as a
silent rewrite of prior events.

### Switch / Recast / Reclass in the care context

These perspectives apply directly:

- **Switch:** view each care observation using the symptom scale in effect at the time of the
  observation. If the family used a 1–5 scale in week one and the agency adopted a 1–10 scale
  in week two, Switch preserves each observation under its original scale.
- **Recast:** view all observations under the current scale for trend analysis. A nurse reviewing
  the episode retrospectively may want all comfort scores expressed on the current 1–10 scale.
- **Reclass:** an explicit event records the formal transition from routine home care to continuous
  home care. The transition is a ledger event, not a silent reclassification of prior observations.

---

## The shared/private boundary

This table shows which attributes of each event type are visible in the shared partition and
which remain private:

| Event type | Shared attributes | Private to nurse / agency | Private to family |
|---|---|---|---|
| Nurse visit | Visit time, clinical summary, per-medication and equipment reconciliation results, next visit | Full clinical notes, billing codes, reimbursement detail | Personal family communications about the visit |
| Symptom observation | Symptom type, severity, time, caregiver ID | — | Emotional context, personal observations not contributed |
| Medication order | Medication, dosage, route, frequency, order ID, care plan version | Prescriber identity details, clinical rationale | — |
| Medication dispense | Medication, quantity, lot number, order ID reference, dispense date | Pricing, insurance adjudication, internal dispensing workflow | — |
| Delivery confirmation | Medication or equipment, quantity / serial number, delivery time, recipient acknowledgment | Internal delivery routing, courier identity | — |
| Medication administration | Medication, dosage, route, time, administered by, order and lot reference | Prescriber order notes, insurance codes | — |
| Medication waste | Medication, amount, time, witness ID, lot reference | DEA reporting record | — |
| On-hand reconciliation | Per-medication: expected on-hand, physical count, discrepancy flag | Internal regulatory response if discrepancy | — |
| Medication disposal | Medication, quantity, method, date, witness | DEA disposition record | — |
| Equipment order | Equipment type, model, quantity, order ID, care plan version | Prior authorization details, pricing | — |
| Equipment delivery and setup | Equipment type, serial number, order ID, setup confirmed by, delivery time | Internal supplier routing, pricing | — |
| Equipment malfunction | Equipment type, serial number, malfunction description, urgency, reported by | — | — |
| Equipment swap | Equipment type, replaced serial number, replacement serial number, reason, swap time | Internal supplier dispatch | — |
| Equipment retrieval | Equipment type, serial number, retrieved by, condition, retrieval time | Internal supplier logistics | — |
| Care plan amendment | Change summary, affected medications and equipment, effective date | Internal agency approval workflow | — |

The family can see that a visit occurred and what was administered. The nurse's internal clinical
documentation is not in the shared partition. The family's personal text exchanges are not in the
shared partition. The shared layer holds only what all parties consented to share.

### Balance control across shared and private partitions

A family caregiver's complete care record is the union of their authorized shared partition and
their private notes. A nurse's complete care record is the union of their authorized shared
partition and their private clinical documentation. Neither shared partition alone is a complete
record.

This mirrors the accounting invariant exactly: a participant's books are the union of shared and
private entries, and balance controls are evaluated across that union, not on the shared layer alone.

---

## Worked sequence

1. Agency creates the Arrangement and care episode Instrument. Care plan Commitments are
   recorded, including the medication schedule. Medication Order Commitments are recorded for
   each prescribed medication: what was ordered, dosage, route, frequency.

2. Pharmacy receives the orders and dispenses. Medication Dispense Events recorded in shared
   partition for each medication: quantity, lot number, order ID reference. Pricing and insurance
   adjudication remain in the pharmacy's private partition. Order fulfillment positions close
   for the dispensed medications; any medication not yet dispensed remains an open Commitment.

3. Delivery service brings medications to the home. Delivery Confirmation Events recorded:
   medication, quantity, lot number, delivery time. Family caregiver acknowledges receipt in the
   shared partition. Delivery positions close. The nurse, before leaving for the first visit, can
   now see: what was ordered, what was dispensed, and what was confirmed delivered — without
   a phone call to the pharmacy or relying on the family's memory.

4. Nurse makes first visit. Nurse counts each medication on hand. On-Hand Reconciliation Events
   recorded per medication: expected on-hand equals physical count — no discrepancy. Visit Event
   recorded: time, clinical summary, next visit commitment. Full clinical notes remain in EMR.

5. Family caregiver administers a dose overnight. Medication Administration Event recorded:
   medication, dosage, time, administered by, order and lot reference. On-hand Resource Position
   for that medication decreases by one dose.

6. Family caregiver records a comfort assessment. Question submitted. Nurse responds.

7. Nurse visits. Counts medications. One medication shows a one-dose discrepancy — physical count
   is one less than the calculated on-hand position. On-Hand Reconciliation Event recorded:
   expected quantity, physical count, discrepancy flag set. The discrepancy investigation happens
   in private partitions; the shared partition records that a discrepancy was found, the amount,
   and the date — not the investigation or its outcome.

8. Care plan is amended: one medication substituted. Amendment Event recorded with new effective
   date. New Medication Order Commitment recorded for the replacement. Pharmacy dispenses and
   delivers the replacement; new Dispense and Delivery Confirmation Events recorded and linked to
   the new order. Prior lot events remain linked to the prior care plan generation.

9. Position reports generated at any point: family view (comfort trend, open questions, next
   visit, current medications and whether they have been delivered); nurse / agency view (per-
   medication order status, delivery status, on-hand quantity, administration history,
   reconciliation history, any open discrepancies).

10. At end of episode, remaining medications are disposed. Medication Disposal Events recorded
    per medication: quantity, method, witness, date. Resource Positions reach zero or a
    documented residual. DEA disposition records remain in the agency's private partition; the
    shared partition records that disposal occurred, by whom, and when.

11. At a later date the symptom scale is updated. Switch view preserves original comfort scores.
    Recast view shows all scores on the new scale. Medication supply chain events are unaffected
    — they belong to a different Reference Data domain (medication catalog and dosage units, not
    the symptom vocabulary).

---

## What this example demonstrates

- A shared ledger can serve non-financial domains where the reconciliation problem is just as real.
- Involved Parties, Resources, Instruments, Commitments, Events, Reference Data, Positions, and
  the Switch/Recast/Reclass perspectives all map cleanly to the clinical care context.
- **Five distinct open-loop positions** — medication order fulfillment, medication delivery,
  medication on-hand quantity, equipment on-site status, and equipment retrieval status — convert
  on-arrival surprises and end-of-episode gaps into pre-visit and pre-close visible facts.
- **Open Commitments are first-class facts.** A medication ordered but not yet dispensed, a
  dispense not yet delivered, an equipment order not yet set up, or an equipment item not yet
  retrieved at episode close — none of these is an absence of data. Each is a positively recorded
  open Commitment that any authorized party can see and act on.
- **Administration as an implicit delivery confirmation** is a declared inference rule, not a
  silent assumption. The rule closes the delivery-status position when a formal confirmation is
  absent, degrades gracefully in offline conditions, and is recorded rather than inferred silently.
- The **Resource Position / Contract Position distinction** is made concrete across two resource
  types: medications (quantity arithmetic) and equipment (set membership). Both must reconcile
  against the care plan obligation (the Arrangement's governing terms). When they diverge, the
  gap is a recorded fact.
- **Two Resource types, two access paths** — medications and equipment share the event file but
  require different position calculations and different sort orders for efficient derivation.
  This is the hospice-scale instance of a problem that recurs at banking scale. See the CKB
  access-path note below.
- **Chain of custody** as movement events (order → dispense → deliver → administer → waste →
  dispose; order → deliver/setup → swap → retrieve) demonstrates that the movement-based ledger
  captures non-financial physical resources of both consumable and durable types.
- **Regulatory weight** on resource tracking shows that the model must support compliance
  obligations with legal consequences, not just business convenience.
- The shared/private boundary is not a technical choice but a governed consent decision.
- Privacy requirements (HIPAA, DEA, family confidentiality) are naturally expressed as partition
  rules, not as restrictions on the conceptual model itself.
- A useful shared layer can exist without replacing the nurse's EMR, the pharmacy's dispensing
  system, the DME supplier's inventory system, or the family's preferred communication method.

---

## Open questions for this example

1. **Authorization model:** who can authorize a family caregiver to contribute observations to the
   shared partition? The agency? The patient? A designated family representative?
2. **Observation quality:** family caregiver observations are lay observations, not clinical
   assessments. Should the model distinguish observation certainty or source role?
3. **Offline behavior:** family caregivers may have poor connectivity. How do queued Events that
   arrive late interact with position snapshots already produced? This is especially acute for
   medication administration and delivery confirmation events: a late-arriving confirmation event
   changes the delivery status position retroactively, and a late-arriving administration event
   changes the on-hand position retroactively, potentially converting a clean reconciliation into
   an apparent discrepancy or vice versa.
4. **Patient as party vs. instrument anchor:** in cases where the patient can participate
   directly (e.g., entering their own comfort assessments), how does the model represent both
   the patient as Involved Party and the care episode as Instrument without confusion?
5. **Episode transfer:** if care transfers from one hospice agency to another, how does the
   Instrument lifecycle record the novation while preserving event history — including the
   per-medication supply chain history — under the prior agency?
6. **Regulatory scope:** HIPAA designates a class of records patients have access rights to (the
   Designated Record Set). Is the shared partition coextensive with the DRS, a subset, or a
   separate but related concept?
7. **Medication as Resource vs. medication as sub-Instrument:** each individual medication
   supply has its own order ID, lot number, and chain of custody. Is it adequately represented
   as a Resource attribute of the episode Instrument, or does the controlled-substance tracking
   obligation justify giving each medication supply a separate stable identity? The answer may
   differ between jurisdictions and between controlled and non-controlled substances.
8. **Delivery confirmation responsibility:** the delivery confirmation event requires someone at
   the home to acknowledge receipt. If no one is home at delivery time, who records the event,
   and when? Is an unacknowledged delivery a different fact from a confirmed delivery, and how
   does the model represent that distinction?
9. **Witness requirements:** DEA rules require a witness for controlled-substance waste. The
   witness is an Involved Party. How does the model record the witness attestation — as an
   authorization attribute of the waste Event, as a separate Evidence record, or as a co-signed
   Event? This is a small instance of the broader authorization and evidence model question.
10. **Discrepancy response:** when an on-hand reconciliation finds a discrepancy, the response
    (report to agency, report to DEA, investigation) happens in private partitions. The shared
    partition records that a discrepancy was found and by how much. What is the minimum shared
    fact, and what must remain private to avoid exposing an ongoing investigation?
11. **Multiple pharmacies:** a patient may receive medications from more than one pharmacy,
    particularly when a primary pharmacy is out of stock of a controlled substance and a second
    pharmacy fills the order. How does the model track that a single order Commitment was
    fulfilled by a dispense from a different pharmacy than originally intended?

---

## CKB access-path note

This section connects the hospice example to the physical realization question that recurs at
banking scale. It belongs here because the hospice example exposes both access paths in a
context small enough to reason about clearly.

### The two sort orders

The event file for this example contains events of several types — nurse visits, medication
events, equipment events, comfort assessments, questions — all associated with the same care
episode Instrument. When deriving positions from this file, two different groupings are needed:

**Patient / episode group (Contract Group in CKB terms):**
All events for a given care episode, regardless of which resource they affect. This group
supports patient-status positions (comfort level, open questions, next visit, episode phase)
and the overall care picture for the nurse's visit summary. Sort key: episode Instrument ID.
"Contract Group" is the GenevaERS/CKB technical term for the Arrangement-keyed buffer group;
it does not imply that the hospice enrollment is a commercial contract.

**Resource group (Resource Group in CKB terms):**
All events affecting a given medication or piece of equipment, regardless of which patient they
are associated with. This group supports resource-level positions (on-hand quantity per
medication lot, equipment on-site status per serial number) and agency-level views such as
controlled-substance inventory across all active patients, or DME fleet tracking across all
episodes. Sort key: resource identifier (medication lot or equipment serial number).

These two groups require **different sort orders on the same event file**. An event file sorted
by episode Instrument ID can buffer all events for one patient together (efficient for patient-
status derivation) but requires a full resort to derive resource-level positions across patients.
An event file sorted by resource ID supports the resource view efficiently but scatters patient
events.

### The access-path tradeoff

The framework already names the three options:

| Option | Mechanism | Cost paid |
|---|---|---|
| **Resort** | Sort the event file a second time by resource key after the episode-keyed pass | Processing time and I/O for the resort; only one copy of the data |
| **Duplicate** | Maintain two sorted copies of the event file — one by episode, one by resource | Storage for the second copy; update and reconciliation overhead when events are added |
| **Index / lookup** | Build an index on resource key; resolve resource positions through random access | Index maintenance; random I/O cost; memory pressure if the index is large |

In the hospice example the event volume per episode is small (weeks to months of care, tens to
hundreds of events per patient, a handful of medications and equipment items). The resort cost
is negligible. The choice of access path has no material performance consequence at hospice scale.

**At banking scale the same tradeoff reappears with material cost.** A loan portfolio or deposit
book may have millions of instruments and hundreds of millions of events. A securities ledger
may require simultaneous position derivation by account (Contract Group) and by security
(Resource Group). The event file is the same; the sort orders are different; the cost of
obtaining the second order — resort, duplicate, or index — must be measured and documented
rather than assumed negligible.

### Why the hospice example is the right place to introduce this

The hospice case makes the two groups structurally visible without the noise of scale:

- The **patient-status positions** (comfort, questions, next visit) are naturally episode-keyed.
  A nurse does not care about another patient's comfort level; they care about this patient.
- The **resource positions** (medication on-hand, equipment on-site) have both an episode view
  (what does this patient have in their home?) and a cross-patient view (what controlled
  substances is the agency accountable for across all active episodes? which concentrators are
  in the field and overdue for maintenance?).

The cross-patient resource view is where the Resource Group sort becomes necessary. In hospice
it is a compliance and inventory management requirement. In banking it is the securities or
collateral position. In insurance it is the policy-level reserve. The conceptual structure is
the same; the scale and the cost of the access-path choice are different.

### What a Ledger Lab workload must record

Any implementation that processes this example must document:

- which sort order the event file uses for the primary pass;
- whether a resort, duplicate, or index strategy is used for the secondary access path;
- the additional I/O, storage, and elapsed time attributable to the secondary path; and
- whether the accounting result changes depending on which strategy is chosen (it must not).

The conceptual model does not mandate one strategy. The Ledger Lab must measure each and report
the costs separately from the correctness of the result.

---

## Infrastructure note

This example depends on a **shared storage layer** — a place where authorized parties from
different organizations write and read the shared partition. A hospice nurse works for an agency;
family caregivers are individuals with personal devices; the pharmacy is a separate organization.
None of them share an existing IT infrastructure.

The provider-independent interface contract, topology analysis, provider registration model,
and IBM Cloud prototype plan are owned by the
[`projects/shared-storage/`](../shared-storage/README.md) project. That project is the
appropriate home for infrastructure scope, prototype direction, and interface contract
requirements. This example document describes only the business concepts and the sharing
boundary.

Hospice-specific infrastructure constraints that remain here:

- **HIPAA:** Any shared store used for this example must be covered by a HIPAA Business
  Associate Agreement. IBM Cloud's HIPAA-eligible tier satisfies this; other topologies
  must provide equivalent coverage.
- **Consumer identity:** Family caregivers do not have enterprise identities. The prototype
  must support a consumer identity model (email/password or social login) connected to the
  shared partition's authorization layer.
- **Synthetic data:** The first prototype iteration should use synthetic or de-identified
  data to avoid triggering the full HIPAA BAA requirement before the interface contract is
  validated.
