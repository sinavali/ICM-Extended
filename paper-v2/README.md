# ICM v2 — Enhanced Methodology

**Status:** active development. Version **2.2.1**.
**Started:** 2026-10-04.
**Derived from:** *Interpretable Context Methodology: Folder Structure as Agent Architecture* —
Jake Van Clief and David McDermott, Eduba / University of Edinburgh,
[arXiv:2603.16021v2](https://arxiv.org/html/2603.16021v2).

---

## 1. What this tree is

[`../paper/`](../paper/) holds the published article **verbatim**, split by section, with
its original wording untouched. It is the citation target and the fidelity baseline. It
is never edited.

`paper-v2/` is the **enhanced methodology**: the same architectural pattern, carried
forward to the point where it can be applied and audited without finding a hole. Where
`paper/` says *what ICM is*, this tree says *how to build one, how to check one, how to
keep one correct, and what happens at every boundary the original paper left open*.

Nothing here replaces or contradicts the published claim. Every v2 addition is either

1. a **formalisation** of something the paper already states informally, or
2. an **extension** covering a case the paper declares out of scope, or
3. a **correction** of something the paper states inconsistently.

Each is tagged with its kind in the [changelog](CHANGELOG.md), so an auditor can tell
instantly whether a change strengthens the original thesis or extends past it.

## 2. The universality bar

The methodology is treated as **architecture-agnostic**. It must be usable, in full, on:

| Axis | Must work identically for |
| --- | --- |
| Source language | any language, including polyglot and no-code/domain-specific |
| Artifact medium | text, markdown, JSON, images, audio, video, PDF, opaque binaries |
| Codebase topology | greenfield, single module, monorepo, polyglot, vendored, legacy |
| Workflow shape | single stage, linear, conditional, looping, DAG, state machine |
| Execution model | interactive, batch, scheduled, event-driven, streaming |
| Team and tenancy | one practitioner, small team, multi-tenant enterprise |
| Scale | 1 stage to 40+; 2k tokens to context-window limits |
| Host | laptop, workstation, CI runner, cluster, serverless |
| Compliance regime | none, EU AI Act, sectoral audit, internal policy |

**Test applied to every term and every rule added to this tree:** if applying it requires
changing its meaning for any cell in that table, the rule is *qualified* and the
qualification is written down. A rule that is only true for markdown workspaces
containing Python scripts is not a rule of ICM — it is an example, and it is labelled as
one.

This is the standard the whole 72-item worklist is measured against. See
[WORKLIST.md](WORKLIST.md) for per-item status.

## 3. How versioning works here

Three files carry the audit trail, and all three are append-only in meaning — later
entries never rewrite earlier ones, they supersede them explicitly.

- **[CHANGELOG.md](CHANGELOG.md)** — every addition, removal and correction, one entry
  per completed work item. Each entry records the driver, the files touched, the
  universality check applied, and how to re-verify.
- **[WORKLIST.md](WORKLIST.md)** — the 72 requested items collapsed into 36 distinct
  concerns, with the paper evidence for each, the artifact that satisfies it, and
  status.
- **[DEVIATIONS.md](DEVIATIONS.md)** — every place the verbatim paper is internally
  inconsistent, self-contradictory, or tied to a specific tool. Carried, not silently
  fixed, so the difference between the published record and v2 is always visible.

Version numbers: **MAJOR** when a new domain of the methodology is opened,
**MINOR** when terms, rules or artifacts are added to an existing domain, **PATCH** when
an existing entry is corrected without changing the method.

Term identifiers (`ICM-###`) are stable. A term is never renumbered; a retired term keeps
its number and is marked retired in the glossary with a pointer to its replacement.

## 4. Contents

| File | What it is |
| --- | --- |
| [00-glossary.md](00-glossary.md) | The formal term register — every load-bearing term, defined once, with citation, example, anti-pattern and universality scope |
| [01-design-patterns.md](01-design-patterns.md) | Twelve design patterns and the 50-check design review checklist — how to shape a workspace and how to tell that it is shaped correctly, before it runs |
| [WORKLIST.md](WORKLIST.md) | 72 requested items → 36 concerns, with status and evidence |
| [CHANGELOG.md](CHANGELOG.md) | Append-only version history |
| [DEVIATIONS.md](DEVIATIONS.md) | Defects and tensions inherited from the verbatim paper |

Additional sections will be added as their work items land. Each new section records its
own scope, its own universality statement, and the glossary terms it introduces, so this
index stays the single entry point.

## 5. Relationship to the verbatim paper

- `paper/` is **not** modified, including where it is wrong. Corrections live here.
- Figure and table numbers cited in this tree refer to the paper's own numbering, so a
  reader holding `../paper/` can check every claim in a glossary entry against the
  original in seconds.
- Where v2 adds a mechanism the paper proposed but did not build (the `Verify` contract
  section, provenance markers, markdown breakpoints), the entry says so explicitly and
  cites the proposing section.

## 6. Attribution

The method, its five principles, its five-layer context hierarchy and the working
implementations described in §4 of the paper are the work of Jake Van Clief and David
McDermott. The protocol is MIT licensed; the upstream implementation is
[RinDig/Interpretable-Context-Methodology-ICM-](https://github.com/RinDig/Interpretable-Context-Methodology-ICM-).
This tree is a derivative study and extension, credited to them.