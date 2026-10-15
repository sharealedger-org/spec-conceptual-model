# Conceptual Model — Work Log

This file records open work items, session notes, and follow-on tasks at the
repo level. Project-specific work is tracked in each project's own `log.md`
and `todo.md`. Items here concern the framework layer, repo structure, and
cross-cutting concerns.

---

## Session: 2025 — Repo Naming Convention and Registry

### Work completed this session

**Repo proliferation strategy decided:**
- Organizing principle: repo *type*, not industry vertical
- Prefix scheme adopted:
  - `proto-` — time-limited prototype instances
  - `spec-` — specifications and conceptual models
  - `tool-` — standalone tools
  - `org-` — internal org infrastructure
  - *(no prefix)* — well-known core projects: `community`, `ledger-lab`, `ledgerlearning`
- No industry taxonomy; industry context belongs inside `spec-conceptual-model` project specs

**Repos renamed on GitHub:**
- `admin` → `org-admin` ✓
- `members` → `org-members` ✓
- `sharealedger-conceptual-model` → `spec-conceptual-model` ✓
- `sharealedger-books` → `org-books` ✓
- Future hospice prototype: `proto-hospice` (not yet created)
- `git-ledger` → `tool-git-ledger` ✓
- `VAPostAnalysisRSCDemo` → `proto-va-rsc` ✓ (archived)
- `universal_ledger` → `proto-universal-ledger` ✓ (archived)
- `Community_Specification` → `spec-community-specification` ✓

**File references updated across all repos (committed separately per repo):**
- `community/README.md` — repo registry table + creation policy added; renamed repo references updated
- `community/meetings/CorporateDocuments/2026-10-01_L.Membership_Access_Policy.md` — `admin` → `org-admin`, `members` → `org-members`
- `community/.github/ISSUE_TEMPLATE/membership-request.md` — `members` → `org-members`
- `community/meetings/README.md` — `members` → `org-members`
- `community/docs/membership.html` — `members` → `org-members`
- `community/docs/governance.html` — `members` → `org-members`
- `community/docs/books/.../appendix-1-accounting-model/index.html` — `conceptual-model` → `spec-conceptual-model`
- `community/docs/books/balancing-act/appendix-1-accounting-model.html` — `conceptual-model` → `spec-conceptual-model`
- `ledgerlearning/README.md` — `members` → `org-members`
- `ledgerlearning/docs/about.html` — `conceptual-model` → `spec-conceptual-model`
- `ledgerlearning/docs/books/.../appendix-1-accounting-model/index.html` — `conceptual-model` → `spec-conceptual-model`
- `sources/theoretical-foundation.md` — `members` → `org-members` (2 occurrences)
- `sharealedger.code-workspace` — all display names updated to match new repo names
- `sharealedger-books/.git/config` remote URL — updated to `org-books`
- `conceptual-model/.git/config` remote URL — updated to `spec-conceptual-model`

**GitHub note:** GitHub automatically redirects old repo URLs after rename, so no links break
immediately. The file updates make the references authoritative and remove redirect dependency.

**Remaining optional item:** rename `git-ledger` → `tool-git-ledger` on GitHub when convenient.

---

## Session: 2025 — Repo Restructure + Confidentiality Fixes

### Work completed this session

**conceptual-model repo:**
- Restructured into three-layer architecture: `sources/`, `framework/`, `projects/`
- All files moved, new project stubs created (ledger-lab), STARTUP.md added, README rewritten
- Committed: `restructure: sources / framework / projects three-layer architecture`

**members repo:**
- E205, E206, E210, E211, E220, E221 transcript titles renamed from HSBC to
  "Global Financial Institution POC" series
- Topical_Index.md committed (had been created last session but not committed)
- Committed in two commits: rename/add new files, then remove old HSBC files

**ledgerlearning repo:**
- README.md updated (CwK episode count 250+ → 330+, expanded description, CwK corpus section)
- **NOT YET COMMITTED** — Dropbox sync was interfering; commit manually:
  `git add README.md && git commit -m "README: update CwK episode count and description; add CwK corpus section"`

---

## Session: 2025 — Repo Restructure (sources / framework / projects)

### Work completed this session

**Repo restructured into three-layer architecture:**
- `framework/` — generalized shared-ledger principles (was `model/`)
- `projects/` — goal-directed work threads with README, todo, and log per project
- `sources/` — contributed materials with copyright retained by contributors

**Files moved:**
- `model/open-accounting-framework.md` → `framework/`
- `model/open-accounting-model-brief.md` → `framework/`
- `model/practical-implementation-roadmap.md` → `framework/`
- `model/examples/hospice-caregiver/README.md` → `projects/hospiceapp/README.md`
- `decisions/fhir-mapping.md` → `projects/hospiceapp/fhir-mapping.md`
- `decisions/shared-storage-infrastructure.md` → `projects/hospiceapp/shared-storage-infrastructure.md`
- `decisions/theoretical-foundation.md` → `sources/theoretical-foundation.md`
- `model/source/VA Data Samples and Specs.xlsx` → `sources/`
- `GenevaERS/` → `sources/GenevaERS/`
- `presentations/` → `sources/presentations/`

**New files created:**
- `STARTUP.md` — session startup checklist
- `projects/ledger-lab/README.md` — project stub
- `projects/hospiceapp/todo.md`, `projects/hospiceapp/log.md`
- `projects/ledger-lab/todo.md`, `projects/ledger-lab/log.md`
- `README.md` — fully rewritten to reflect new structure

**Also this session (members repo):**
- E205, E206, E210, E211, E220, E221 transcript titles renamed from HSBC to
  "Global Financial Institution POC" series; all file names, title lines, and
  cross-references updated across README, Sequential_Scan, XLSX_Correlation,
  and Topical_Index.

---

## Session: 2025 — Hospice Example, Infrastructure, and Corpus Infrastructure

### Work completed this session

**New files:**
- `model/examples/hospice-caregiver/README.md` — hospice family caregiver shared ledger
  example; full conceptual model mapping including medications (individual delivery model),
  medical equipment (DME), shared/private boundary, worked sequence, CKB access-path note
- `decisions/shared-storage-infrastructure.md` — infrastructure scope boundary, interface
  contract operations, candidate provider topologies (6 including blockchain), IBM Cloud
  as initial prototype target
- `decisions/fhir-mapping.md` — FHIR R4/R5 mapping stub; 20-row candidate mapping table;
  six identified gaps; three structural gaps; work required before interoperability claims
- `decisions/theoretical-foundation.md` — extended with CwK corpus reference section and
  13-episode blockchain position table

**Updated files:**
- `README.md` — registered new examples and decisions in the index
- `decisions/shared-storage-infrastructure.md` — added blockchain topology 6 with the
  five-gap analysis grounded in CwK episodes; expanded FHIR topology 4 with specific
  resource mappings and gap identification
- Members repo `E196` transcript — location field updated from specific institution name
  to "Global financial institution, Buffalo, NY"
- LedgerLearning `README.md` — added Conversations with Kip transcript corpus section
  with members repo pointer and access permissions statement
- Members repo `content/conversations-with-kip/Topical_Index.md` — new 14-section
  topical index bridging E-number corpus to conceptual model documents

---
## Session: 2025 — Shared Storage project split

### Work completed this session

- `projects/shared-storage/` created as a new peer project:
  - `README.md` — provider-independent charter; IBM Cloud as first prototype instance, not
    normative; scope boundary and relationship table for hospice, ledger-lab, and framework
  - `shared-storage-infrastructure.md` — moved from `projects/hospiceapp/`; cross-references
    updated to new paths for `sources/` and `projects/hospiceapp/`
  - `todo.md` — HIGH: interface contract formalization, provider registration model, IBM Cloud
    prototype plan; MEDIUM: authorization rule language, portability acceptance test,
    generalization analysis
  - `log.md` — initial state recorded
- `projects/hospiceapp/shared-storage-infrastructure.md` removed
- `projects/hospiceapp/README.md` Infrastructure note updated — now references
  `projects/shared-storage/`; hospice-specific constraints (HIPAA BAA, consumer identity,
  synthetic data) remain in the hospice project
- `projects/hospiceapp/todo.md` trimmed — cross-cutting infrastructure items moved to
  shared-storage; hospice retains FHIR mapping, HIPAA BAA decision, consumer identity model,
  conformance fixtures, and synthetic dataset definition
- `STARTUP.md` and top-level `README.md` updated to register new project

---

---

## Session: 2025 — Hospice prototype architecture and technology stack

### Work completed this session (hospiceapp project)

- **FHIR as field-level data model** — decided that FHIR R4/R5 resource fields are the
  hospice app field-level data model for covered event types. No parallel schema. Six gap
  event types require purpose-designed fields informed by FHIR extension patterns. Three
  structural gaps (shared/private partition, open commitment, administration-implies-delivery)
  are server-enforced rules, not FHIR concepts.

- **Prototype architecture decided** — modeled on RSC demo (VAPostAnalysisRSCDemo, cloned
  for reference). Server holds all state and enforces all rules. Clients are role-specific
  thin interfaces. Storage is server-side only. RSC's named-pipe transport → HTTPS REST.
  RSC's terminal UI → PWA served from same server. Terminal simulation mode preserved for
  development and demos.

- **Technology stack decided:**
  - Server: Node.js + Express
  - Client: PWA (no app install; URL or QR code; any smartphone browser)
  - Storage: Dropbox API v2 first instance; OneDrive second candidate; pluggable
  - Dependencies: `express`, `dropbox`, `dotenv` only to start
  - Onboarding: care coordinator provisions phone/email; participant receives URL; no OAuth

- **IBM Cloud de-prioritized** — Dropbox/OneDrive sufficient for synthetic-data prototype
  and more directly demonstrate portability claim. IBM Cloud relevant only if HIPAA-eligible
  deployment required.

- **SMS transport evaluated and deferred** — SMS rejected for hospice prototype (smartphone
  users; reliability/cost/length constraints). Thin-client *principle* preserved in PWA
  design. SMS/USSD/WhatsApp for financial inclusion recorded as a separate design thread.

- **RSC repo cloned** to `/tmp/VAPostAnalysisRSCDemo` for reference during architecture
  discussion. Not committed anywhere; re-clone from
  `https://github.com/sharealedger-org/VAPostAnalysisRSCDemo.git` as needed.

- **`projects/hospiceapp/log.md`** updated with full session record.
- **`projects/hospiceapp/todo.md`** rewritten to reflect new priorities and stack decisions.

### Documents needed before prototype coding begins

1. `projects/hospiceapp/data-model.md` — field-level spec; next session
2. `projects/shared-storage/interface-contract.md` — OpenAPI/AsyncAPI for 8 operations
3. `sharealedger-hospice` repo scaffold on GitHub

---

## Open Work Items for Next Session(s)

### HIGH — Confidentiality (members repo)

- [x] **E205/E206/E210/E211/E220 titles in members repo** — renamed to "Global Financial
  Institution POC" series; all file names, title lines, and cross-references updated.

- [ ] **E271 transcript body** — contains a specific institution name in the body text
  (not just location metadata). Review and redact or generalize as appropriate.
  File: `members/content/conversations-with-kip/Transcripts/E271_Data_Quality_and_People_qTf886PKgkw.md`

- [ ] **Full corpus scan for confidential institution names** — systematic grep across all
  transcript files. E196 and E205–E221 were found opportunistically; a full scan is needed.

### HIGH — Hospice App (`projects/hospiceapp/`)

- [ ] **Data model** (`projects/hospiceapp/data-model.md`) — field-level spec for all
  entities; FHIR fields for covered events; designed fields for 6 gap types; rules for
  3 structural gaps. **Next session.**

- [ ] **Prototype repo scaffold** — create `sharealedger-hospice` repo on GitHub with
  directory structure, README, and RSC lineage note. No implementation code yet.

- [ ] **HIPAA BAA decision** — synthetic data sufficient for first iteration; record as
  decision and confirm no BAA required for Dropbox/OneDrive with synthetic data.

- [ ] **Consumer identity model** — prototype approach decided (URL provisioning); record
  as a formal decision in the project.

### HIGH — Shared Storage (`projects/shared-storage/`)

- [ ] **Interface contract formalization** — OpenAPI or AsyncAPI for the 8 operations.
  Dropbox is the first concrete binding. Blocks hospice prototype coding.
  See `projects/shared-storage/todo.md`.

- [ ] **Provider registration model** — minimum viable manifest format for Arrangement
  endpoint discovery.

- [ ] **IBM Cloud prototype plan** — lower priority now; relevant only for HIPAA-eligible
  deployment. Dropbox/OneDrive serve the synthetic-data prototype.

### MEDIUM — Important for conceptual model completeness

- [ ] **First conformance example data files** — plain JSON/CSV fixture files for
  purchase/settlement (first fixture) and hospice (second). Ledger Lab owns execution;
  this repo owns fixture definitions.

- [ ] **Decision records for open framework questions** — framework names several unresolved
  questions; each should become a decision record stub:
  - Event / Movement / Position as separate record types vs. common lineage model
  - Global vs. scoped identifiers
  - Commitment reciprocity (buyer's and seller's commitments as one shared fact vs. two)
  - Amount / quantity / unit / currency / uncertainty representation
  - Late-arriving and corrected events affecting snapshots and period-close views
  - Rules declaring dependencies and producing auditable generated events
  - Minimum conformance suite definition

- [ ] **Corpus method for conceptual-model repo** — topic-first navigation layer analogous
  to `Topical_Index.md` in members repo; define structure before document count grows further.


### LOWER — Background / research tasks

- [ ] **Members repo README** — the members repo top-level `README.md` should be updated
  to register the new `Topical_Index.md` and explain the relationship between the
  corpus guide, the topical index, and the sequential scan documents.

- [ ] **CwK corpus guide update** — `Conversations_With_Kip_Corpus_Guide.md` was written
  for the older 057-episode numbering. The thematic taxonomy there is incomplete relative
  to the full E-number corpus. The new `Topical_Index.md` is the richer entry point, but
  the guide should be updated to point to it and note the relationship.

- [ ] **GenevaERS conceptual mapping** — `GenevaERS/ERP_DISCUSSION_STARTER.md` exists but
  has not been read or evaluated in this session. It should be read and assessed against
  the current conceptual model to determine what it adds, what it conflicts with, and
  whether it belongs in `decisions/` as a mapping record.

- [ ] **Monograph Papers 03–09** — Papers 00–02 are available in the members research
  directory and are referenced in `decisions/theoretical-foundation.md`. Papers 03–09
  cover the full architecture, GenevaERS, enterprise transformation, reclassification,
  operational integrity, legacy conversion, and shared ledgers. As each paper circulates,
  it should be registered in `theoretical-foundation.md` with its key correspondences
  to the conceptual model.

- [ ] **VA source workbook analysis** — `model/source/VA Data Samples and Specs.xlsx`
  is listed in the README as historical source material but has not been analyzed in
  this session. It likely contains the original VA Arrangement Ledger data that the
  monograph cites as one of its two production natural experiments. Reading and mapping
  its contents to the conceptual model would strengthen the empirical grounding.

---

## Confidentiality Notes

**Do not use specific institution names in this public repo.** Use generic descriptions:
- "global financial institution" or "major global bank" for large international banks
- "major U.S. insurer" for the insurance production natural experiment
- "major U.S. agency" for the VA / government context

Specific institution names found and already addressed:
- E196 transcript location (members repo) — fixed previous session
- E205, E206, E210, E211, E220, E221 transcript titles (members repo) — renamed to
  "Global Financial Institution POC" series; all file names, title lines, and
  cross-references updated

If you encounter any other institution names in any public repo file, fix immediately
and log here.
