# Sharealedger Conceptual Model

This repository is the conceptual home for Sharealedger's model of
perspective-independent shared bookkeeping. It describes the business concepts,
relationships, invariants, and accounting perspectives that implementations should
preserve. It is intentionally separate from any one ledger engine, ERP product, or
physical data layout.

**Status:** Foundational concepts collected here for community discussion. This is
not yet a ratified standard, implementation specification, or product commitment.

> **Starting a session?** Read [`STARTUP.md`](STARTUP.md) first.

---

## Why This Repository

Sharealedger's vision is to improve financial and other forms of performance
measurement through community-led innovation, a library of concepts and software
for shared bookkeeping, and adoption at lower cost and higher scale. A stable
conceptual model helps the community discuss what is shared, what remains private
to each participant, how events relate to resources and agents, and how different
accounting perspectives are produced — before those ideas are encoded in a
particular system.

The model should be useful independently of its implementation. GenevaERS,
Universal Ledger, and other systems can be compared as physical realizations of
the concepts; implementation constraints should be recorded without silently
redefining the concepts themselves.

---

## Repository Structure

```
conceptual-model/
├── STARTUP.md                         ← session startup checklist
├── README.md                          ← you are here
├── work-log.md                        ← repo-level session log and open items
│
├── framework/                         ← generalized principles for shared ledgers
│   ├── open-accounting-framework.md   ← working vocabulary, invariants, perspectives
│   ├── open-accounting-model-brief.md ← branches of work, first conformance target
│   └── practical-implementation-roadmap.md
│
├── projects/                          ← goal-directed work threads (what to build)
│   ├── hospiceapp/                    ← hospice caregiver shared ledger
│   │   ├── README.md                  ← conceptual model mapping
│   │   ├── todo.md                    ← active task list
│   │   ├── log.md                     ← session log
│   │   └── fhir-mapping.md            ← FHIR R4/R5 mapping stub
│   ├── shared-storage/                ← provider-independent interface contract + prototypes
│   │   ├── README.md                  ← project charter and scope
│   │   ├── todo.md                    ← active task list
│   │   ├── log.md                     ← session log
│   │   └── shared-storage-infrastructure.md  ← topology analysis, IBM Cloud stack
│   └── ledger-lab/                    ← reference implementation spec
│       ├── README.md
│       ├── todo.md
│       └── log.md
│
└── sources/                           ← contributed materials (copyright retained
    ├── theoretical-foundation.md      │  by contributors; used with permission)
    ├── VA Data Samples and Specs.xlsx
    ├── GenevaERS/
    └── presentations/
```

---

## The Three Layers

**`framework/`** holds generalized principles that apply to all shared ledger
implementations. These emerge from project work — when the same pattern appears
across multiple projects, it gets distilled here. Framework documents are owned
by the Sharealedger community.

**`projects/`** holds goal-directed work threads. Each project defines *what to
build* — requirements, conformance fixtures, acceptance criteria, design decisions.
Implementation code lives in separate repositories. Each project has its own
README, todo list, and session log.

**`sources/`** holds contributed materials where copyright is retained by the
contributor. These inform the work but are not Sharealedger-owned. The VA workbook,
GenevaERS materials, and presentations live here.

---

## Active Projects

- **[Hospice App](projects/hospiceapp/README.md)** — a non-financial shared ledger
  for hospice family caregivers; first prototype target for the framework.
- **[Shared Storage](projects/shared-storage/README.md)** — provider-independent
  interface contract, topology catalogue, and prototype instances (IBM Cloud first)
  for the shared partition layer that all Sharealedger examples require.
- **[Ledger Lab](projects/ledger-lab/README.md)** — reference implementation of
  the open accounting framework; spec lives here, code in the `ledger-lab` repo.

---

## Framework Documents

- [Open Accounting Framework](framework/open-accounting-framework.md) — working
  vocabulary, invariants, perspectives, and logical questions.
- [Open Accounting Model Brief](framework/open-accounting-model-brief.md) — branches
  of work, first conformance target, and development sequence.
- [Practical Implementation Roadmap](framework/practical-implementation-roadmap.md) —
  the path from concepts to schemas, data, code, and tests.

---

## Model Scope

The conceptual work covers:

- **Business ontology:** Involved Parties, Contracts/Arrangements, Commitments,
  Resources, Positions, and economic Events.
- **Event semantics:** perspective-independent event definitions, event duality,
  provenance, and the relationship between source events and generated accounting
  entries.
- **Financial perspectives:** participant-specific chart of accounts, balance views,
  Switch/Recast/Reclass semantics, and reconciliation obligations.
- **Shared and private state:** which event facts and relationships are shared,
  which remain participant-private, and how shared identifiers connect participant
  partitions without collapsing their books.
- **Model invariants:** identity, effective time, amount/sign conventions, balancing,
  lifecycle, and rules for correcting, reversing, or superseding events.

---

## Conceptual Layers

Keep these views distinct and explicitly connected:

1. **Conceptual model:** business meaning and invariants, independent of any
   particular application or report.
2. **Logical model:** records, identifiers, relationships, temporal rules, and
   constraints that represent the concepts.
3. **Accounting projection:** participant-specific journal entries, chartfields,
   balances, and reports derived from shared or private events.
4. **Physical realization:** files, database structures, CKB groups, streams,
   generated code, and recovery processes used by a runtime.

A proposal should say which layer it changes and show the mapping to adjacent
layers. A physical optimization must not silently become a new business definition;
a participant-specific account mapping must not be mistaken for the shared event
ontology.

---

## Context and Related Work

- [Sharealedger.org](https://sharealedger.org/) — community vision and mission.
- [LedgerLearning white papers](https://ledgerlearning.com/whitepapers/) — related
  research on financial data maintenance, event-based systems, REA, shared ledgers,
  and system renewal.
- [Sharealedger community repository](https://github.com/sharealedger-org/community)
  — community governance, discussion starters, and meeting records.
- [Ledger Lab](https://github.com/sharealedger-org/ledger-lab) — executable
  prototypes and measured evidence. A passing prototype does not by itself ratify
  this conceptual model.

---

## How to Contribute

Start proposals as questions or alternatives, not assumed facts. For a substantive
change, include:

- the concept or invariant being clarified
- a worked business example and the participating perspectives
- valid-time and event-lifecycle implications
- the logical representation and any competing choices
- mappings to accounting projections and candidate physical realizations
- controls or falsifiable examples that could distinguish the alternatives

Record unresolved choices and rationale in the relevant project's log. Do not
present a proposal as adopted until the Sharealedger community's review and
governance process has accepted it.

---

## Licensing

- Executable code: [Apache License 2.0](LICENSE-Apache.md)
- Business and conceptual content: [Creative Commons Attribution 4.0 International](LICENSE-CC4.0.md),
  subject to the attribution and notices accompanying each work.
- Source materials in `sources/` retain their original copyright and notices;
  see the individual files for terms.
