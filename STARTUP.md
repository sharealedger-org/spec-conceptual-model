# Startup Checklist — Sharealedger Conceptual Model

Read this at the start of every working session before doing anything else.

---

## Every session

1. **Read [`README.md`](README.md)** — repo structure and what lives where
2. **Read [`work-log.md`](work-log.md)** — open items, last session summary,
   confidentiality notes
3. **Ask the user** — "Which project do you want to work on, or is this a
   framework/repo-level session?" Then read only that project's files.

## Only after the user chooses a project

4. **Read `projects/<name>/log.md`** — what was done last session and current state
5. **Read `projects/<name>/todo.md`** — active task list; check HIGH items first
6. **Read `projects/<name>/README.md`** only if you need deeper context

## Active projects

| Project | README | Todo | Log |
|---|---|---|---|
| Hospice App | [`projects/hospiceapp/README.md`](projects/hospiceapp/README.md) | [`todo.md`](projects/hospiceapp/todo.md) | [`log.md`](projects/hospiceapp/log.md) |
| Shared Storage | [`projects/shared-storage/README.md`](projects/shared-storage/README.md) | [`todo.md`](projects/shared-storage/todo.md) | [`log.md`](projects/shared-storage/log.md) |
| Ledger Lab | [`projects/ledger-lab/README.md`](projects/ledger-lab/README.md) | [`todo.md`](projects/ledger-lab/todo.md) | [`log.md`](projects/ledger-lab/log.md) |

---

## Repo structure

```
conceptual-model/
├── STARTUP.md          ← you are here
├── README.md           ← full repo guide
├── work-log.md         ← repo-level session log and open items
│
├── framework/          ← generalized principles for shared ledgers
│   ├── open-accounting-framework.md
│   ├── open-accounting-model-brief.md
│   └── practical-implementation-roadmap.md
│
├── projects/           ← goal-directed work threads (what to build)
│   ├── hospiceapp/     ← hospice caregiver shared ledger
│   └── ledger-lab/     ← reference implementation spec
│
└── sources/            ← contributed materials (copyright retained by contributors)
    ├── theoretical-foundation.md
    ├── VA Data Samples and Specs.xlsx
    ├── GenevaERS/
    └── presentations/
```

---

## Confidentiality rule

Do not use specific institution names in this public repo. Use generic descriptions:
- "global financial institution" or "major global bank"
- "major U.S. insurer"
- "major U.S. agency" for VA / government context

See `work-log.md` for the full confidentiality notes and history of fixes.
