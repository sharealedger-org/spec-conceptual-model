# Perspective Derivation and Persistence

**Status:** Proposed decision record for community discussion. Not ratified. Layer affected:
conceptual rule, with a conformance test that constrains physical realizations.

---

## Question

When two parties share an economic event, each has its own accounting perspective on it. Should a
system **generate** each perspective from the shared event when needed, or **store** each
perspective as a record?

The same question appears in several places:

- the transaction/balance trade-off: compute a position on demand, or materialize it at posting time;
- the physical choice between processing in memory and processing through sorted, buffered
  access paths (for example Common Key Buffering) as volumes grow;
- whether a counterparty's journal entry belongs in the shared partition or in that party's
  private partition; and
- whether a stand-alone ledger keeps its own copy of events that are also shared.

## Why storage matters

Stored data is not a problem in itself. Stored data creates reconciliation when there are
**two independent sources of truth** for one fact.

| | Independent record | Derived cache |
|---|---|---|
| Example | Each party keys or imports its own entry for the same event | Each party's entries are generated from shared events plus that party's rules and reference data |
| Sources of truth | Two or more | One (the shared events plus versioned rules and reference data) |
| Check needed | Reconciliation between records | Regenerate and compare |
| Redundancy | Real | Physical only; may be discarded and rebuilt |

## Candidate rule

1. A shared Event is recorded once, as the authoritative fact.
2. A participant's perspective is a deterministic function of: the Events it is authorized to
   see, its own Rule version, and a declared Reference Data generation. Private inputs are
   included in the function and recorded in the lineage, without being disclosed.
3. An implementation may materialize any perspective or Position, but only as a **derived
   cache**. A cache identifies its source cutoff (or snapshot), Rule version and Reference Data
   generation, and must be reproducible by regeneration.
4. Whether to materialize is a physical choice. It must not change any reported number.
5. Each participant derives only its own perspective. A shared partition does not hold one
   party's complete journal on behalf of another, and one participant's private rules and
   reference data are not needed by the other.

## Two kinds of persisted artifact

These have different correctness rules and should not share a name.

- **Provenance snapshot:** written for archive and audit. It is never read in place of
  derivation. Example: a periodic balance file stamped with the commit that authorized the
  underlying journals.
- **Performance cache:** read in place of derivation. It must be invalidated when a late or
  corrected Event, a Rule version, or a Reference Data generation changes.

A **period-close record** is a third case. A reported Position may need to be preserved as
reported even though it could be regenerated. Whether it is a cache with lineage or a new
authoritative fact is an open question below.

## Conformance test

For the same inputs, an implementation operating in each mode it supports must produce
identical Positions, control totals and lineage:

- derive on every request, from events;
- derive once and hold in memory;
- read from a persisted cache; and
- (at scale) read through the physical access path the implementation uses for large volumes.

An automatic switch between modes, based on data volume or any other condition, is conformant
only if this test passes on both sides of the threshold. The switch point itself is a
performance finding, measured outside the conceptual model.

## Interaction with sharing lifecycle

Ending a sharing relationship is a defined point where derived perspectives become stored
records. The service copies shared Events into each party's stand-alone ledger (copy-on-exit; see
[`projects/shared-storage/log.md`](../projects/shared-storage/log.md)). From that point the two
copies are independent records, and later transactions between the same parties again require
reconciliation unless they are shared in a new partition. Copy-on-exit is therefore a
generate-to-store transition and needs the same lineage as any other materialization.

## Resolution: period-close record is a new authoritative fact

**Added October 30, 2026.** The first open question below is resolved for the purposes of the
Small Venture Sample and proposed for general adoption:

> A **period-close record** is a new authoritative fact, not a cache. It is produced by a
> declared derivation run against a stated event cutoff, rule versions, and reference data
> generation — but it is then authorized by the participating parties and stored as an immutable
> shared event. It is not subject to invalidation by later events, rule changes, or software
> updates. The stored control totals govern; regeneration from the same inputs is a verification
> tool, not the source of authority after close. Corrections enter the next period as explicit
> events with links to the prior close.

**Why this resolves the audit reliability problem:** Auditors require inspectable, authorized
records, not a promise that a regeneration process will reproduce a number. By making the
period-close record a shared event — confirmed by both parties, carrying the rule versions and
control totals in effect at the time — the system produces an artifact that auditors can inspect
directly. The audit confirmation between counterparties is structurally replaced: the close event
is the mutual authorization of the balance at that date, agreed at the time rather than recalled
after the fact.

**Relationship to copy-on-exit:** Both are generate-to-store transitions. Copy-on-exit (§
Interaction with sharing lifecycle) transfers the relationship's full history into each party's
stand-alone ledger when sharing ends. Period close is the recurring within-relationship
materialization, producing one new authoritative fact per period. The mechanisms are the same;
the trigger and scope differ.

## Remaining open questions

- How are private-input versions identified in lineage without disclosing them?
- What invalidation rules apply when late-arriving Events change a **not-yet-closed** Position?
  (Post-close positions are not subject to invalidation; the correction enters the next period.)
- What lineage is sufficient when the unit of authorization is a repository commit (is a
  commit identifier enough when the working data differs from the committed data)?
- Which conditions, beyond data volume, should trigger a physical change (access pattern, query
  frequency, latency target)?

## Evidence from current work

- Small-volume tool: [`tool-git-ledger`](https://github.com/sharealedger-org/tool-git-ledger)
  re-derives its ledger on every run, and writes a provenance snapshot with a commit stamp when
  journal size reaches a fraction of available memory. The snapshot is not read back.
- The shared-storage log records the same principle: preserve shared facts once, derive
  authorized participant perspectives, and treat cached positions as reproducible with
  identified cutoff and rule/reference versions.
- Ledger Lab's access-path comparison (see the
  [Practical Implementation Roadmap](practical-implementation-roadmap.md), step 8) is the
  scale-side counterpart: the cost of obtaining a required order is measured separately from the
  accounting result.

## Related

- [Open Accounting Framework](open-accounting-framework.md) — selected sharing, relationship to physical systems
- [Small Venture Sample](../projects/small-venture/README.md) — first project to carry this test
