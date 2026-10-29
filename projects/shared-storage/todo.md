# Shared Storage — Active Task List

Items are tracked here by priority. Move completed items to `log.md` when done.

---

## HIGH — Required before any prototype can be built

- [ ] **Interface contract formalization** — the eight operations in
  `shared-storage-infrastructure.md` are described in prose. Formalize as a
  machine-readable spec (OpenAPI or AsyncAPI). Decision needed: which format,
  and where does the spec file live (this project or ledger-lab)?

- [ ] **Provider registration model** — define a minimum viable manifest format
  for how an application discovers the shared-partition endpoint for a given
  Arrangement. Who signs the manifest, where does it live, how is it updated
  when a provider migrates?

- [ ] **IBM Cloud prototype plan** — the IBM Cloud component stack is identified
  in `shared-storage-infrastructure.md` but no implementation plan exists. Define:
  which component first, what synthetic dataset, what acceptance test, who operates
  the IBM Cloud account. Note: IBM Cloud is an instance of topology 1 (managed cloud
  provider); the plan should be written so a second topology could follow the same
  acceptance structure.

## MEDIUM

- [ ] **Operations required by the Small Venture Sample** — add to the contract: copy-on-exit (named,
  atomic, confirmed; distinct from the terms-of-use export), retry-safe append (a duplicate
  event ID does not create a second event), and claim-versus-confirmation status on events.
  See [`projects/small-venture/spec.md`](../small-venture/spec.md) §8. The first binding for the
  sample is Git-backed files.

- [ ] **Authorization model specification** — the interface contract names four
  authorization categories (read, write, administrative, audit) but does not specify
  the rule language. Define a portable authorization rule format expressible in
  conceptual-model terms (Involved Party role, event type, Instrument ID) that a
  provider translates to its native IAM.

- [ ] **Portability acceptance test** — `shared-storage-infrastructure.md` defines
  the portability requirement (export preserving all events, reference data, commitments,
  audit trail, and provider registration). Define the acceptance test: what does a
  passing export/reimport look like, who runs it, what fixture data is used?

- [ ] **Generalization analysis** — the IBM Cloud prototype targets the hospice use
  case. Identify the minimum changes (if any) to the interface contract and prototype
  stack to support a financial use case (purchase and settlement). Are there contract
  operations missing? Are there authorization requirements that differ?

- [ ] **Topology catalogue maintenance** — `shared-storage-infrastructure.md` currently
  covers six topologies. As community proposals arrive, new topologies should be
  evaluated and added with the same tradeoff structure.

## LOWER

- [ ] **Second prototype instance** — identify a second candidate topology for a
  prototype (e.g., FHIR server as shared store for the hospice use case, or a
  participant-hosted node for a financial use case). Define scope and acceptance
  criteria before starting.

- [ ] **Interface contract versioning** — define how a provider declares its supported
  interface contract version, and how a client discovers version compatibility.
