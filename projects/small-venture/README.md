# Project: Small Venture Sample

## What this project is

A small, realistic sample of a shared ledger for a **small venture**: a few parties who pool
resources, one or more of whom operate it, who sell to customers and share the results. The same
shape covers a small business partnership, a cooperative, a cross-border joint venture, or a
member-funded project. The first fixture uses two members and one customer; a second variant adds
more members to show the cooperative case.

This project defines **what a small-volume implementation must do**: the scenario, events, shared
and private boundaries, participant perspectives and acceptance tests. It is distinct from the
other projects:

| Project | Why it is not this one |
|---|---|
| Hospice App | A domain application for caregivers; clinical data, FHIR mapping, consumer identity |
| Ledger Lab | Scala reference implementation, measured at CKB scale; scale comes after this sample is tested |
| Shared Storage | The provider-independent storage contract; this project **uses** it and does not define it |

Python is acceptable for this sample's implementation. The implementing tool is
[`tool-git-ledger`](https://github.com/sharealedger-org/tool-git-ledger). This is a deliberate
carve-out from the Scala-mainline rule in the
[Practical Implementation Roadmap](../../framework/practical-implementation-roadmap.md): the sample
validates the concepts at small volume, and Ledger Lab measures scale afterward on the same fixture.

## Goal

Pass one conformance fixture with an independent, small-volume implementation, so that:

- the fixture is validated by a second implementation, not only by the code that wrote it;
- the generate-versus-store rule in
  [`framework/perspective-derivation-and-persistence.md`](../../framework/perspective-derivation-and-persistence.md)
  has a working test; and
- a real-shaped, low-volume use supplies requirements that synthetic examples miss: pledges paid
  in installments, a member in another currency, customers who are not on the platform, and
  members who leave.

## Agreements are developed in parallel

Parties to a venture need agreements: among the members, between the service operator and each
participant, and terms of use for the service. Those are developed separately, with legal advice,
and are **not defined in this repository**. The sample treats an agreement as a recorded input: a
versioned record that the parties acknowledge, whose terms are read by declared Rules (for example,
how an ownership share is calculated, or what a withdrawal pays). The ledger records agreements,
acknowledgments and events. It does not decide what any agreement means legally.

Each agreement is developed alongside this code and may change what a Rule says. The sample keeps
Rules declared and versioned so a change in an agreement is a change in a Rule version and not a
change in code.

## Current state

**Status:** Specification started. No implementation changes made. The spec is a working draft,
not a ratified standard.

## Key artifacts

- [`spec.md`](spec.md) — scenario, events, boundaries, perspectives, storage references and
  acceptance criteria
- [`todo.md`](todo.md) — active task list
- [`log.md`](log.md) — session log

## Boundaries

- **Synthetic only in this repo.** Real names, amounts, agreements and records stay in private
  repositories. Fixtures here use invented parties and values. Notes used privately to shape the
  idea are not committed here.
- **No legal conclusions.** The venture's legal form, ownership percentages, tax treatment and
  jurisdictional requirements are outside this project.
- **No storage design.** Storage operations are referenced from
  [`projects/shared-storage/`](../shared-storage/README.md).

## Relationships

- **Framework:** [`open-accounting-framework.md`](../../framework/open-accounting-framework.md),
  [`perspective-derivation-and-persistence.md`](../../framework/perspective-derivation-and-persistence.md)
- **Roadmap:** [`practical-implementation-roadmap.md`](../../framework/practical-implementation-roadmap.md)
- **Storage:** [`projects/shared-storage/`](../shared-storage/README.md)
- **Scale counterpart:** [`projects/ledger-lab/`](../ledger-lab/README.md) runs the same fixture
  at volume
- **Implementation repo:** `tool-git-ledger` (separate repository)
