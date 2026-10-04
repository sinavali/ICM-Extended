# Worklist — 72 requested items, 36 distinct concerns

**Status of this file:** living. Updated at the end of every completed work item.
**Last updated:** 2026-10-04.

## How to read this

The requested list contains 72 numbered items. Thirty-six of them are the same concern
stated twice or three times under different names, sometimes in different categories.
Collapsing them is not a judgement about importance — it is bookkeeping, so that one
artifact satisfies every item that describes it and the audit can show which items a
single piece of work closed.

Status values:

- `done` — artifact written, verified, changelog entry exists
- `partial` — the paper already carries part of it; the remainder is pending
- `open` — nothing written yet
- `blocked` — waiting on a named predecessor

**Rule in force:** an item advances to `done` only when its artifact is complete without
gaps or un-concreteness, or when a named other concern fully covers it. `partial` is not
an exit state.

## Cluster map

| # | Concern | Requested as | Paper status | Artifact | Status |
| --- | --- | --- | --- | --- | --- |
| C1 | Formal glossary | I1, G1, A1 | absent | [00-glossary.md](00-glossary.md) | done |
| C2 | Design patterns + design review checklist | I2 | absent | [01-design-patterns.md](01-design-patterns.md) | done |
| C3 | Stage contract schema + validator | I3, E5 | absent, and contradicted — see D-03 | — | open |
| C4 | Prompt/context caching | I4, E3 | absent | — | open |
| C5 | Pre-stage input existence validation | I5 | absent | — | open |
| C6 | Declared gates + automated validation | I6, E2 | partial — gates exist as concept only | — | partial |
| C7 | Benchmarks vs monolithic and framework pipelines | I7 | admitted absent in §4.6 | — | open |
| C8 | Context overflow: compress, chunk, fall back | I8, E10, G7, A8 | absent | — | open |
| C9 | Versioning and rollback | I9, E11 | partial — §3.4 narrates Git history | — | partial |
| C10 | Locking, run IDs, atomic writes | I10, A14 (part) | absent | — | open |
| C11 | Retry, resume, checkpoints, breakers | I11, E9, G5, A7 | absent | — | open |
| C12 | Automated context scoping / dependency analysis | I12 | absent | — | open |
| C13 | Model-specific prompt adapters | I13 | absent | — | open |
| C14 | Automatic stage splitting by token count | I14 | absent | — | open |
| C15 | Token estimation, budgets, quotas | E8, G3, A3 | asserted in §3.2, never computed | — | open |
| C16 | Non-text artifact handling | I15, G6, A6 | partial — §4.3 consumes PDFs; §3.1 forbids binaries — D-02 | — | partial |
| C17 | DAG scheduling and concurrency | I16, G10, A11 | absent; §5.2 scopes out | — | open |
| C18 | Monorepo and polyglot | I17 | absent | — | open |
| C19 | Large-codebase scaling | I18 | absent | — | open |
| C20 | Retrieval, indexing, code intelligence | I19, E15, G15, A13 | absent | — | open |
| C21 | Secrets, audit, RBAC, sandboxing | I20, E12, E14, G2, G13, G16, A2, A5, A14 | partial — §5.3 argues alignment, no mechanism | — | partial |
| C22 | CLI scaffolding and templates | E1 | partial — §4.4 workspace-builder already does this | — | partial |
| C23 | Stage dependency graph (`deps`) | E6 | partial — §6.1 implies it, never formalised | — | partial |
| C24 | Model routing rules | E7 | partial — §4.1 delegation is implicit routing | — | partial |
| C25 | Structured logging and tracing | E4 | absent; §5.3 argues against — see D-01 | — | open |
| C26 | Artifact diff, merge and review UI | E13 | absent | — | open |
| C27 | Evaluation harness, rubrics, regression metrics | G8, A9 | absent | — | open |
| C28 | Event triggers and pub/sub | G4, A4 | absent | — | open |
| C29 | Branching, loops, workflow definition language | G9, A10 | absent; §5.2 human-only | — | open |
| C30 | State machine with guarded transitions | G11 | absent | — | open |
| C31 | Orchestration API and SDKs | G12, A12 | absent | — | open |
| C32 | Streaming and incremental execution | G14 | absent | — | open |
| C33 | Plugin and skill registry | G17, A15 | partial — §3.2 names skill files, no registry | — | partial |
| C34 | Migration tooling for existing repos | G18, A16 | absent | — | open |
| C35 | Multi-user collaboration and conflict | G19, A14 (part) | absent | — | open |
| C36 | Distributed execution and worker pools | G20, A17 | absent | — | open |

Legend: `I` = Issues, `E` = Enhancements, `G` = Gaps, `A` = Additions. Numbers are the
positions in the requested list, which are **not** complexity order within a category —
see §3 for the inversions that had to be corrected.

## Coverage accounting

- Items requested: **72**
- Distinct concerns: **36**
- Concerns `done`: **2** — C1, C2
- Concerns `partial`: **8** — C6, C9, C16, C21, C22, C23, C24, C33
- Concerns `open`: **26**
- Concerns `blocked`: **0**

Items closed: **4** of 72 (I1, G1, A1 by C1; I2 by C2). Next item: **C3** — the
machine-readable stage contract schema and its validator, per Issues #3 and
Enhancements #5.

## 1. Completed

### C1 — Formal glossary — v2.0.0, 2026-10-04

Closes Issues #1, Gaps #1, Additions #1.

The paper defines its vocabulary inline and never centrally: *stage contract* is
introduced in `../paper/03-interpretable-context-methodology/3.3-stage-contracts-and-handoffs.md`,
*working artifacts* and *reference material* in `../paper/03-interpretable-context-methodology/3.2-architecture.md`,
and a reader has to reconstruct the rest from prose.

`00-glossary.md` defines every load-bearing term once, with the paper section that
defines it, a worked example, an anti-pattern, an explicit statement of which universality
axes the term is independent of, and — for terms that collide with ordinary usage — an
explicit list of what the term is **not**.

It also carries a reserved-slot register (§8) so terms that later work items will need
(C22 CLI, C23 `deps`, C15 token budget, C10 run IDs, and the rest) are recorded as
*reserved, owner named, not yet defined*, rather than being invented now and contradicted
later.

### C2 — Design patterns and the design review checklist — v2.2.0, 2026-10-04

Closes Issues #2.

The paper states five principles and one architecture and then stops. Nothing names the
recurring structural choices it depends on — that a stage is a *contract* before it is a
prompt, that its context is a *declaration* rather than a consequence of where files sit,
that the boundary between stages is a *directory* rather than an event. And there is no
way to examine a workspace that has not yet run: every interpretability claim in the paper
is a claim about a run, so the only available feedback is a failed one.

`01-design-patterns.md` closes both gaps:

- **Twelve patterns**, each in a ten-field record — problem, forces, structure, a worked
  example in a *non-content* domain (`02_refactor`, a polyglot API rename), consequences,
  an explicit applicability limit, the anti-patterns that corrupt it, the paper section
  that supports it, and the universality axes it survives unchanged. Nine are extracted
  from the paper; three are formalised from mechanisms §6 *proposes* and says are not
  built, and are marked as proposals so no reader mistakes one for shipped practice.
- **Fifty review checks** in seven groups. A core of structural and contractual checks
  that always applies, and nine conditional groups keyed one-to-one to the universality
  axes, so the checklist meets the bar by being *nine lists plus a core* rather than one
  list bent to fit every cell. Five checks that genuinely cannot be answered from a
  folder are listed with stable IDs and named owners instead of being quietly dropped.
- **A review record format** with exactly three verdicts — pass, finding, **unverified** —
  where `unverified` is first-class, because a review format that cannot record its own
  epistemic state is a defect in a method whose thesis is that state should be inspectable.

Nine of the twelve patterns carry at least one enforcing check. The exception is
[DP-11](01-design-patterns.md#dp-11--verification-as-a-stage), which cannot have one
until the `Verify` section becomes a real field in C3's schema — recorded in the coverage
table rather than papered over.

## 2. Partially present in the paper, remainder pending

These are not `done` and are not `open`. The paper carries the concept in prose; the
mechanism does not exist. Each names what is missing.

| Concern | Carried by | What is missing |
| --- | --- | --- |
| C6 gates | Fig. 4 caption, §5.3, §6.3 | a way to *declare* a gate rather than assume one exists at every boundary |
| C9 versioning | §3.4 | a restore procedure; what to do when no VCS is present |
| C16 non-text artifacts | §4.3 (PDFs consumed) | any declared handling; §3.1 forbids binaries outright — D-02 |
| C21 enterprise | §2.3, §5.3 | every mechanism. Alignment with oversight principles is argued, nothing is built |
| C22 CLI | §4.4 | the workspace-builder is described in prose only; no artefact, no templates, no invocation |
| C23 dependency graph | §6.1 | the `deps` field itself; staleness is described as a consequence, not specified |
| C24 model routing | §4.1 | a declared routing rule with fallback, rather than an observed delegation habit |
| C33 skill registry | §3.2 | skill *files* are named as Layer 3 content; no manifest, lifecycle or registry |

**One near-miss, recorded so it is not re-litigated.** C15 (token estimation, budgets,
quotas) is in the same shape — §3.2 asserts 2,000–8,000 tokens per stage, and the mechanism
that would compute it does not exist. It is nonetheless counted `open`, not `partial`, on
one ground: what the paper carries is a **number**, not a concept. There is no prose
mechanism to complete, only a measurement to build, and a measurement has no half-built
form. If C15 ever becomes concept-in-prose it moves here; until then it stays in the
`open` row of the cluster map.

## 3. Ordering inversions found in the requested list

The list is ordered lowest to highest complexity *within* each category, and the category
order is respected. Four inversions sit inside it and are handled as follows.

### 3.1 Issues #7 (benchmarks) precedes the tooling needed to run them

Benchmarks cannot be honestly completed without an estimator and an evaluation harness,
which arrive later as Enhancements #8 and Gaps #8.

**Resolution.** C7 is completed in two declared phases. Phase 1 — the benchmark protocol
(hypotheses, metrics, controls, statistical plan) plus a **token-efficiency harness that
runs locally and deterministically**. This is not a workaround: the paper's central
efficiency claim (2,000–8,000 tokens per stage against 30,000–50,000 monolithic) is a
token-count claim, and token counting is exactly reproducible offline. Phase 2 — the
model-judged quality comparison — is delivered under C27 and is reported as
`C7 phase 1 done / phase 2 tracked under C27`, never as `done`.

### 3.2 Issues #12 (scoping) and Issues #19 (retrieval) are one subsystem

**Resolution.** C12 is scoped to *file-graph dependency analysis* — which files a stage
actually reads, derived from declared inputs plus observed reads. Retrieval and
code-intelligence remain C20 and are not touched by C12.

### 3.3 Issues #14 (auto stage splitting) needs an estimator that Enhancements #8 later formalises

**Resolution.** C14 introduces a minimal shared token-counting routine and registers it
as provisional. C15 promotes it to the canonical estimator and may revise its interface;
C14 is updated to match. The dependency is recorded so an auditor sees the earlier version
was provisional, not accidental.

### 3.4 Ordering that is already correct and must not be disturbed

- Enhancements #6 (`deps`) precedes Gaps #9, Gaps #11 and Additions #10. All three need
  the dependency substrate, and they come after it.
- Gaps #10 and Additions #11 precede Gaps #20 and Additions #17. Distributed execution
  presupposes a DAG executor, and it comes after one.

## 4. Not requested, found during the analysis, and folded in

The user's mandate is to fix all issues, and three defects in the verbatim paper fall
outside the 72 items. They are logged as deviations and fixed **here**, not in `paper/`.

| Ref | Defect | Folded into |
| --- | --- | --- |
| D-04 | §6.2 cites "Section 4.4" for the U-shaped intervention pattern; it is reported in §4.5 | C27 (evaluation) and the §4.5 record |
| D-05 | Ten subsection headings (2.1–2.3, 5.1–5.4, 6.1–6.3) are plain paragraphs, not headings — no outline, no anchors | Fixed when v2 restates §2, §5 and §6 |
| D-06 | §3.1 promises "markdown and JSON files" but no JSON file is ever named or shown | C3 (schema) |