# Shared Storage — Session Log

Running log of work done on this project. Compress older entries to 3–5 bullets
when the log grows long. Each entry closes with a **State** line.

---

### Session: 2026-10-20 - Farm commerce pilot, Git storage, and relationship partitions

Discussion with Kip; recorded by Codex. This entry records design direction and proposals,
not an implemented service or a ratified amendment to the framework.

**Recovery note:** The original update to `sharealedger-org/spec-conceptual-model`,
`projects/shared-storage/log.md`, was blocked by GitHub integration permissions (HTTP 403).
The prepared entry was supplied by Kip and added to the local working tree on October 25,
2026; it has not been committed or pushed as part of this recovery.

#### Use case and implementation starting point

- Kip is considering adapting tool-git-ledger for a farm partnership in Nigeria,
  potentially extending the prototype to the partner's customers.
- The tool currently supplies accounting journals, validation, and reports. It does not
  yet implement the shared-event service, participant confirmations, authorization,
  or phone interface discussed here.
- RSC means the Really Simple Commerce demonstration, now in proto-va-rsc:
  authorizer, vendor, and customer exchange messages while a server maintains state.
  That interaction pattern is relevant to the farm pilot; a bookkeeping form alone
  does not demonstrate counterparties establishing shared facts.
- A proposed sequence is sale proposal, acceptance, dispatch/delivery, receipt
  confirmation, and settlement acknowledgment. Commitments, claims, and confirmations
  remain distinguishable. Recording payment evidence does not itself transfer money.

#### Transport and interface

- The architecture is not limited to SMS. A thin phone-browser interface can communicate
  with the service over HTTPS; messaging channels can carry invitations or later become
  additional interfaces to the same event and authorization model.
- Kip reports that the partner uses Facebook and Messenger. A browser link shared manually
  through Messenger is therefore a plausible first trial, not evidence of customers'
  preferences or connectivity. WhatsApp, SMS, USSD, and offline capture remain alternatives
  to assess with actual users; no automated messaging integration was selected.
- AI-generated HTML/CSS/JavaScript can supply the interface. GitHub Pages is suitable for a
  synthetic demonstration, not the complete live transaction service: a running service is
  needed to enforce authorization and accept events.

#### Hosting and Git recording

- Kip prefers IBM Cloud for the farm pilot to make alignment with his IBM work
  demonstrable. This is a farm-pilot direction, not a reversal of the separate hospice
  synthetic-data prototype's Dropbox/OneDrive discussion.
- Git remains the durable transaction/event history. It was mistakenly omitted from an
  earlier hosting description and was restored explicitly at Kip's direction.
- Actual records should be hosted privately within the IBM environment; public GitHub
  holds source code, tests, and synthetic examples.
- Codex proposed a small Linux VPC virtual server, persistent block storage for the Git
  repository, and protected recovery copies in IBM Cloud Object Storage. This is a candidate
  physical binding, not a selected/provisioned configuration. The preliminary USD 75-150
  monthly allowance was an unverified planning estimate, not an IBM quotation.
- Git requires a controlled hosted writer; a full Git hosting product is not inherently
  required. Branches may support proposal/review workflows but are not privacy boundaries.
  The application must authenticate the actor, validate permissions and lifecycle rules,
  serialize commits, handle duplicate retries, and define durable acknowledgment behavior.
- A server commit identifies the technical writer, not necessarily the business authorizer;
  event records must separately preserve participant identity and authorization evidence.
  Git history alone is neither an immutable archive nor a record of who read the data.

#### Storage contract and database preference

- Kip prefers a non-database starting implementation. PostgreSQL is not a framework
  requirement: the documented Object Storage/PostgreSQL/Code Engine stack is one candidate.
- A Git/file implementation may satisfy the same provider-independent contract. It must
  still provide the eight operations: event append; instrument-event reads; reference-version
  reads and writes; commitment status; position reads; participant enumeration; and read audit.
- Permissions must be expressed in model terms, with explicit read, write, administrative,
  and audit rights. Provider discovery, portable export, authorization history, reproducible
  positions, and recovery remain necessary.
- Hosting Git does not by itself implement these semantics. File-based audit records and
  rebuildable indexes are possible; no database requirement follows from the contract.
- The conceptual project does not itself undertake to operate customer infrastructure.
  Account ownership, operator responsibility, support, and costs remain to be assigned.

#### Central clarification: relationship partitions and participant projections

- Kip corrected an overly organization-centered design and the suggestion of one repository
  for every customer relationship. A shared partition belongs to an authorized relationship.
  One enterprise can share a distinct partition with each customer, and each customer can
  participate in other shared partitions with other enterprises.
- Many such partitions, together with participant-private records and rules, can occupy one
  server-private Git repository. Repository topology is a physical choice; logical partition
  identity, membership, and permitted facts define the sharing boundary.
- Participants use authorized application views/exports, not whole-repository clones. A full
  clone would disclose all contents and history; folders and branches cannot prevent that.
- In the partnership example, Kip may be a limited partner and the other partner the general
  partner. The farm's books, each partner's perspective, and each customer's perspective are
  distinct. Authority and visibility follow the agreement, not assumptions about role names.
- The same contribution event can project as farm cash/capital and as the limited partner's
  investment. A sale's agreed facts can be shared while farm costs, margins, customer internal
  classifications, and participant-specific accounting rules remain private.
- A participant's complete books combine its authorized shared facts with private facts and
  rules. A shared partition need not balance independently.
- Separate repositories or servers may later address capacity, independent custody, direct
  Git access, or retention needs. Moving a partition must preserve its identity and lineage;
  an enterprise's perspective may span multiple storage locations.

#### Economic principle

Kip characterized the design as exchanging compute complexity for minimized data.
Preserve shared facts once and derive authorized participant perspectives, reducing duplicate
business records and reconciliation. Complexity moves into authorization, event selection,
rules, and calculation. "Once" refers to the authoritative logical fact, not a prohibition
on backups, replicas, or explicitly derived caches. Cached positions should identify their
source cutoff and rule/reference versions and remain reproducible.

**Next work proposed:** Define a small Git-backed binding and one farm purchase/settlement
fixture against the existing storage contract. Test partner/customer visibility, private
projections, retry-safe atomic writes, read auditing, export/reimport, and recovery. Measure
Git write throughput, query latency, growth, and recovery before making enterprise-scale claims.

**State:** Discussion logged. IBM hosting, Git-backed event history, a non-database start,
and relationship-level partitions are the expressed design direction. No service deployed,
code changed, infrastructure purchased, or customer data recorded during the discussion.

---

### Project created (session: hospice restructure)

- Project split out from `projects/hospiceapp/` — `shared-storage-infrastructure.md`
  and its associated todo items moved here; hospice retains only hospice-specific
  infrastructure concerns (HIPAA BAA choice, consumer identity for family caregivers,
  hospice synthetic dataset).
- `README.md` written: project scope, relationship to other projects, interface contract
  summary, prototype instances framing (IBM Cloud as first instance, not normative).
- `todo.md` written: HIGH items are interface contract formalization, provider registration
  model, and IBM Cloud prototype plan; MEDIUM items add authorization rule language,
  portability acceptance test, and generalization analysis.
- Cross-references in `shared-storage-infrastructure.md` updated to reflect new location.

**State:** Project scaffolded. Full topology analysis and IBM Cloud component stack exist in
`shared-storage-infrastructure.md` as working analysis. No interface contract spec, no
prototype plan, no manifest format defined yet. See `todo.md` for next steps.
