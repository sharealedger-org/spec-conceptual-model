# Hospice App — Active Task List

**Status: ON HOLD as of 2026-11-01.** All work deferred until hospice is re-prioritized.
No action should be taken on items below without first updating this status line.

Items are tracked here by priority. Move completed items to `log.md` when done.

---

## HIGH — Required before prototype coding begins

- [ ] **Data model** (`data-model.md`) — field-level specification for all hospice
  entities. Approach decided:
  - For 13 FHIR-covered event types: FHIR R4/R5 resource fields are the field model;
    record the relevant fields, value sets, and relationships by resource type.
  - For 6 gap event types (Delivery Confirmation, Medication Waste, On-Hand
    Reconciliation, Medication Disposal, Equipment Swap, Equipment Retrieval):
    design field specs informed by FHIR extension patterns.
  - For 3 structural gaps (shared/private partition, open commitment position,
    administration-implies-delivery rule): specify as server-enforced rules in the
    data model document.
  - All fields must be expressible as short JSON values (thin-client principle).

- [ ] **Storage interface contract** (`projects/shared-storage/interface-contract.md`)
  — OpenAPI or AsyncAPI spec for the 8 operations defined in the shared-storage
  README. Dropbox implementation is the first concrete binding. This is a
  shared-storage project item but blocks hospice prototype coding.

- [ ] **Prototype repo scaffold** (`sharealedger-hospice` on GitHub) — create repo,
  establish directory structure, add README with architecture description and RSC
  lineage note. No implementation code yet; structure only.
  ```
  sharealedger-hospice/
  ├── server/          ← Node.js/Express; state, rules, storage calls
  ├── client/          ← PWA; role-specific HTML/CSS/JS interfaces
  │   ├── caregiver/
  │   ├── nurse/
  │   ├── pharmacy/
  │   └── dme/
  ├── storage/
  │   ├── interface.js ← abstract storage contract
  │   └── dropbox.js   ← Dropbox API v2 implementation
  ├── data/            ← synthetic hospice episode fixtures
  ├── sim/             ← terminal simulation mode (stdin/stdout)
  └── docs/            ← data model and protocol references
  ```

## HIGH — Identity and compliance decisions

- [ ] **HIPAA BAA decision** — prototype uses synthetic data; confirm this is
  sufficient to avoid BAA requirement for first iteration. Dropbox/OneDrive with
  synthetic data does not require HIPAA-eligible tier.

- [ ] **Consumer identity model** — for prototype: care coordinator provisions
  phone number or email; participant receives URL; opens PWA in browser; no OAuth
  required. Confirm this is sufficient and record as a decision.

## MEDIUM

- [ ] **FHIR mapping validation** (`fhir-mapping.md`) — validate each of the 20
  candidate mappings against FHIR R4/R5 specs. The data model document supersedes
  the stub but the mapping table should be validated and cross-referenced.

- [ ] **Conformance fixture data files** — plain JSON files representing the
  synthetic hospice episode: parties, instruments, events, reference data, expected
  positions. Drives the server's test suite.

- [ ] **Synthetic dataset definition** — define the synthetic hospice care episode:
  patient profile (de-identified), care team, medication list, DME items, timeline.
  Clinically plausible; no real PHI.

## LOWER — Deferred design threads

- [ ] **SMS/USSD/WhatsApp transport design** — thin-client transport for financial
  inclusion use cases (not hospice). Shares the same Node.js server architecture.
  Separate design thread; do not conflate with hospice prototype scope.

## Notes

- Technology stack decided: Node.js/Express server, PWA client, Dropbox storage
  (pluggable), terminal sim mode. See `log.md` for full rationale.
- Architecture modeled on RSC demo (VAPostAnalysisRSCDemo). Server is the single
  source of truth. Clients are role-specific thin interfaces.
- IBM Cloud is no longer the primary prototype target. Relevant only if HIPAA-eligible
  deployment is required beyond the synthetic-data prototype.
- Infrastructure items (interface contract, provider registration, portability test)
  remain in [`projects/shared-storage/todo.md`](../shared-storage/todo.md).
