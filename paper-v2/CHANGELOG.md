# Changelog — ICM v2

**Append-only.** Later entries supersede earlier ones explicitly; they never rewrite
them. One entry per completed work item. Each entry records the driver, the files
touched, the universality check applied, and how to re-verify.

**Version scheme.** MAJOR — a new domain of the methodology is opened. MINOR — terms,
rules or artifacts added to an existing domain. PATCH — an existing entry corrected
without changing the method.

**Entry types.** `scaffolding` — process, no method content · `formalisation` — makes
explicit something the paper already states · `extension` — covers a case the paper
declares out of scope · `correction` — fixes something the paper states inconsistently.

---

## v2.2.1 — 2026-10-04 — delivery audit of C1 and C2

**Type:** correction
**Driver:** the standing test for a work item is *"complete without gaps and
un-concreteness"*, applied to the shipped files rather than to the notes made while
writing them
**Files:** [WORKLIST.md](WORKLIST.md) and [DEVIATIONS.md](DEVIATIONS.md) — `modified`;
[README.md](README.md) — version bump only

No definition, term, pattern or check changed. Every defect below was in the
**bookkeeping that tells a reader what state the work is in** — which is precisely the
kind of defect this tree exists to prevent elsewhere, so leaving them would have been the
worst possible place to leave them.

### What was wrong

1. **The worklist contradicted itself three ways.** The cluster map marked **C16** (non-text
   artifact handling) `open`, while the coverage accounting counted it `partial` and gave
   it a row in the §2 table whose own preamble reads *"These are not `done` and are not
   `open`."* Arithmetic followed: the map held 2 done / 7 partial / 27 open and the
   accounting claimed 2 / 8 / 26.

   Resolved at the cause. C16 is **`partial`**, because it is the same shape as the other
   seven — §4.3 consumes PDFs in prose and no mechanism exists, which is exactly the §2
   definition. The map row was the error, not the accounting.

2. **C15 sat in a table it contradicted.** §3.2 asserts 2,000–8,000 tokens per stage and
   nothing computes them, which is `partial` by the letter of §2's definition — but the row
   itself said *"counted as `open` for that reason."* A row arguing against the table it
   lives in is a defect whatever the verdict.

   Kept `open` and moved the argument out of the table, because the ground is real: what
   the paper carries is a **number**, not a concept. There is no prose mechanism to
   complete — only a measurement to build, and a measurement has no half-built form. It is
   now a named near-miss with its condition for changing.

3. **The deviation register declared a vocabulary it never used.** Its header defines
   `open` / `carried` / `noted`, and the summary table had no status column at all — so an
   auditor could not tell, from the register alone, which defects were already discharged.
   Column added and populated. It now reads against the worklist: **D-05 and D-12 are
   `carried`** because their owners are finished (restated in v2; C1), and the other ten
   are `open` because their owning concerns are not.

### Verification

31 automated checks across all six files, all passing, and three of them were *changed*
because the original assertion was wrong rather than the file:

- Checks previously run against `### D-##` headings when the register uses `## D-##`, which
  reported all twelve deviations as missing when all twelve exist.
- A check comparing README's contents table against the directory included `README.md`
  itself, which a README correctly does not list.
- A check asserting every pattern had an enforcing check compared against `DP-01`–`DP-13`;
  the correct assertion is `DP-01`–`DP-10` plus `DP-12`, because DP-11's exception is
  documented in the coverage table rather than papered over.

Each was corrected to assert what is actually required, not relaxed to make it pass.

New checks added by this audit, all passing:

- every status value used in the glossary is one §3 documents;
- every deviation's status agrees with its owner's worklist status — the one rule that
  stops a register and a worklist drifting apart;
- `DRC-01`–`DRC-50` have no gaps and no duplicates (the earlier check counted headings but
  never verified the *sequence*);
- all nine conditional review groups exist, one per universality axis, and each states its
  activation condition;
- every work item marked `done` names an artifact that resolves.

**Verification.** `paper/` and `figures/` remain byte-identical; `git status` shows changes
only under `paper-v2/`.

---

## v2.2.0 — 2026-10-04 — C2, Design patterns and the design review checklist

**Type:** formalisation
**Driver:** Issues #2
**Closes:** 1 requested item
**Depends on:** C1 — every term used here is cited by its `ICM-###` ID and means what the
glossary says it means

### Files

| Action | File |
| --- | --- |
| added | [01-design-patterns.md](01-design-patterns.md) |
| modified | [README.md](README.md) — contents index, version bumped to 2.2.0 |
| modified | [WORKLIST.md](WORKLIST.md) — C2 set to `done`, §1 completion note, coverage accounting |
| modified | this file |
| removed | none |

`../paper/` was **not** modified, and `00-glossary.md` was **not** modified — this entry
introduces no new term and no new deviation, and says so in §7 of the new file.

### What changed

The paper gives principles, not vocabulary. Three decisions load-bearing throughout its
architecture are never named: a stage is a contract before it is a prompt; a stage's
context is a declaration rather than a consequence of file placement; the boundary between
stages is a directory rather than an event. Unnamed decisions cannot be chosen
deliberately, cannot be taught, and cannot be reviewed.

The second gap costs more. Every interpretability property the paper claims is a claim
about a *run*. A design review is about a *folder*, before any model is called. Without
that, the only feedback available is a failed run — which has already cost money and
produced an artefact somebody trusts.

What the new file contains:

- **12 patterns** (`DP-01`–`DP-12`), each in a ten-field record. Nine extracted from the
  paper; three formalised from mechanisms §6 proposes and explicitly says *"are not yet
  implemented"*, marked as proposals so they cannot be mistaken for shipped practice.
- **50 review checks** (`DRC-01`–`DRC-50`) in seven groups: structure, contracts, content
  layers, then **nine conditional groups keyed one-to-one to the universality axes**. That
  structure is how this file meets the bar — it is a core plus nine conditional lists, not
  one list bent to fit every cell, so no axis is over-served and none is silently skipped.
- **5 checks that cannot be answered from a folder** (`DRC-46`–`DRC-50`), kept with stable
  IDs and named owning concerns rather than dropped, so a reader can see the five things
  the method does not yet know about itself.
- **A review record** written in the workspace like any other artifact, with three
  verdicts: pass, finding, and `unverified`.

### Universality check applied

Every pattern was written against `02_refactor` — a polyglot API rename across TypeScript,
Go and SQL — rather than against the paper's content-production examples, for the reason
C1 set out. Four patterns turned out to need a **written qualification** rather than a
passing mention, and those qualifications are the most load-bearing part of the file:

| Pattern | Axis that qualifies it | What actually breaks |
| --- | --- | --- |
| DP-01 numbering | workflow shape, host | A total order is not a dependency relation ([D-09](DEVIATIONS.md#d-09)); and ordering is *the host's sort*, which case-insensitive and non-ordering filesystems change. |
| DP-03 scoping | execution model | A declaration is only a constraint if something enforces it. On a host with unrestricted tree access the Inputs section is **advisory**, and the paper's token claim is then unestablished. |
| DP-04 folder-as-handoff | host | Assumes coherent reads. An object store or an eventually-consistent mount breaks the pattern's core assumption, and breaks it silently. |
| DP-06 factory/product | execution model, team | "Human-maintained" is a claim about *who writes it*; under concurrent runs a promoted cache reconfigures the factory between runs. |
| DP-10 delegation | host | Delegation is a host capability. A workspace may not assume it, and where it is assumed but absent the substitution is invisible in the folder. |
| DP-12 workspace-as-artifact | host | "Copy the folder" assumes stable paths, a filename charset, and no length ceiling. |

Each qualification is written as a *condition plus a check*, so it produces a reviewable
finding rather than a caveat. That is the difference between applying the universality bar
and merely citing it.

### Why `unverified` is a first-class verdict

Three checks require judgement about work the reviewer may not understand. A review format
with only pass/fail forces such an item to be recorded as a pass, asserting something
nobody checked — in a method whose entire thesis is that state should be inspectable. So
the third verdict exists, and §5.2 says why its cost (a review that looks incomplete) is
worth paying.

### The one honest limit

**[DP-11](01-design-patterns.md#dp-11--verification-as-a-stage) has no enforcing check.**
It cannot have one: the pattern is a §6.2 proposal, so no shipped workspace declares a
`Verify` section yet. The check belongs in C3's schema, where `Verify` becomes a real
field. This is recorded as a row in the coverage table rather than resolved by inventing
a check against a mechanism that does not exist.

### Verification

12 automated checks over the new file, all passing, plus a whole-tree link check:

- `DP-01`–`DP-12` present and complete; every pattern carries all ten record fields.
- `DRC-01`–`DRC-50` present with no gaps; every check states what is checked, gives a
  *how* or a *why*, and names either the pattern it enforces or the concern that owns it.
- Every pattern is enforced by at least one check, with the DP-11 exception stated in the
  coverage table rather than hidden.
- Every anti-pattern `AP-01`–`AP-11` is named by at least one check.
- All relative file links and all in-page and cross-file anchors resolve, checked by
  regenerating heading slugs for every file in `paper-v2/` and matching every link
  against them.
- No placeholder link, no leftover append sentinel.

---

## v2.1.1 — 2026-10-04 — C1 closure audit

**Type:** correction
**Driver:** the completion test for C1 was "no gaps and no un-concreteness", so the glossary
was re-validated against its own §3 record format rather than against the paper
**Files:** [00-glossary.md](00-glossary.md) only — `modified`

The first validation pass used the wrong field names for the contrast records and produced
a false failure list. Re-running with the format each record type actually uses surfaced
three real defects, all bookkeeping rather than method content:

1. **`partial` status was undocumented.** §3 listed four statuses;
   [ICM-027](00-glossary.md#icm-027--review-gate) and
   [ICM-029](00-glossary.md#icm-029--skill-file) used a fifth. Added it, with the rule that
   a partial record is readable but **not buildable** until its owning concern replaces it —
   otherwise a provisional definition would be quoted as settled.
2. **Three allocated gaps.** The identifier space reserved `ICM-038`, `ICM-039` but not
   `ICM-037`, `ICM-068` or `ICM-069`, so five slots in the 001–079 range had no owner.
   Now explicit: `037`–`039` held for execution concerns (C10, C11), `068`–`069` for the
   oversight and record layer (C21).
3. **An unenforceable format rule.** §3 asserted a `*Correction:*` / `*Deviation:*` form
   that no record used. Replaced with the form in use (`**Qualification.**` plus a
   deviation link on the `*Universality:*` line) and an escape clause — a qualified record
   may instead state **why no deviation exists**, which is the case when the paper's
   *examples* are narrower than its *criterion*.
   [ICM-034](00-glossary.md#icm-034--local-script) now says so explicitly.

**Universality check applied.** None needed: no definition changed, so no term's meaning
moved along any axis. The corrections only make the register self-describing.

**Verification.** 14 automated checks over `00-glossary.md`, all passing: record count and
ID uniqueness; per-record-field completeness for core and contrast records separately;
a `Universality` line on every record; a `../paper/` citation on every record whose status
is `core`; a `Qualification` body and a deviation-or-reason on every `qualified` record; a
named owning concern on every `partial` record; zero broken in-page anchors (heading slugs
regenerated and matched against every link); zero unresolved relative links; anti-pattern and
verified-consistency ID sets complete; reserved slots exactly `ICM-090`…`ICM-110`; no
`APPEND-MARKER` sentinel left in the file.

---

## v2.1.0 — 2026-10-04 — C1, Formal glossary

**Type:** formalisation
**Driver:** Issues #1, Gaps #1, Additions #1
**Closes:** 3 requested items
**Depends on:** nothing — this is the vocabulary foundation

### Files

| Action | File |
| --- | --- |
| added | [00-glossary.md](00-glossary.md) |
| modified | [README.md](README.md) — contents index |
| modified | [WORKLIST.md](WORKLIST.md) — C1 set to `done`, coverage accounting updated |
| removed | none |

`../paper/` was **not** modified. The glossary cites it; it does not correct it.

### What changed

The paper introduces its vocabulary in passing prose and never defines it centrally.
`stage contract`, `working artifacts`, `reference material`, `handoff`, `edit surface` and
`review gate` each appear once in a sentence, in six different files, with no definition
and no shared referent. `Inputs table` is called a table in two places and rendered as a
bullet list in a third. `skill file` is named as a Layer 3 content type and never defined
at all.

`00-glossary.md` is now the single authority. It provides:

- **74 term records** (`ICM-001`…`ICM-079`) across 7 groups — foundations, context
  hierarchy, contracts, execution, method properties, the compiler lineage, and oversight —
  each with a canonical definition, the paper section that defines it, a worked example
  drawn from a domain other than content production, its anti-pattern, and an explicit
  **universality statement** naming which axes the term is independent of.
- **A contrast table** (ICM-070…ICM-079) separating ten terms that collide with ordinary
  usage — *agent*, *context*, *pipeline*, *cache*, *memory* — where the ICM sense and the
  ordinary sense diverge. This is the failure mode a glossary has to prevent: a reader who
  takes "cache" to mean a prompt cache will build the wrong thing.
- **An anti-pattern catalogue** (§12) keyed by anti-pattern ID, each naming the terms it
  corrupts, the observable symptom, and the check that catches it. `AP-01` through `AP-12`.
- **A reserved-slot register** (§14) listing the 21 terms that later work items will
  require — `run_id`, `deps`, `token_estimate`, `manifest`, and so on — each marked
  *reserved, owner named, not yet defined*.
- **A verified-consistencies table** (§11) recording seven places where two parts of the
  paper agree, so an audit does not re-derive them — including that
  [Figure 1](../figures/figure-1.svg)'s per-layer token figures sum to §3.2's stated
  Layers 0–2 aggregate.

That last part is the reason this item is complete rather than aspirational. The worklist
has 35 concerns still open, and roughly two dozen of them need new vocabulary. Recording
those slots now, explicitly undefined, prevents this glossary from being written twice and
prevents an early definition from being contradicted by a later one.

### Universality check applied

Every term record carries a **Universality** line stating which of the nine axes in
[README.md §2](README.md#2-the-universality-bar) the term is independent of, and whether
it is qualified for any of them. The check that mattered most was the codebase-topology
axis: the paper's examples are all content-production workspaces, so a term inherited
wholesale could have smuggled in "markdown folder" as a precondition. Each term was
therefore restated against at least one non-content example — a code refactor, a data
pipeline, an infrastructure change — and any term that could not survive that restatement
was qualified in place.

Three terms emerged **qualified** and say so:

- `ICM-034 local script` — the paper uses it for the mechanical work that needs no AI
  (fetching, moving, formatting, emailing). That role is tool-specific. v2 restates the
  term by *criterion* — work whose output is fully determined by its input — not by
  example, so it holds for compilers, linters, formatters and provisioners.
- `ICM-041 plain text as the interface` — qualified by D-02. The paper states it as "no
  binary formats" and then feeds PDFs into a shipped workspace. v2 keeps the principle and
  corrects its scope to *undeclared conversion*.
- `ICM-052 proto-debugger` — a term the paper uses for one specific artifact. v2 keeps it
  but marks it an instance of `ICM-051 provenance`, so a reader does not generalise from
  one audit file.

### Why it matters downstream

C3 writes a schema; C6 defines gates; C16 declares artifact types; C23 declares `deps`.
All four need the same field names with the same meanings. Every one of them would
otherwise have invented its own vocabulary, and the paper's own inconsistency would have
been reproduced four more times.

### Verification

- 74 term records, IDs `ICM-001`…`ICM-079`, no duplicates, verified by extraction.
- Every `§`-bearing citation resolves to a file under `../paper/`.
- Every term attributed to the paper carries a universality line; every v2-only term is
  marked `v2 addition` and carries no false attribution.
- 12 anti-patterns, each naming ≥1 term it corrupts and ≥1 check that catches it.
- 21 reserved slots, each naming its owning concern from [WORKLIST.md](WORKLIST.md).
- All relative file links and all in-page anchors resolve — checked by generating heading
  slugs for every file and matching every link target against them.
- `../paper/` unmodified; `git status` shows changes only under `paper-v2/`.

---

## v2.0.0 — 2026-10-04 — bootstrap

**Type:** scaffolding
**Driver:** repository layout and audit process
**Files added:** [README.md](README.md), [WORKLIST.md](WORKLIST.md),
[DEVIATIONS.md](DEVIATIONS.md), this file

Established the enhanced tree alongside the verbatim one, and fixed the process before any
method content was written:

- **`../paper/` stays verbatim**, including where it is wrong, so the published record
  stays citable and every v2 claim can be checked against it in seconds.
- **The universality bar** ([README.md §2](README.md#2-the-universality-bar)) — nine axes
  across which the method must behave identically, with a written test for qualification.
- **The deviation register** ([DEVIATIONS.md](DEVIATIONS.md)) — 12 entries where the
  paper is inconsistent, self-contradictory, or tied to one tool. Recorded, not fixed in
  place. Four are high severity: D-01 (observability claimed as free, no run record
  exists), D-02 (binaries forbidden, then consumed), D-03 (the contract's central field is
  called a table and shown as prose), D-07 (Figure 1 hardcodes a vendor filename inside a
  model-agnostic protocol).
- **The cluster map** ([WORKLIST.md](WORKLIST.md)) — 72 items collapsed to 36 concerns,
  with the four ordering inversions in the requested list recorded and resolved in
  advance, so a later item does not discover that it was specified before its dependency.
- **Version and identifier discipline** — semantic versions here, stable `ICM-###` term
  IDs, `AP-##` anti-pattern IDs, `D-##` deviation IDs, `C##` concern IDs. A retired term
  keeps its number and points at its replacement.

**Verification.** `paper-v2/` contains four files. No file outside `paper-v2/` is touched
by this entry.