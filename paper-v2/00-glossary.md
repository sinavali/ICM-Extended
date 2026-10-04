# ICM v2 — Formal Glossary

**Version:** 2.1.0 · **Established:** 2026-10-04 · **Status:** active, extended per work item
**Driver:** Issues #1, Gaps #1, Additions #1 → [C1](../paper-v2/WORKLIST.md)
**Depends on:** nothing. Every later work item writes against this vocabulary.

---

## 1. How to read this glossary

This is the **single authority** for ICM vocabulary. The paper introduces these terms in
passing prose across six files, without defining them centrally, and in two cases uses one
name for two things and two names for one thing. Those inconsistencies are recorded in
[DEVIATIONS.md](DEVIATIONS.md) rather than corrected in place, because
[`../paper/`](../paper/) is preserved verbatim.

Rules of use:

- A term defined here **overrides** incidental usage in `../paper/` wherever the two
  differ, and the override is always flagged with a deviation reference.
- Terms are **stable and numbered**. A retired term keeps its ID and points at its
  replacement. Nothing is renumbered.
- Nothing is invented to fill a hole. Where a term is needed by an open work item but not
  yet defined, it appears in [§14 Reserved terms](#14-reserved-terms) as an owned slot,
  explicitly *not yet defined*. Defining it early and contradicting it later is worse than
  leaving it open.

## 2. The universality contract

ICM is architecture-agnostic. A term belongs to this glossary only if it holds unchanged
across all nine axes in [README §2](README.md#2-the-universality-bar):

| Axis | Range |
| --- | --- |
| Source language | any, including polyglot and domain-specific |
| Artifact medium | text, markdown, JSON, image, audio, video, PDF, opaque binary |
| Codebase topology | greenfield, single module, monorepo, polyglot, vendored, legacy |
| Workflow shape | 1 stage, linear, conditional, looping, DAG, state machine |
| Execution model | interactive, batch, scheduled, event-driven, streaming |
| Team and tenancy | one practitioner, small team, multi-tenant enterprise |
| Scale | 1–40+ stages; 2k tokens to window limits |
| Host | laptop, workstation, CI runner, cluster, serverless |
| Compliance | none, EU AI Act, sectoral audit, internal policy |

**The test.** If applying a term requires changing its meaning for any cell in that table,
the term is *qualified* and the qualification is written into its record. A rule that is
only true for markdown workspaces containing Python scripts is not a rule of ICM — it is an
example, and it is labelled as one here.

**Why the non-content examples.** The paper's worked examples are all content production:
research, script, animation, slide decks. A glossary written only against those examples
will silently encode "a folder of markdown" as a precondition. So each term below is
tested against a **second, deliberately different** domain: *`02_refactor`*, a polyglot
monorepo refactor that renames a public API across TypeScript, Go and SQL. If a term
survives both, it is a term of the method. Terms that do not survive both are qualified in
place rather than quietly dropped.

## 3. Term record format

Each record carries: the canonical term, kind, status, where the paper defines it, its
universality scope, a definition, a worked example in the non-content domain, the
anti-patterns that corrupt it, and what it must not be confused with.

- **Status `core`** — defined by the paper; v2 adds precision only.
- **Status `v2 addition`** — introduced by this tree; carries no false attribution to the
  paper.
- **Status `qualified`** — the paper's statement is narrower than the term's real scope;
  the record states the correction and links the deviation.
- **Status `partial`** — the paper relies on the term without defining it, so a provisional
  definition is recorded here and an owning concern must replace it. A partial record is
  usable for reading the paper and is *not* usable for building; the owning concern is
  named in the record.
- **Status `reserved`** — slot claimed by an open work item, not yet defined.

A status may carry a **parenthetical qualifier** — `core (as an external mechanism)`,
`v2 addition (proposed by the paper)`. The qualifier never changes the status, only its
provenance, and is always a slash, not a new status. Contrast terms in
[§13](#13-contrast-terms) carry no status: they are disambiguations of ordinary usage,
not claims about the method, so they cannot be `core`.

Two further forms appear only in `qualified` records. A `**Qualification.**` paragraph
states where the paper is narrower than the term, and the `*Universality:*` line carries a
link to the owning entry in [DEVIATIONS.md](DEVIATIONS.md). **A qualified record must
carry a deviation link, or say explicitly why no deviation exists** — the common case being
that the paper's *examples* are narrower than its *criterion*, which is an interpretation
and not a defect (see [ICM-034](#icm-034--local-script)).

---

## 4. Foundations

### ICM-001 · Interpretable Context Methodology

*Kind:* method · *Status:* core · *Defined in:* [Abstract](../paper/00-abstract.md),
[§1](../paper/01-introduction.md), [§7](../paper/07-conclusion.md) ·
*Universality:* all nine axes

**Definition.** A methodology for orchestrating AI agents across multi-step workflows in
which **folder structure performs the coordination** that a framework would perform in
code. Numbered folders are stages. Plain-text files carry the prompts and context that
tell a single agent what to do at each step. The stages' inputs and outputs are files, so
every intermediate state is readable, diffable and editable by a human.

**Example.** In `02_refactor`, `00_knowledge/` is the Layer 3 reference set — the API
surface inventory, the naming policy, the migration checklist. `03_tests/output/` is a
failing-test report from this run. Neither is special; both are files the agent reads at
a named moment.

**Anti-pattern.** AP-05, AP-01.

**Not** — a framework, a library, or a tool. ICM has no runtime; it is a convention for
arranging files.

---

### ICM-002 · Enhanced ICM (ICM v2)

*Kind:* method revision · *Status:* v2 addition · *Defined in:* this tree ·
*Universality:* all nine axes

**Definition.** The versioned form of ICM maintained in `../paper-v2/`. It preserves the
published method's claims intact and adds what the original left open: formal
definitions, machine-checkable contracts, and coverage of every case the paper declares
out of scope in [§5.2](../paper/05-discussion.md).

**Not** — a revision of the paper. The paper remains the citation target and is never
edited. Where the two differ, the difference is a logged entry in
[DEVIATIONS.md](DEVIATIONS.md) or [CHANGELOG.md](CHANGELOG.md).

---

### ICM-003 · Workspace

*Kind:* container · *Status:* core · *Defined in:*
[§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md),
[§3.4](../paper/03-interpretable-context-methodology/3.4-portability-and-reproducibility.md) ·
*Universality:* all nine axes

**Definition.** The root folder of an ICM pipeline. It is the unit of portability,
versioning, handoff and permissioning: a workspace *"can be copied to another machine,
committed to Git, emailed as a zip file, or synced through any cloud storage service"*
([§3.4](../paper/03-interpretable-context-methodology/3.4-portability-and-reproducibility.md)).
It carries its own prompts, its own context structure and its own stage definitions. There
is no server and no deployment artifact.

**Example.** `02_refactor/` is a workspace. Cloning it gives a new engineer the complete
pipeline; there is nothing else to configure.

**Anti-pattern.** AP-08, AP-10.

**Not** — a repository. A Git repository is one *optional* way to version a workspace, and
the paper treats it as compatible-by-default rather than required — see
[ICM-055](#icm-055--staleness) and concern C9.

---

### ICM-004 · Stage

*Kind:* unit of work · *Status:* core · *Defined in:*
[§3.1](../paper/03-interpretable-context-methodology/3.1-design-principles.md),
[§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md) ·
*Universality:* all nine axes

**Definition.** One step of a workflow, occupying one stage folder, with one declared
input set, one declared output set, and one contract. A stage *"handles a single step of
the workflow and writes its output to its own folder"* and obeys **one stage, one job**:
*"A stage that fetches data does not also filter it. A stage that filters does not also
format the final output."*

**Example.** `03_tests` has exactly one job: run the affected suites and write a report. It
does not fix what it finds. If the fix is worth automating, that is `04_fix`, a different
stage with a different contract.

**Anti-pattern.** AP-01, AP-06, AP-04.

**Not** — a step in a script, and not an agent. A stage is a *contract plus a folder*; the
agent that executes it is incidental and may change between runs
([ICM-050](#icm-050--model-agnosticism)).

---

### ICM-005 · Stage folder

*Kind:* structure · *Status:* core · *Defined in:*
[§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md) ·
*Universality:* independent of language, artifact medium, codebase topology, team, host,
compliance; depends on workflow shape only through *number of stages*

**Definition.** The numbered directory that materialises a stage, conventionally
`NN_name/` — the paper's own workspaces use `01_research`, `02_script`, `03_production`
([§4.2](../paper/04-working-implementations/4.2-script-to-animation-pipeline.md)). The
folder boundary *"enforces separation of concerns"*: it is what makes a stage's inputs and
outputs *findable* rather than merely described.

**Example.** `02_refactor/02_python/` and `02_refactor/03_go/` are two stage folders that
happen to share a topic. The shared topic lives in a Layer 3 reference file they both
declare, not in a folder they both happen to sit next to.

**Anti-pattern.** AP-08, AP-01.

**Not** — a namespace for tidiness. The number carries order and the name carries meaning;
a folder exists to delimit what the stage may read and write.

---

### ICM-006 · Stage numbering

*Kind:* ordering convention · *Status:* qualified · *Defined in:*
[§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md) ·
*Universality:* qualified for the workflow-shape axis — see [D-09](DEVIATIONS.md#d-09)

**Definition.** The zero-padded numeric prefix of a stage folder, which *"encodes execution
order"*. It supplies a **total order** over stages and a human-readable position in the
pipeline.

**Qualification.** The paper also writes *"Stage sequencing is the folder numbering"*
([§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md)), which
over-claims. Numbering cannot express that stage 05 may run before stage 04 finishes when
05 does not read 04's output. A total order is not a dependency relation. The declared
dependency relation is authoritative; numbering is the default order and the fallback when
no relation is declared. See [ICM-057](#icm-057--incremental-recompilation).

**Example.** `00_knowledge`, `01_inventory`, `02_refactor`, `03_tests` — read left to
right, that is the default order. It is *not* a claim that every stage must wait.

**Anti-pattern.** AP-06.

---

### ICM-007 · Run

*Kind:* execution · *Status:* v2 addition · *Defined in:* implicit throughout
[§4.5](../paper/04-working-implementations/4.5-early-practitioner-experience.md) ·
*Universality:* all nine axes

**Definition.** One execution of a stage against one set of inputs, producing one set of
outputs. The paper distinguishes runs constantly but never names the unit — *"the same
pipeline runs repeatedly with different input"* ([§5.1](../paper/05-discussion.md)), and
per-run change is the defining property of Layer 4 ([Table 2](../paper/03-interpretable-context-methodology/3.2-architecture.md),
"Changes between runs: Yes").

**Example.** Running `01_inventory` twice — once on Tuesday's branch, once on Monday's —
is two runs. Both write to `01_inventory/output/`; the second overwrites the first unless a
run identifier is introduced (concern C10, reserved as `ICM-090 run_id`).

**Not** — a pipeline execution in the CI sense; it has no fixed trigger and no fixed
boundary.

---

### ICM-008 · Pipeline (ICM sense)

*Kind:* structure · *Status:* core · *Defined in:*
[§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md),
[§3.3](../paper/03-interpretable-context-methodology/3.3-stage-contracts-and-handoffs.md) ·
*Universality:* qualified for the workflow-shape axis

**Definition.** The ordered sequence of stages within a workspace, realised as the chain
of stage folders. The paper's own gloss: *"This is prompt chaining at the filesystem
level… ICM does the same thing, but the chain is a sequence of folders and the links
between them are plain files."*

**Qualification.** A pipeline here is a **linear** chain by default. Conditional,
looping and DAG shapes are explicitly out of scope in
[§5.2](../paper/05-discussion.md) and are addressed by concerns C17, C29 and C30. Until
those land, "pipeline" means linear.

**Anti-pattern.** AP-01.

**Not** — a Unix pipeline. See [ICM-073](#icm-073--pipeline-unix-sense).

---

### ICM-009 · Handoff

*Kind:* boundary · *Status:* core · *Defined in:*
[§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md),
[§3.3](../paper/03-interpretable-context-methodology/3.3-stage-contracts-and-handoffs.md) ·
*Universality:* all nine axes

**Definition.** The transfer of an artifact from one stage to the next, realised as
*"one folder's output being another folder's input"*
([§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md)). The `output/`
directory is *"the Layer 4 handoff point"*, and it is deliberately an ordinary folder: no
queue, no message, no serialisation format.

**Example.** `01_inventory/output/api-surface.json` is the handoff artifact for
`02_refactor`. If a human edits it between the two stages, `02_refactor` reads the edited
version — *"the agent picks up the edited version"*.

**Anti-pattern.** AP-07, AP-08.

**Not** — an interface or an API. There is no contract on the wire; the contract is the
file's meaning, carried by its name and its declaring stage.

---

## 5. The context hierarchy

### ICM-010 · Layer (of the context hierarchy)

*Kind:* structural · *Status:* core · *Defined in:*
[§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md) ·
*Universality:* all nine axes

**Definition.** One of five ordered bands of context a stage may load. The layers are
numbered by *when the agent needs them*, not by importance:

| Layer | Holds | Question it answers ([Figure 1](../figures/figure-1.svg)) | Typical size |
| --- | --- | --- | --- |
| 0 | Global identity | "Where am I?" | ~800 tokens |
| 1 | Workspace routing | "Where do I go?" | ~300 tokens |
| 2 | Stage contract | "What do I do?" | 200–500 tokens |
| 3 | Reference material | "What rules apply?" | 500–2,000 tokens |
| 4 | Working artifacts | "What am I working with?" | varies |

Layers 0–2 are *structural* and total roughly 1,300–1,600 tokens
([§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md)); layers 3–4
are *content*. The table is reproduced from the paper's prose and
[Figure 1](../figures/figure-1.svg), whose per-layer labels and §3.2's aggregate figures
are mutually consistent — verified, see [§11](#11-verified-consistencies).

**Example.** A rendering agent *"might only need Layers 0 through 2"*. A refactoring agent
reads down to Layer 4. *"No agent reads everything."*

**Anti-pattern.** AP-02.

---

### ICM-011 · Layer 0 — global identity file

*Kind:* layer · *Status:* qualified · *Defined in:*
[§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md) ·
*Universality:* qualified for the toolchain axis — see [D-07](DEVIATIONS.md#d-07)

**Definition.** The workspace-level file that tells the agent **which workspace it is in,
what the folder structure contains, and where to find things**. It is the answer to
"where am I?" and is the only layer read by every stage in every run.

**Qualification.** [Figure 1](../figures/figure-1.svg) labels this layer `CLAUDE.md`,
which is one harness's filename convention, not the method's. The method specifies a
**role**; naming is a protocol decision deferred to concern C3. Until then: any file at
the workspace root serving this role is a Layer 0 file, whatever it is called.

**Example.** `02_refactor/ICM.md` names the workspace, lists its stage folders, and points
at `_config/` for conventions. Change a stage's purpose and Layer 0 changes with it —
otherwise the agent has stale beliefs about its own location.

**Anti-pattern.** AP-10.

---

### ICM-012 · Layer 1 — workspace routing file

*Kind:* layer · *Status:* qualified · *Defined in:*
[§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md),
[footnote 4](../paper/03-interpretable-context-methodology/3.2-architecture.md) ·
*Universality:* qualified for the toolchain axis — see [D-07](DEVIATIONS.md#d-07)

**Definition.** The workspace-level file answering *"given what the user wants to do, which
stage handles it, and what shared resources exist across stages"*. It is the map from
intent to stage, and it is where a workspace's shared vocabulary is declared.

**Qualification.** [Figure 1](../figures/figure-1.svg) labels this layer `CONTEXT.md`, the
same filename it gives Layer 2. The *role* differs by depth: Layer 1 routes between
stages, Layer 2 contracts one stage. Naming is deferred to C3.

**Example.** Layer 1 says: *"renaming work → `02_refactor`; convention questions →
`_config/naming-policy.md`; anything touching the SQL migration path → `02_sql` first."*
A new stage is unreachable until this file mentions it — see AP-10.

**Anti-pattern.** AP-10.

---

### ICM-013 · Layer 2 — stage contract

*Kind:* layer · *Status:* core · *Defined in:*
[§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md) ·
*Universality:* all nine axes

**Definition.** The contract governing one stage, holding its inputs, its process and its
outputs. The paper calls Layer 2 *"the control point of the entire system"*, because the
Inputs declaration is what determines everything the model sees. See
[ICM-020](#icm-020--stage-contract).

**Example.** `02_refactor/02_python/CONTEXT.md` is a Layer 2 file.

---

### ICM-014 · Layer 3 — reference material

*Kind:* layer · *Status:* core · *Defined in:*
[§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md),
[Table 2](../paper/03-interpretable-context-methodology/3.2-architecture.md) ·
*Universality:* all nine axes

**Definition.** Stable context: *"design systems, voice rules, build conventions, style
guides, domain knowledge bundled as skill files"* — *"configured once during workspace
setup and remain stable across every run of the pipeline."* These files are
**the factory**.

Its processing role is prescribed, not incidental: *"the model should write **like this**,
use **these colors**, follow **these conventions**"*. Layer 3 is to be **internalised as
constraint**.

**Example.** `_config/naming-policy.md`, `references/error-handling.md`,
`references/sql-migrations.md`. A migration guide is Layer 3 whatever it is about.

**Anti-pattern.** AP-02, AP-07.

**Not** — a cache, a constant, or a config dump. It changes when the practitioner changes
it, which is rare, and it is never written by a run. See
[ICM-077](#icm-077--cache) and [ICM-015](#icm-015--layer-4--working-artifacts).

---

### ICM-015 · Layer 4 — working artifacts

*Kind:* layer · *Status:* core · *Defined in:*
[§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md),
[Table 2](../paper/03-interpretable-context-methodology/3.2-architecture.md) ·
*Universality:* all nine axes

**Definition.** Per-run content: *"the output of the previous stage, user-provided source
material, anything specific to this particular run."* Layer 4 changes every run and is
normally written into `output/`. These files are **the product**.

Its processing role is prescribed: *"the model should transform **this research** into a
script"* — Layer 4 is to be **processed as input**.

**Example.** `01_inventory/output/api-surface.json`, `02_refactor/output/coverage.txt`.
Both are this run's results, disposable in principle and valuable in fact.

**Anti-pattern.** AP-02, AP-08.

**Not** — scratch space. A run's output is the handoff to the next stage and often the
human's edit surface, so it is not scratch.

---

### ICM-016 · The factory

*Kind:* metaphor · *Status:* core · *Defined in:*
[§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md),
[Table 2](../paper/03-interpretable-context-methodology/3.2-architecture.md),
[§3.1](../paper/03-interpretable-context-methodology/3.1-design-principles.md) ·
*Universality:* all nine axes

**Definition.** The configured part of a workspace — Layer 3 and the structural layers —
that is set up once and reused. [Table 2](../paper/03-interpretable-context-methodology/3.2-architecture.md)
gives the pairing directly: the factory is **the recipe**, and the product is **the
ingredients**. Paired with the principle **configure the factory, not the product**:
*"a workspace is set up once with the user's preferences, brand, style, and structural
decisions. After that, each run produces a new deliverable using the same configuration."*

**Example.** Duplicating a workspace and editing its Layer 3 files to target a new
audience is retooling the factory. Duplicating it and editing only the inputs is a new run
of the same factory — which is why the paper reports duplication as the dominant
practitioner move ([§4.5](../paper/04-working-implementations/4.5-early-practitioner-experience.md)).

**Not** — infrastructure. There is nothing to deploy; the factory is files.

---

### ICM-017 · The product

*Kind:* metaphor · *Status:* core · *Defined in:*
[§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md) ·
*Universality:* all nine axes

**Definition.** The per-run output of a factory: Layer 4 artifacts, *"unique to each
run"*. See [ICM-015](#icm-015--layer-4--working-artifacts).

---

### ICM-018 · Routing file

*Kind:* mechanism · *Status:* core · *Defined in:* footnote 4,
[§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md) ·
*Universality:* all nine axes

**Definition.** A Layer 1 file applied **recursively within Layer 3**. The paper's
footnote is explicit: *"Larger reference collections can include their own routing files, a
`CONTEXT.md` within a configuration or design system folder, that help agents navigate to
the right content within the collection. This is the routing pattern from Layer 1 applied
recursively within Layer 3."*

**Why it matters.** Without recursive routing, a Layer 3 collection large enough to be
useful becomes too large to load, and the stage either loads it whole — recreating the
monolithic problem inside Layer 3 — or the agent guesses.

**Example.** `references/` contains thirty documents; `references/CONTEXT.md` says *"style
disputes → `tone-and-style.md`; legal caveats → `disclaimers.md`; ignore `archive/`"*.
Then `02_refactor` loads `references/CONTEXT.md`, not thirty files.

**Not** — an index for humans. It is loaded by the agent as context.

---

### ICM-019 · Context scoping

*Kind:* mechanism · *Status:* core · *Defined in:*
[§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md),
[§3.1](../paper/03-interpretable-context-methodology/3.1-design-principles.md) ·
*Universality:* all nine axes

**Definition.** The act of deciding **which files from Layers 3 and 4, and which sections
of them**, a stage loads. The paper calls it the method's core mechanism: *"By delivering
different context to the same model at each stage, ICM changes the task the model is
performing… The model's capabilities do not change between stages. What changes is the
information it has available."*

Without it, *"an agent would either load everything in the workspace or rely on its own
judgment about what matters."* Scoping makes the selection *"explicit, editable, and
auditable."*

**Example.** `03_tests` declares `inputs: [../02_refactor/output/**, _config/test-policy.md]`
and nothing else. It does not need the naming policy, the Layer 0 file's stage list, or
any of `01_inventory/output/`.

**Anti-pattern.** AP-03, AP-09.

**Not** — summarisation. Scoping selects; it does not reduce. See
[ICM-044](#icm-044--prevention-rather-than-compression).

---

## 6. Contracts and artifacts

### ICM-020 · Stage contract

*Kind:* contract · *Status:* core · *Defined in:*
[§3.3](../paper/03-interpretable-context-methodology/3.3-stage-contracts-and-handoffs.md) ·
*Universality:* all nine axes

**Definition.** The specification a stage obeys, comprising exactly three required parts —
**what it reads (Inputs), what it does (Process), what it writes (Outputs)** — spelled out
in that stage's `CONTEXT.md`. The contract is simultaneously the instruction to the agent
and the documentation for the human: *"the markdown files that instruct the agent are
simultaneously the documentation… The instruction set and the documentation are the same
artifact."*

**Example.** The paper's own worked example, for a script stage:

```markdown
## Inputs
- Layer 4 (working): ../01_research/output/
- Layer 3 (reference): ../../_config/voice.md
- Layer 3 (reference): references/structure.md

## Process
Write a script based on the research output.
Follow the structure in structure.md.
Match the tone described in voice.md.

## Outputs
- script_draft.md -> output/
```

**Anti-pattern.** AP-03, AP-05, AP-04.

**Not** — a prompt. The contract is what the stage *is*; the agent re-derives a prompt from
it on every run, which is why the contract outlives the model
([§3.4](../paper/03-interpretable-context-methodology/3.4-portability-and-reproducibility.md)).
**A fourth part, `Verify`, is proposed by the paper and not implemented — see
[ICM-025](#icm-025--verify-section).**

---

### ICM-021 · CONTEXT.md

*Kind:* file · *Status:* qualified · *Defined in:*
[§3.3](../paper/03-interpretable-context-methodology/3.3-stage-contracts-and-handoffs.md),
[§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md) ·
*Universality:* qualified for the toolchain axis — see [D-07](DEVIATIONS.md#d-07)

**Definition.** The filename convention by which a workspace declares its Layer 1 and
Layer 2 files, and by which Layer 3 collections declare recursive routing. *The role is
what the method specifies; the name is a convention the paper adopts from one harness.*

**Qualification.** One name serves three distinct roles — Layer 1, Layer 2, and recursive
Layer 3 routing — distinguished only by location. That is workable and is not unusual, but
it is why the glossary defines the three roles separately
([ICM-012](#icm-012--layer-1--workspace-routing-file),
[ICM-013](#icm-013--layer-2--stage-contract),
[ICM-018](#icm-018--routing-file)). A single reserved filename is also how the paper's
model-agnostic claim picks up a vendor dependency it does not intend. Naming is deferred to
concern C3.

---

### ICM-022 · Inputs table

*Kind:* contract field · *Status:* qualified · *Defined in:*
[§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md),
[§3.3](../paper/03-interpretable-context-methodology/3.3-stage-contracts-and-handoffs.md) ·
*Universality:* all nine axes · **Deviation: [D-03](DEVIATIONS.md#d-03)**

**Definition.** The contract field that declares, *"exactly which files from Layers 3 and
4 the agent should load, and which sections of those files are relevant."* Each entry
carries a layer classification — Layer 3 versus Layer 4 — and the distinction is
load-bearing rather than cosmetic, because it tells the model whether to **internalise** or
to **process** the material.

**Qualification.** The paper calls it a *table* in both places that mention it and renders
it as a bullet list in the only example given. There is no column grammar, no statement of
whether a path is required, optional or generated, and no rule for directories or globs.
Because this field determines everything the model sees, it is the highest-leverage place
in the method to be vague. Formalisation is concern C3.

**Provisional grammar adopted by v2** (pending C3, which may revise it):

| Column | Required | Meaning |
| --- | --- | --- |
| Path | yes | Path relative to the stage folder; absolute paths forbidden |
| Layer | yes | `3` (reference, internalise) or `4` (working, process) |
| Req | yes | `required` — stage must not run without it · `optional` · `generated` |
| Section | no | Anchor or heading restricting the load to part of the file |
| Purpose | no | One line on why this input is needed, for human review |

**Example.** `| ../../_config/naming-policy.md | 3 | required | # Go | governs exported names |`

**Anti-pattern.** AP-03, AP-09.

---

### ICM-023 · Process section

*Kind:* contract field · *Status:* core · *Defined in:*
[§3.3](../paper/03-interpretable-context-methodology/3.3-stage-contracts-and-handoffs.md) ·
*Universality:* all nine axes

**Definition.** The contract field stating **what the stage does**. It is instruction, not
description: the paper's example is imperative — *"Write a script based on the research
output. Follow the structure in structure.md. Match the tone described in voice.md."*
Non-technical practitioners edit this field directly to change behaviour
([§4.5](../paper/04-working-implementations/4.5-early-practitioner-experience.md)):
*"adjusting tone instructions, adding constraints… and reordering the emphasis."*

**Example.** Adding `Keep generated helpers under 40 lines.` to a Process section is a
behaviour change, made with a text editor, with no code and no redeploy.

**Anti-pattern.** AP-05.

**Not** — an algorithm. It is natural language the agent interprets; the paper's
model-agnosticism claim rests on the assumption that different agents interpret it
comparably ([D-08](DEVIATIONS.md#d-08)).

---

### ICM-024 · Outputs section

*Kind:* contract field · *Status:* core · *Defined in:*
[§3.3](../paper/03-interpretable-context-methodology/3.3-stage-contracts-and-handoffs.md) ·
*Universality:* all nine axes

**Definition.** The contract field declaring **what the stage writes**, in the form
`filename -> output/`. It is what makes the handoff findable and what lets a downstream
stage declare a real input.

**Example.** `- script_draft.md -> output/`

**Anti-pattern.** AP-04.

---

### ICM-025 · Verify section

*Kind:* contract field · *Status:* v2 addition (proposed by the paper) · *Defined in:*
[§6.2](../paper/06-future-directions.md) · *Universality:* all nine axes

**Definition.** A **proposed, not implemented** fourth contract section. The paper's
wording: *"A stage contract could include a `Verify` section alongside its `Inputs`,
`Process`, and `Outputs` sections, specifying which earlier stage outputs should be checked
for consistency and what criteria to check against. The agent would run these verification
checks as part of the stage's execution and flag discrepancies before the human reviews."*

The paper justifies it from a concrete failure: animation specifications drift out of
alignment with scripts — *"timing drifts, animations reference phrases that were revised,
visual density does not match pacing"* — which it identifies as the cause of the U-shaped
intervention pattern. The existing mitigation is an audit file in the script-to-animation
workspace, described as *"a proto-debugger"*.

**Status.** Reserved. Not counted among the contract's required parts until concern C6
decides whether verification is universal or declared per stage.

---

### ICM-026 · Edit surface

*Kind:* property · *Status:* core · *Defined in:*
[§3.1](../paper/03-interpretable-context-methodology/3.1-design-principles.md),
[§3.3](../paper/03-interpretable-context-methodology/3.3-stage-contracts-and-handoffs.md) ·
*Universality:* all nine axes

**Definition.** A property of stage output that *"a human can open, read, edit, and save
before the next stage runs."* The paper states it as a design principle — **every output is
an edit surface** — and grounds it in Horvitz's mixed-initiative work and Shneiderman's
direct manipulation: *"the human works with visible, manipulable objects, and the system
picks up whatever the human left there."*

**Example.** A refactor stage emits `output/renames.md`. A reviewer corrects two entries.
The downstream stage reads the corrected file. No merge step exists and none is needed,
because the file *is* the state.

**Anti-pattern.** AP-07.

**Not** — a UI control. The property is satisfied by any mechanism that makes the artifact
editable and durable; a plain text file satisfies it fully.

---

### ICM-027 · Review gate

*Kind:* boundary · *Status:* partial · *Defined in:*
[§3.3](../paper/03-interpretable-context-methodology/3.3-stage-contracts-and-handoffs.md),
[§5.3](../paper/05-discussion.md), [§6.3](../paper/06-future-directions.md) ·
*Universality:* all nine axes

**Definition.** The review point at a stage boundary where a human inspects the output
before the next stage runs. The paper treats gates as universal — *"Review gates at every
stage boundary support dismissal (decide not to proceed, re-run the previous stage with
different input, or abandon the run entirely)"* — and cites them as one of the three things
ICM produces for free.

**Status note.** The paper describes gates as always present. Nothing in the method
*declares* one, distinguishes a mandatory gate from an ordinary review, or records that an
approval happened. That is why Issues #6 and Enhancements #2 remain open: a gate that
cannot be declared cannot be enforced, and a gate that leaves no record cannot be audited.
Concerns C6 and C21.

**Not** — an approval workflow. A review gate today is a human deciding, in their own
time, whether to proceed.

---

### ICM-028 · Output folder

*Kind:* structure · *Status:* core · *Defined in:*
[§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md),
[§3.3](../paper/03-interpretable-context-methodology/3.3-stage-contracts-and-handoffs.md) ·
*Universality:* all nine axes

**Definition.** The `output/` directory inside a stage folder, where that stage writes its
artifacts. *"The `output/` directories are the Layer 4 handoff points."* It is the physical
realisation of both the handoff and the edit surface, and the reason a stage's writes are
findable without reading its contract.

**Example.** `02_refactor/02_python/output/renames.md` — the only directory a run of that
stage may write.

**Anti-pattern.** AP-04, AP-07.

**Not** — a build directory to be cleaned. Its contents are the pipeline's state.

---

### ICM-029 · Skill file

*Kind:* artifact type · *Status:* partial · *Defined in:*
[§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md) ·
*Universality:* all nine axes

**Definition.** A Layer 3 file holding *"domain knowledge bundled as skill files"* — the
paper's term for reference material that carries procedures rather than rules. It is named
once and never defined, never given an example, and never distinguished from an ordinary
reference file.

**Status note.** The method currently treats it as a synonym for a specialised Layer 3 file.
Concern C33 (plugin and skill registry) will need a real definition: what a skill contains,
how it is selected, how it is versioned. **Until then, do not build on this term** — a
skill file is a Layer 3 file, and that is the whole of its specified meaning.

---

## 7. Execution

### ICM-030 · Orchestrating agent

*Kind:* actor · *Status:* core · *Defined in:* [§4.1](../paper/04-working-implementations/4.1-model-and-environment.md), [§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md) · *Universality:* all nine axes

**Definition.** The single agent that executes a workspace's stages. It *"reads the right files at the right moment"* and needs no coordination layer, because *"stage sequencing is the folder numbering"* ([§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md)).

**Example.** One Claude Code session runs `01_inventory` then `02_refactor` then `03_tests` in `02_refactor/`. Changing the agent does not change the workspace.

**Anti-pattern.** AP-01, AP-05.

**Not** — a manager of other agents. Delegation exists ([ICM-036](#icm-036--context-delegation)) but the orchestrator is not required to use it.

---

### ICM-031 · Sub-agent

*Kind:* actor · *Status:* core · *Defined in:* [§4.1](../paper/04-working-implementations/4.1-model-and-environment.md), [§4.2](../paper/04-working-implementations/4.2-script-to-animation-pipeline.md) · *Universality:* all nine axes

**Definition.** A smaller or faster agent to which the orchestrator delegates work *within* a stage, given context assembled from the workspace's own files. The paper's worked case: Opus 4.6 as orchestrator delegating to Sonnet 4.6 for sub-agent tasks.

**Example.** `02_refactor/02_go` delegates per-package symbol sweeps to sub-agents; each receives the Go naming policy and the target package listing, and returns an edit list.

**Not** — a stage. A sub-agent has no folder, no contract and no output of its own. Its result is consumed inside the running stage.

---

### ICM-032 · Agent Teams

*Kind:* vendor capability · *Status:* core (as an external mechanism) · *Defined in:* [§4.1](../paper/04-working-implementations/4.1-model-and-environment.md) · *Universality:* **not** — vendor-specific, one instantiation

**Definition.** The named capability through which Opus 4.6 *"coordinates multiple agents working in parallel from a single orchestrator."*

**Status note.** Vendor-specific and named as such. Under the universality bar this is an **instantiation**, not part of the method: another model family provides equivalent orchestration under a different name, and §4.1's claim is that the *folder structure* supplies the delegation specification either way.

---

### ICM-033 · Workspace-builder

*Kind:* workspace · *Status:* core · *Defined in:* [§4.4](../paper/04-working-implementations/4.4-building-new-workspaces.md) · *Universality:* all nine axes

**Definition.** A five-stage ICM workspace whose output is a new ICM workspace. It walks **discovery** (domain, workflow), **stage mapping** (natural breakpoints), **scaffolding** (folder structure), **questionnaire design** (setup questions), and **validation** (does the pipeline run end to end).

**Why it matters.** *"The workspace-builder itself follows ICM conventions. The workspaces it produces are consistent because the builder enforces the same structural rules it was built with."* It is the method's own account of its conventions, encoded procedurally — which is why concern C22 (a real `icm init` with templates) is a formalisation rather than an invention.

**Anti-pattern.** AP-11.

**Example.** Answering a discovery questionnaire about a weekly reporting workflow yields a numbered workspace with contracts, not a blank folder.

---

### ICM-034 · Local script

*Kind:* mechanism · *Status:* qualified · *Defined in:* [§1](../paper/01-introduction.md), [Abstract](../paper/00-abstract.md) · *Universality:* qualified — see below; **no deviation entry, because the paper's criterion is broader than its examples rather than narrower than the term**

**Definition.** Code in the workspace that performs work requiring no model judgement. The paper describes the role as *"the mechanical work that does not need AI at all"* and gives examples: fetching data, moving files, formatting output, sending emails.

**Qualification.** The examples are all Python utilities, which would read as a language requirement. It is not. The **criterion** is determinism: if the output is fully determined by the input, it belongs to a script and not to a stage. Under that criterion, formatters, linters, compilers, code generators, schema validators, provisioners and test harnesses are all local scripts, in any language, including none.

**Example.** `scripts/render_diagrams.py`, `scripts/validate_openapi`, `scripts/terraform -plan`, or a shell one-liner — all local scripts when their output is a pure function of their input. So is a deterministic test suite run as a verification step.

**Not** — a stage with no model in it. A stage has a contract and a review boundary; a script usually has neither.

---

### ICM-035 · Model Context Protocol (MCP)

*Kind:* external protocol · *Status:* core (as an external protocol) · *Defined in:* [§2.2](../paper/02-background-and-related-work.md) · *Universality:* not — external, and complementary

**Definition.** The protocol that *"standardizes how models access external tools and data sources."* The paper is explicit that ICM addresses a different layer: *"An ICM stage might use MCP connections to access external services, while the stage's folder structure determines what context the agent receives when doing so."*

**Why it matters.** Because tool definitions consume context, the two compose: scoping tool definitions per stage is *"avoid[ing] this by scoping tool definitions to individual stages."* MCP supplies the tools; ICM decides which stage sees them.

**Not** — an alternative to ICM. The paper spends a paragraph separating them precisely because they are frequently confused.

### ICM-036 · Context delegation

*Kind:* mechanism · *Status:* core · *Defined in:* [§4.1](../paper/04-working-implementations/4.1-model-and-environment.md) · *Universality:* all nine axes

**Definition.** Deriving a sub-agent's prompt from the workspace's own files rather than composing it by hand. The paper reports this happening naturally and names it as a double duty: *"The folder hierarchy is both the human's control surface and the model's orchestration logic."*

**Example.** `02_refactor/02_go/CONTEXT.md` declares its inputs; the orchestrator reads it and assembles the sub-agent prompt from the declared files plus the sub-task. The same declaration serves the human reviewing the stage.

---

## 8. Method properties

### ICM-040 · One stage, one job

*Kind:* principle · *Status:* core · *Defined in:* [§3.1](../paper/03-interpretable-context-methodology/3.1-design-principles.md) · *Universality:* all nine axes

**Definition.** The first design principle: each stage *"handles a single step of the
workflow"*. A stage that fetches does not also filter; a stage that filters does not also
format. It traces to McIlroy's Unix principle and Parnas's information-hiding criterion, and
to the same structure that governs passes in a multi-pass compiler.

**Example.** A stage that renames a symbol and a stage that checks the rename compiles are two stages. Merging them produces a stage whose output cannot be reviewed before the expensive half runs.

**Anti-pattern.** AP-01.

---

### ICM-041 · Plain text as the interface

*Kind:* principle · *Status:* qualified · *Defined in:* [§3.1](../paper/03-interpretable-context-methodology/3.1-design-principles.md) · *Universality:* qualified for the artifact-medium axis — **D-02**

**Definition.** The second design principle: *"Stages communicate through markdown and JSON
files. No binary formats, no database connections, no proprietary serialization."* The
rationale is participation — *"Any tool that can read a text file can participate"* — and
inspection.

**Qualification.** The paper states this as *no binary formats* and then feeds PDFs into a
shipped workspace ([§4.3](../paper/04-working-implementations/4.3-course-deck-production.md)).
Both cannot be the rule as written. The principle that actually survives is
**no undeclared conversion**: whatever enters a stage, the transformation to text is an
explicit artifact that can be reviewed and diffed. Auditable beats ASCII.

**Anti-pattern.** AP-12.

**Not** — a ban on non-text inputs. See [ICM-016](#icm-016--the-factory) and concern C16.

### ICM-042 · Layered context loading

*Kind:* principle · *Status:* core · *Defined in:* [§3.1](../paper/03-interpretable-context-methodology/3.1-design-principles.md), [§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md) · *Universality:* all nine axes

**Definition.** The third design principle: *"Agents load only the context they need for the
current stage"*, grounded in the finding that less irrelevant context means better model
performance. Its two halves — layering the load, and splitting stable rules from per-run
material — together produce structurally separate context: reference is *"internalised as
constraints"*, working material is *"processed as input"*.

**Example.** In `03_tests`, the error-handling guide (Layer 3) is loaded to decide what
counts as an acceptable failure; the other stages' outputs (Layer 4) are not, because this
stage has no opinion on them.

**Anti-pattern.** AP-02, AP-03.

---

### ICM-043 · Configure the factory, not the product

*Kind:* principle · *Status:* core · *Defined in:* [§3.1](../paper/03-interpretable-context-methodology/3.1-design-principles.md) · *Universality:* all nine axes

**Definition.** The fifth design principle: set the workspace up once with *"the user's
preferences, brand, style, and structural decisions"*, then let every run produce a new
deliverable from that same configuration. It is the continuity-delivery argument applied to
content.

**Example.** Fixing *"keep generated helpers under 40 lines"* in a contract improves every
future run; fixing one generated file improves exactly one.

**Anti-pattern.** AP-11.

**Not** — immutability of outputs. The product is meant to change every run; the factory is
what does not.

---

### ICM-044 · Prevention rather than compression

*Kind:* distinction · *Status:* core · *Defined in:* [§3.1](../paper/03-interpretable-context-methodology/3.1-design-principles.md), [§2.2](../paper/02-background-and-related-work.md) · *Universality:* all nine axes

**Definition.** The line between not loading material and loading it then shrinking it. The
paper takes the prevention side: *"This is prevention rather than compression"*, and later
*"ICM's architecture avoids it by construction."* Compression research is acknowledged —
up to 20x reduction with minimal loss — and declined as the primary mechanism, on the
grounds that a model never receives the material at all.

**Example.** A 40,000-token reference library is not summarised into a 2,000-token digest.
`references/CONTEXT.md` routes to the two documents this stage actually needs.

**Anti-pattern.** AP-03.

**Not** — a claim that compression is useless. It is the fallback when material genuinely
must be included — concerns C8 and C15.

### ICM-045 · Monolithic prompt

*Kind:* contrast baseline · *Status:* core · *Defined in:* [§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md), [Figure 3](../figures/figure-3.svg) · *Universality:* all nine axes

**Definition.** The alternative ICM is measured against: a single prompt loading *"all stage
instructions, all reference files, and all prior outputs"*, reaching 30,000–50,000 tokens.

It is a **defined baseline**, not a straw man — it is what the practitioner gets by
concatenating the workspace. Defining it precisely matters because the efficiency claim is
a comparison, and a comparison needs both sides pinned down. Note the figure and prose
disagree on the figure — [D-10](DEVIATIONS.md#d-10) — which is exactly why C7 replaces the
quotation with a measurement.

**Example.** The same `02_refactor` workspace loaded as one prompt: all five contracts, all
of `_config/`, and every prior `output/` directory, for a stage that reads two files.

---

### ICM-046 · Irrelevant context

*Kind:* concept · *Status:* core · *Defined in:* [§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md), [§2.2](../paper/02-background-and-related-work.md) · *Universality:* all nine axes

**Definition.** Tokens present in the context window that do not inform the current task. The
paper decomposes it precisely: *"instructions the agent will not follow during this step,
reference material that applies to a different stage, and prior outputs already consumed by
earlier stages."*

**Why the decomposition matters.** The three sources have different cures. Instructions from
other stages are fixed by scoping. Reference material for other stages is fixed by
scoping. Prior outputs already consumed are fixed by scoping *or* by deletion — the last
is why [AP-08](#12-anti-pattern-catalogue) exists.

**Example.** `03_tests` loading `01_inventory/output/api-surface.json`, which it consumed
through `02_refactor` and no longer needs.

**Anti-pattern.** AP-08, AP-03.

---

### ICM-047 · Context window composition

*Kind:* concept · *Status:* core · *Defined in:* [§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md), [Figure 3](../figures/figure-3.svg) · *Universality:* all nine axes

**Definition.** The per-stage breakdown of delivered tokens by layer: Layers 0–2 ≈1,300–1,600;
Layer 3 ≈500–2,000; Layer 4 variable, *"rarely exceeds a few thousand tokens when the
previous stage has done its job of condensing and structuring"*. Total **2,000–8,000** per
stage.

The per-layer figures in [Figure 1](../figures/figure-1.svg) agree with these aggregates
(≈800 + ≈300 + 200–500 = 1,300–1,600). Verified — see
[§11](#11-verified-consistencies).

**Status note.** These are **asserted, representative** counts from one workspace, not
measurements. No estimator exists to produce them for another workspace. Concern C15
formalises this into something computable; concern C7 measures it.

**Anti-pattern.** AP-06, AP-01.

### ICM-048 · Lost in the middle

*Kind:* external finding · *Status:* core (as an external finding) · *Defined in:* [§2.2](../paper/02-background-and-related-work.md), [§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md) · *Universality:* not — external, model-dependent

**Definition.** Liu et al.'s finding that models *"perform significantly worse when relevant
information is buried in the middle of long contexts."* It is the load-bearing empirical
support for the whole method.

**Critical caveat for audit.** This is the paper's only quantitative warrant for its
efficiency claim, and it is **cited, not reproduced**. The paper itself concedes in §4.6 that
*"No controlled comparison has been conducted between ICM's staged context loading and a
monolithic prompting approach on the same tasks, so the claim that scoped context improves
output quality rests on the theoretical support from the 'lost in the middle' literature and
practitioner judgment rather than measured effect sizes."* Every downstream claim about
quality inherits that caveat. Concern C7.

---

### ICM-049 · Context engineering

*Kind:* external discipline · *Status:* core (as an external discipline) · *Defined in:* [§2.2](../paper/02-background-and-related-work.md) · *Universality:* not — broader than ICM

**Definition.** Karpathy's framing of *"filling the context window with the right
information: instructions, retrieved knowledge, memory, tool descriptions, and prior
outputs, all structured so the model can use them effectively"*, with Lance Martin's
taxonomy of **write, select, compress, isolate**.

ICM is a *particular instance*: **select** and **isolate** are architectural, while
**write** is per-stage and **compress** is a fallback ICM declines by default
([ICM-044](#icm-044--prevention-rather-than-compression)).

**Not** — prompt engineering. The paper is explicit that the older term *"understates the
work."*

---

### ICM-050 · Model-agnosticism

*Kind:* claim · *Status:* qualified · *Defined in:* [§4.1](../paper/04-working-implementations/4.1-model-and-environment.md), [§5.4](../paper/05-discussion.md) · *Universality:* qualified — **D-08**

**Definition.** The claim that *"the protocol specifies folder structure, file formats, and
naming conventions… It does not depend on any model-specific capability"*, so a workspace
built for one model can be run by another.

**Qualification.** The claim is about the *protocol*; the evidence is one model family. The
paper is honest — §4.6 records that all testing used a single family and §5.4 raises
generalisation as an open question — but several protocol rules are tuned to how one family
handles long prompts: the 2,000–8,000 token target, the Inputs-scoping convention, and the
one-instruction-per-stage assumption.

**Practical reading.** Model-agnosticism is a **protocol-level property that is currently
untested at the behaviour level**. Treat portability of the folder as established;
treat equivalence of output as an open question. Concern C13.

---

## 9. The compiler lineage

§6 draws the paper's structural analogy. The terms below are the ones the analogy depends
on; they are external to ICM but load-bearing inside it.

### ICM-051 · Compiler pass

*Kind:* analogy · *Status:* core · *Defined in:* [§6.1](../paper/06-future-directions.md) · *Universality:* not — analogy

**Definition.** One discrete transformation in a multi-pass compiler: lexer, parser, semantic
analysis, optimisation, code generation. Each *"reads the output of the previous pass,
transforms it according to its own rules, and writes an intermediate representation that the
next pass can consume."*

**ICM's use.** A stage is a pass. `01_research` is the lexer, `02_script` the parser,
`03_production` the code generator.

---

### ICM-052 · Intermediate representation

*Kind:* analogy · *Status:* core · *Defined in:* [§6.1](../paper/06-future-directions.md), [§3.3](../paper/03-interpretable-context-methodology/3.3-stage-contracts-and-handoffs.md) · *Universality:* not — analogy

**Definition.** The well-defined, inspectable artefact a pass produces for the next pass.
ICM's stage outputs are IRs: *"each one is a complete, readable artifact that captures the
work done so far and provides everything the next stage needs to continue."*

**Why the analogy earns its keep.** It explains why the artefacts must be *complete* rather
than merely useful: an IR that omits what the next pass needs forces either re-derivation
(monolithic thinking) or human recall.

**Example.** `02_refactor/02_go/output/renames.md` must contain every rename, not a
representative sample — otherwise `03_tests` cannot consume it without re-reading the source.

### ICM-053 · Dependency graph

*Kind:* analogy · *Status:* core · *Defined in:* [§2.1](../paper/02-background-and-related-work.md), [§6.1](../paper/06-future-directions.md) · *Universality:* not — analogy, but becomes literal in C23

**Definition.** Make's insight, in the paper's reading: workflows can be defined as
*"dependency graphs between files using declarative specifications"*, and *"you do not need a
separate orchestration layer when the filesystem tracks what has been produced and what
depends on it."*

**ICM's status.** Implicit, not declared. §6.1 observes that *"a stage's Inputs table
declares which files it reads"* — so the graph is already *derivable* — but no artifact
stores it, so nothing can query it, check it for cycles, or schedule from it. Concerns C23
(declare it) and C17 (execute from it).

**Example.** Deriving the graph of `02_refactor` from its five Inputs tables yields: `00_knowledge`
→ everything; `01_inventory` → `00_knowledge`; `02_refactor/02_go` → `01_inventory`, `02_refactor/01_python`; `03_tests` → all four. `02_refactor/03_go` and `02_refactor/03_sql` are then provably independent and safe to parallelise — which is not knowable from the numbering.

---

### ICM-054 · Incremental build

*Kind:* analogy · *Status:* core · *Defined in:* [§2.1](../paper/02-background-and-related-work.md), [§6.1](../paper/06-future-directions.md) · *Universality:* not — analogy

**Definition.** *"Recompiling only the parts of the program that changed, rather than
rebuilding from scratch."* The paper claims ICM *"supports this by default"*: re-run stage 2
without touching stage 1, or touch one Layer 3 file and re-run only the stages that load it.

**Status note.** The claim is correct as an affordance and unformalised as a mechanism.
Nothing tracks what a stage read; correctness depends on the Inputs declarations being
complete, which today nothing verifies. Concern C12.

**Anti-pattern.** AP-09.

---

### ICM-055 · Staleness

*Kind:* property · *Status:* core · *Defined in:* [§6.1](../paper/06-future-directions.md) · *Universality:* all nine axes

**Definition.** The condition in which a stage's output no longer reflects its declared
inputs. The paper's statement: *"a change to any of those files signals that the stage's
output may be stale."*

Note the hedge — *may be*. A declared input changing is evidence of staleness, not proof of
it, and a stage whose real inputs exceed its declared ones can go stale silently. That gap
is the whole of Issues #5 and #12.

**Example.** A practitioner corrects a line in `02_refactor/02_python/output/renames.md`.
`03_tests` is now stale — but only because that stage declares the file, not because anything
detected the edit.

**Anti-pattern.** AP-09.

### ICM-056 · Semantic debugging

*Kind:* future direction · *Status:* v2 addition (proposed by the paper) · *Defined in:* [§6.2](../paper/06-future-directions.md) · *Universality:* all nine axes

**Definition.** The paper's proposed successor to what ICM currently offers: *"ICM
currently provides observability but not traceability."* Given output that is wrong, a
practitioner must *"read the stage 3 contract, check which files it loaded, read those
files, and form a judgment"* — *"the equivalent of debugging a program by reading the
source code and thinking hard, without a debugger."*

The section is explicit that these ideas *"are not yet implemented."* They are recorded
here because they are the vocabulary any future tooling must use, not because the method
currently provides them.

---

### ICM-057 · Incremental recompilation

*Kind:* analogy · *Status:* core · *Defined in:* [§6.1](../paper/06-future-directions.md), [§4.2](../paper/04-working-implementations/4.2-script-to-animation-pipeline.md) · *Universality:* all nine axes

**Definition.** Re-running only the stages affected by a change, derived from
[ICM-053](#icm-053--dependency-graph) rather than from stage order. The paper's worked
cases: *"if the research output is fine but the script needs rework, the practitioner
re-runs stage 2 without touching stage 1"*; *"if a voice guide in the reference material
changes, only the stages that load that file need to run again."*

**Example.** Fixing the Go naming policy touches `00_knowledge`. Reverse-dependency
transitively gives `01_inventory`, both refactor stages, and `03_tests`; the SQL migration
stage is excluded unless it also declares that file.

---

### ICM-058 · Provenance

*Kind:* future direction · *Status:* v2 addition (proposed by the paper) · *Defined in:* [§6.2](../paper/06-future-directions.md) · *Universality:* all nine axes

**Definition.** A recorded link from a piece of output back to the instruction or reference
file that produced it. The paper's proposal: *"if each section of a stage's output carried an
identifier linking it to the source instruction or reference file that produced it, a
practitioner could trace backward from any part of the output to the context that generated
it"* — *"the equivalent of debug symbols or source maps."*

Proposed carriers are GUIDs, section tags or comment annotations — markers that *"reference
specific sections of the stage's `CONTEXT.md` or Layer 3 reference files"* without changing
the artefact's meaning.

**Not** — a version number. Provenance answers *where this came from*; versioning answers
*when this changed*.

### ICM-059 · Audit file / proto-debugger

*Kind:* artifact · *Status:* core (as an instance) · *Defined in:* [§6.2](../paper/06-future-directions.md) · *Universality:* all nine axes

**Definition.** The cross-stage consistency check used in the script-to-animation workspace.
The paper describes what it does: it *"forces the agent to trace back from the specification
to the original script, re-verifying timing for each phrase and flagging inconsistencies"* —
it checks stage *n* against stage *n-2* by re-reading both against defined criteria.

The paper's characterisation: *"This audit file is a proto-debugger."*

**Status note.** This is **one instance** of [ICM-058](#icm-058--provenance) and
[ICM-056](#icm-056--semantic-debugging), not a general facility, and the paper says so.
Its value to a reader is diagnostic rather than prescriptive: it shows what a `Verify`
section looks like when implemented, and what a generalised one would have to specify.

**Example.** Flagging frame-count discrepancies and pacing breaks at scene boundaries — the
error kinds the paper reports as *"remarkably consistent."*

---

### ICM-060 · Breakpoint in markdown

*Kind:* future direction · *Status:* v2 addition (proposed, speculative by the author's own
label) · *Defined in:* [§6.2](../paper/06-future-directions.md) · *Universality:* all nine axes

**Definition.** The paper's most speculative proposal, flagged as such: *"a breakpoint in a
`CONTEXT.md` file might say 'after the agent processes this instruction, show me what it
produced before continuing.'"* It would *"turn a single-pass stage into a sequence of
verifiable sub-steps."*

Recorded because a term must exist before tooling can be specified, **not** because the
method recommends it. No implementation exists and none is claimed.

---

## 10. Oversight and observation

### ICM-061 · Observability by inspection

*Kind:* property · *Status:* core · *Defined in:* [§5.3](../paper/05-discussion.md) · *Universality:* all nine axes · **D-01**

**Definition.** The paper's strongest and most quotable claim: *"Because every intermediate
output is a plain file, the system is observable by default. There is no logging layer to
build, no dashboard to configure."* Grounded in Rudin's argument for interpretable systems
over post-hoc explanation: *"ICM is a glass-box AI workflow… There is nothing to explain
because nothing was hidden."*

**Scope note.** This is a claim about **inspection** — reading current state. It is not a
claim about **accounting** — reconstructing what happened across a run that overwrote its
own output twice. v2 splits the two; the paper's argument is unaffected, and the part it
does not cover is named. Concern C25.

### ICM-062 · Glass-box workflow

*Kind:* property · *Status:* core · *Defined in:* [§5.3](../paper/05-discussion.md) · *Universality:* all nine axes

**Definition.** A workflow whose internal state is directly readable at every point, as
opposed to one that is inferable only from inputs and outputs. ICM's claim is that glass-box
status is **structural**, not engineered: *"It did not become transparent through the addition
of an explanation layer. It was never opaque in the first place."*

**Test for whether a workspace qualifies.** At any point, can a person with a text editor
name the next action, the input it will use, and the artifact it will produce? If any of the
three requires asking the model, the workspace is not glass-box.

**Example.** `02_refactor` — open the folder, read the contracts, the references, the
outputs. Nothing is hidden behind a query.

---

### ICM-063 · Mixed-initiative system

*Kind:* external concept · *Status:* core (as an external concept) · *Defined in:* [§3.1](../paper/03-interpretable-context-methodology/3.1-design-principles.md), [§2.3](../paper/02-background-and-related-work.md) · *Universality:* not — external

**Definition.** Horvitz's framing: systems should *"let users invoke, adjust, and terminate
automated processes at natural breakpoints"*, and such systems require that *"the system's
state be visible and its actions are reversible."*

ICM's compliance is the direct design goal of two principles — **every output is an edit
surface** and **plain text as the interface** — because those are what make adjustment and
termination possible without a control panel.

---

### ICM-064 · Intervention pattern

*Kind:* empirical claim · *Status:* core · *Defined in:* [§4.5](../paper/04-working-implementations/4.5-early-practitioner-experience.md), [Figure 5](../figures/figure-5.svg) · *Universality:* qualified — see [D-11](DEVIATIONS.md#d-11)

**Definition.** Where in a pipeline humans intervene. The paper's finding: of 33 community
members using multi-stage workspaces, **30** reported a **U-shape** — heavy editing at
stage 1 (direction-setting), light in the middle (constrained execution), heavy at the end
(alignment). Three reported roughly equal editing.

**Audit status.** Self-reported through conversation, not instrumented measurement, and the
paper says so twice (§4.5 and §4.6). [Figure 5](../figures/figure-5.svg) plots an ordinal
five-point scale with **no counts**, so it cannot represent the 30/3 split — see
[D-11](DEVIATIONS.md#d-11). Quote this finding as a practitioner report, never as data.

**Why the paper cares.** The two peaks are different work: stage 1 is creative judgement, the
final stage is *"closer to debugging"* — which is what motivates §6.2 in the first place.

### ICM-065 · Edit-source principle

*Kind:* principle · *Status:* v2 addition (proposed by the paper) · *Defined in:* [§6.3](../paper/06-future-directions.md) · *Universality:* all nine axes

**Definition.** The paper's argument that durable improvement comes from fixing the source
that generates an artefact, not the artefact. Its software-engineering framing: *"editing the
output is patching the binary. It works, but it does not improve the compiler."*

**Example.** A script that is consistently too formal is fixed by strengthening the voice
guide with an example of the target register — not by rewriting the script, which fixes one
run and leaves the next one wrong.

**The honest counterweight.** The paper does not claim the principle is absolute. Creative
output sometimes needs a human touch that no source-level rule captures: *"a script might
benefit from a turn of phrase that no amount of voice guide refinement would have produced.
Editing the output in that case is the right move."* The distinguishing test is whether the
edit **recurs**.

---

### ICM-066 · Diagnostic edit

*Kind:* concept · *Status:* v2 addition (proposed by the paper) · *Defined in:* [§6.3](../paper/06-future-directions.md) · *Universality:* all nine axes

**Definition.** An edit to a stage's output that **recurs in the same form across runs** and
therefore signals a fixable source-level defect. The paper's example: *"if the practitioner
consistently tightens the opening paragraph, that is a signal that the stage contract should
say 'keep the opening under three sentences.' … These recurring edits are debugging
information."*

The distinction from a one-off creative touch is **frequency**, and that is what makes it
detectable: *"If a practitioner edits the same kind of thing in the same stage's output three
runs in a row, the system could surface that pattern and suggest a source-level change."*

**Status note.** Requires tracking output edits across runs. Nothing in the method does
that today. Concerns C26 (diff/review) and C27 (regression metrics).

**Anti-pattern.** AP-07.

---

### ICM-067 · Compliance alignment

*Kind:* property · *Status:* qualified · *Defined in:* [§5.3](../paper/05-discussion.md), [§2.3](../paper/02-background-and-related-work.md) · *Universality:* qualified — see [D-01](DEVIATIONS.md#d-01)

**Definition.** The paper's observation that ICM structurally produces what the EU AI Act's
oversight requirements ask for — *"staged review, audit trails, and defined intervention
points"* — and does so *"as a byproduct of its architecture: there is no way to run an ICM
pipeline without generating inspectable intermediate artifacts."*

**Qualification, and it is a large one.** The paper is careful: *"Whether this constitutes
compliance with specific regulatory requirements is a legal question this paper does not
attempt to answer, but the structural alignment is worth noting."* Under the universality
bar this term must not be read as a compliance claim. Structural alignment with a principle
is not an audit trail, a retention policy, or an access-control record — the properties
regulated regimes actually require. Concerns C21 and the reserved terms
`ICM-098 audit_record`, `ICM-097 role`.

**Not** — certification. Nothing here has been assessed against any specific regime.

---

## 11. Verified consistencies

Checked while building this glossary, so an auditor does not re-derive them. These are places
where two parts of the paper **agree**, recorded because several look like they might not.

| # | Claim A | Claim B | Verdict |
| --- | --- | --- | --- |
| V-1 | [Figure 1](../figures/figure-1.svg) per-layer sizes: ≈800, ≈300, 200–500, 500–2k, varies | §3.2: *"Layers 0 through 2 together contribute roughly 1,300 to 1,600 tokens"* | **Agree.** 800+300+(200…500) = 1,300…1,600 |
| V-2 | §4.2 describes three stages: `01_research`, `02_script`, `03_production` | §6.1: *"Stage 1 (research) … Stage 2 (script) … Stage 3 (production)"* | **Agree.** Same workspace, same order |
| V-3 | §4.5: *"Section 6 explores what tooling for this kind of traceability might look like"* | §6.2 is *Toward Semantic Debugging* | **Agree** (contrast with [D-04](DEVIATIONS.md#d-04), which is wrong) |
| V-4 | §5.4 cites §4.1 for the model-agnostic protocol claim | §4.1 states exactly that claim | **Agree** |
| V-5 | §4.2 cites §4.1 for sub-agent delegation | §4.1 describes Opus→Sonnet delegation | **Agree** |
| V-6 | [Table 1](../paper/01-introduction.md) concedes four rows where ICM is weaker than frameworks | §5.2 names three non-goals in prose | **Agree, with a split.** Error recovery, branching and concurrency map to §5.2 directly; *external service integration* is answered in §2.2 (MCP + local scripts) rather than §5.2. Not a contradiction — a different home |
| V-7 | §3.1: *"Stages communicate through markdown and JSON files"* | No JSON file appears anywhere in the paper | **Not a consistency** — see [D-06](DEVIATIONS.md#d-06) |

---

## 12. Anti-pattern catalogue

Each entry names the terms it corrupts, the symptom a practitioner will actually observe, and
the check that catches it. Anti-patterns are part of the glossary proper: Additions #1 asks
for terms *"with examples and anti-patterns"*, and an example alone does not teach the
failure.

### AP-01 · The god stage

**Corrupts** [ICM-004](#icm-004--stage), [ICM-008](#icm-008--pipeline-icm-sense), [ICM-040](#icm-040--one-stage-one-job), [ICM-047](#icm-047--context-window-composition)
**Symptom.** One stage's output folder accumulates five unrelated files; nobody can say which
stage the reviewer's edit belonged to; context for one job includes four jobs' material.
**Check.** If no single sentence describes what the stage produces, split it.

### AP-02 · Reference/working blend

**Corrupts** [ICM-014](#icm-014--layer-3--reference-material), [ICM-015](#icm-015--layer-4--working-artifacts), [ICM-042](#icm-042--layered-context-loading)
**Symptom.** One file holds both rules and this run's content, so the model cannot tell which
to internalise and which to transform. Rules drift between runs because they are edited next
to output.
**Check.** Ask: would this file be identical on a different topic? If yes it is Layer 3. If
no, it is Layer 4. A file that is both is the defect.

### AP-03 · The empty or decorative Inputs table

**Corrupts** [ICM-022](#icm-022--inputs-table), [ICM-019](#icm-019--context-scoping), [ICM-044](#icm-044--prevention-rather-than-compression)
**Symptom.** `## Inputs` lists a whole directory, or nothing, or the paper's own vague
`../01_research/output/`. Context loads balloon; the model re-derives its own file
selection, which is exactly what the Inputs declaration exists to prevent.
**Check.** Every entry names a file or a deliberate glob, and every glob is justified by a
named downstream need.

### AP-04 · Unpinned outputs

**Corrupts** [ICM-024](#icm-024--outputs-section), [ICM-028](#icm-028--output-folder), [ICM-009](#icm-009--handoff)
**Symptom.** The stage writes somewhere other than `output/` — the workspace root, a sibling
stage's folder, a temp directory. The next stage's declared input no longer resolves and
nothing reports it.
**Check.** Every Outputs entry ends `-> output/`.

### AP-05 · The contract as documentation

**Corrupts** [ICM-020](#icm-020--stage-contract), [ICM-023](#icm-023--process-section), [ICM-030](#icm-030--orchestrating-agent)
**Symptom.** `CONTEXT.md` was written once during scaffolding, describes a stage that no
longer exists, and is never read by anyone. Editing happens in the artefact instead.
**Check.** Has anyone edited this file since the last run? If never, it is not a contract.

### AP-06 · Unnumbered or misordered stages

**Corrupts** [ICM-005](#icm-005--stage-folder), [ICM-006](#icm-006--stage-numbering), [ICM-047](#icm-047--context-window-composition)
**Symptom.** `draft/`, `final/`, `v2/` alongside numbered stages. Nothing encodes order, and
a human's "next" and the agent's "next" diverge.
**Check.** Sort the workspace listing; execution order must be readable from names alone.

### AP-07 · Silent stage rewrite

**Corrupts** [ICM-026](#icm-026--edit-surface), [ICM-066](#icm-066--diagnostic-edit), [ICM-055](#icm-055--staleness)
**Symptom.** A re-run discards a human's edit with no diff shown and no backup. The edit
surface is destroyed silently — the failure mode §2.3 identifies as causing both **misuse**
(trusting blind) and **disuse** (abandoning the system).
**Check.** Edit an output, re-run its stage, confirm the edit is either still there or
visibly replaced.

### AP-08 · Monolithic accumulation

**Corrupts** [ICM-009](#icm-009--handoff), [ICM-046](#icm-046--irrelevant-context), [ICM-057](#icm-057--incremental-recompilation)
**Symptom.** Stage 01's output from three runs ago still sits in `output/`, so downstream
stages read stale material and the workspace grows without bound.
**Check.** Delete a stage's output and re-run only the next stage. If it fails, the
dependency was never real.

### AP-09 · Silent skip

**Corrupts** [ICM-022](#icm-022--inputs-table), [ICM-055](#icm-055--staleness), [ICM-054](#icm-054--incremental-build)
**Symptom.** A declared input does not exist or is unreadable; the agent proceeds and
producribes plausible output from partial context. Nothing reports the gap.
**Check.** Before each stage, resolve every `required` input. This is concern C5, and it is
the single most load-bearing check in the whole method.

### AP-10 · Stale identity

**Corrupts** [ICM-011](#icm-011--layer-0--global-identity-file), [ICM-012](#icm-012--layer-1--workspace-routing-file), [ICM-003](#icm-003--workspace)
**Symptom.** Stages were added, renamed or deleted; Layer 0 and Layer 1 still describe the
old workspace. The agent routes to stages that no longer exist, or skips new ones, and its
belief about where it is is wrong — which is the one thing Layer 0 exists to prevent.
**Check.** Add a stage. Is it reachable from Layer 1 without editing Layer 1?

### AP-11 · Builder output treated as correct by construction

**Corrupts** [ICM-033](#icm-033--workspace-builder), [ICM-043](#icm-043--configure-the-factory-not-the-product)
**Symptom.** A workspace is generated and never tuned, so every stage loads the default
reference set regardless of whether it is relevant. The builder guarantees *consistency*;
it does not guarantee *fit*.
**Check.** Can you name which Layer 3 file each stage loads, and why? If not, the workspace
is scaffolding, not a factory.

### AP-12 · Undeclared conversion

**Corrupts** [ICM-041](#icm-041--plain-text-as-the-interface)
**Symptom.** A PDF, spreadsheet or image enters a stage and text emerges with no record of
how. Nobody can tell whether the extraction dropped a table, and no diff exists when the
source changes.
**Check.** For every non-text input, there is a projection artifact naming the tool, version
and options used. This is the corrected form of the plain-text principle — see
[D-02](DEVIATIONS.md#d-02).

---

## 13. Contrast terms

Ten words that carry a **different meaning inside ICM** than in ordinary use. Each is the
most likely source of a workspace built to the wrong shape, so each is recorded with both
senses side by side.

### ICM-070 · Agent

*Contrast term* · *Universality:* all nine axes — the agent is outside the workspace entirely

**Ordinary:** an LLM plus tools, looping until a task is done.
**ICM:** an execution actor, deliberately *not* part of the workspace. The workspace's
persistent state is files; the agent is replaceable
([§3.4](../paper/03-interpretable-context-methodology/3.4-portability-and-reproducibility.md)).
*Consequence:* never design a workspace around an agent's identity, memory or session.

### ICM-071 · Context

*Contrast term* · *Universality:* all nine axes

**Ordinary:** the text handed to a model.
**ICM:** a **structured, layered** thing — Layers 0–4, scoped per stage. The paper's whole
argument is that context is not a blob but an arrangement.
*Consequence:* "add it to the context" is not an action; "declare it in Inputs with a layer
and a purpose" is.

### ICM-072 · Prompt

*Contrast term* · *Universality:* all nine axes

**Ordinary:** the instruction you write to get an output.
**ICM:** a *derived artifact*. The contract is the source; the prompt is what the agent
builds from it on each run. Because it is derived, it is not edited and not stored.
*Consequence:* improving behaviour means editing the contract, never a saved prompt.

### ICM-073 · Pipeline (Unix sense)

*Contrast term* · *Universality:* all nine axes

**Ordinary:** processes connected by byte streams — small, composable, no shared state.
**ICM:** a chain of **numbered folders** where each stage reads and writes files in place.
Different failure model: a Unix pipeline fails loudly at a process; an ICM pipeline fails
silently by producing a plausible file.
*Consequence:* do not assume pipe-like atomicity.

### ICM-074 · Agent framework

*Contrast term* · *Universality:* all nine axes

**Ordinary:** CrewAI, LangChain, AutoGen — the thing ICM replaces.
**ICM:** explicitly *not* this, and the paper's [Table 1](../paper/01-introduction.md)
enumerates the trade in both directions.
*Consequence:* the value of ICM is the absence of the dependency, not a feature parity. A
workspace that starts importing a framework has stopped being ICM.

### ICM-075 · Dependency graph

*Contrast term* · *Universality:* all nine axes — becomes literal in C23

**Ordinary:** an explicit DAG structure computed by a tool.
**ICM:** currently an **implicit** structure derivable from Inputs declarations
([§6.1](../paper/06-future-directions.md)). It exists, nothing stores it, and nothing checks
it for cycles. This is concern C23.
*Consequence:* never infer dependency from stage numbering — see [D-09](DEVIATIONS.md#d-09).

### ICM-076 · Workflow engine / state machine

*Contrast term* · *Universality:* all nine axes

**Ordinary:** Airflow, Temporal, a BPMN engine — the thing that would add branching,
retries and concurrency.
**ICM:** explicitly out of scope. §5.2 concedes that automated branching *"would require
scripting that moves ICM toward being a framework itself."*
*Consequence:* any future orchestration must stay an **optional outer layer** that preserves
folder-as-state, or the method's central claim is lost.

### ICM-077 · Cache

*Contrast term* · *Universality:* all nine axes — and the one most often got wrong

**Ordinary:** a stored result of an expensive computation, reused on repeat.
**ICM:** not a Layer 3 concept at all. Layer 3 is human-maintained and never written by a
run; a cache is machine-written and machine-invalidated. **Conflating them is the most
expensive single error in this list** — see AP-11's neighbourhood and concern C4.
*Consequence:* provider prompt caching and Layer 3 are different mechanisms with different
invalidation rules and different failure modes.

### ICM-078 · Memory

*Contrast term* · *Universality:* all nine axes

**Ordinary:** agent state persisting across turns or sessions.
**ICM:** not present. Persistence is **the files**; that is the entire mechanism.
*Consequence:* "the agent should remember" is always really "the contract should declare it."

### ICM-079 · Retrieval (RAG / code intelligence)

*Contrast term* · *Universality:* all nine axes

**Ordinary:** fetching relevant passages from a corpus by embedding similarity.
**ICM:** not present and not required. *"Agents only see explicitly placed files"* is the
method's position, and it is a deliberate trade — prevention over recall
([ICM-044](#icm-044--prevention-rather-than-compression)). Retrieval is an open extension
(C20), and introducing it changes the architecture rather than extending the contract.
*Consequence:* introducing retrieval answers a different question from the one ICM asks —
*what should this stage see?* rather than *what might be relevant?* Both cannot be the
contract.

---

## 14. Reserved terms

Slots claimed by open work items and **not yet defined**. Listing them prevents a term being
invented in `04-pipeline-engineering.md` and contradicted in `07-enterprise-operations.md`.
A reserved term is not usable until its owner's entry lands.

| Slot | Term | Owner | Why it is needed |
| --- | --- | --- | --- |
| `ICM-090` | `run_id` | C10 | Distinguish concurrent and successive runs of one stage |
| `ICM-091` | `lock` | C10 | Prevent two runs writing one artefact |
| `ICM-092` | `checkpoint` | C11 | Resume point after a failed stage |
| `ICM-093` | `retry_policy` | C11 | Bound and space retries |
| `ICM-094` | `deps` | C23 | Declared dependency replacing inferred numbering — [D-09](DEVIATIONS.md#d-09) |
| `ICM-095` | `manifest` | C3 | The promised JSON file — [D-06](DEVIATIONS.md#d-06) |
| `ICM-096` | `contract_version` | C3, C9 | Which contract produced an artefact |
| `ICM-097` | `role` | C21 | Least-privilege subject — [D-01](DEVIATIONS.md#d-01) |
| `ICM-098` | `audit_record` | C21 | Immutable who/what/when — [D-01](DEVIATIONS.md#d-01) |
| `ICM-099` | `secret_ref` | C21 | Indirection from workspace to credential store |
| `ICM-100` | `artifact_type` | C16 | Typed non-text artefacts — [D-02](DEVIATIONS.md#d-02) |
| `ICM-101` | `projection` | C16 | Declared binary→text extraction — AP-12 |
| `ICM-102` | `token_estimate` | C15 | Make [ICM-047](#icm-047--context-window-composition) computable |
| `ICM-103` | `budget` | C15 | Hard stop before cost spirals |
| `ICM-104` | `gate` | C6 | Make [ICM-027](#icm-027--review-gate) declarable |
| `ICM-105` | `routing_rule` | C24 | Which model, with what fallback — [D-08](DEVIATIONS.md#d-08) |
| `ICM-106` | `template` | C22 | `icm init` scaffolding for research, review, content, data |
| `ICM-107` | `dependency_edge` | C12, C23 | Declared vs observed reads |
| `ICM-108` | `skill_manifest` | C33 | Give [ICM-029](#icm-029--skill-file) a real definition |
| `ICM-109` | `rubric` | C27 | Scoring stage outputs against a fixed standard |
| `ICM-110` | `condition` | C29 | Branching and loops in a workflow definition |

---

## 15. Change log for this document

Full history in [CHANGELOG.md](CHANGELOG.md). Recorded here only for term-level changes.

| Version | Date | Change | Reason |
| --- | --- | --- | --- |
| 2.1.0 | 2026-10-04 | Initial glossary. 74 term records, 12 anti-patterns, 10 contrast terms, 21 reserved slots, 7 verified consistencies | C1 — Issues #1, Gaps #1, Additions #1 |

### Identifier space

| Range | Use |
| --- | --- |
| `ICM-001`–`ICM-036` | Foundations, context hierarchy, contracts, execution |
| `ICM-037`–`ICM-039` | Unallocated, held for execution concerns (C10, C11) |
| `ICM-040`–`ICM-067` | Method properties, compiler lineage, oversight |
| `ICM-068`, `ICM-069` | Unallocated, held for the oversight and record layer (C21) |
| `ICM-070`–`ICM-079` | Contrast terms |
| `ICM-090`–`ICM-110` | Reserved, not yet defined |
| `AP-01`–`AP-12` | Anti-patterns |
| `D-01`–`D-12` | Deviations, in [DEVIATIONS.md](DEVIATIONS.md) |
| `C1`–`C36` | Worklist concerns, in [WORKLIST.md](WORKLIST.md) |

### Open questions this glossary deliberately does not answer

Recorded so they are not mistaken for oversights.

1. **Is `CONTEXT.md` the right name?** One reserved filename serving three roles
   ([ICM-021](#icm-021--contextmd)) is defensible, but [D-07](DEVIATIONS.md#d-07) is a
   vendor dependency inside a model-agnostic claim. Concern C3.
2. **Is a gate universal or declared?** The paper implies every boundary has one
   ([ICM-027](#icm-027--review-gate)); making it declarable is C6. Until that lands, do not
   assume a workspace has gates it never declared.
3. **What is the unit of a stage's atomicity?** The paper has no answer, and
   [AP-07](#12-anti-pattern-catalogue) cannot be fixed without one. Concern C10.
4. **Does a stage have one output or many?** The paper's notation implies one; real stages
   emit several. Affects `ICM-024`. Concern C3.
5. **What does "this run" mean when two runs overlap?** Undefined, and load-bearing for
   Layer 4 ([ICM-015](#icm-015--layer-4--working-artifacts)). Concern C10, reserved as
   `ICM-090`.