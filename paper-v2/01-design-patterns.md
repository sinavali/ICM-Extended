# 01 — Design patterns and the design review checklist

**Status:** v2.2.0 · concern C2 · Issues #2
**Depends on:** [00-glossary.md](00-glossary.md) — every term below is cited by its
`ICM-###` ID and means exactly what the glossary says it means.
**Governed by:** the universality bar in [README.md §2](README.md#2-the-universality-bar).
**Rule:** this file may not introduce a term that is not in the glossary, and may not
contradict a deviation in [DEVIATIONS.md](DEVIATIONS.md). Where it qualifies something the
paper states, it says so in the record and cites the `D-##`.

---

## 1. Why this file exists

### 1.1 What the paper leaves missing

The paper states five principles
([§3.1](../paper/03-interpretable-context-methodology/3.1-design-principles.md)) and one
architecture
([§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md)). Principles
say what to believe. They are not enough to build from, because two practitioners who
both accept all five principles can produce workspaces that behave nothing alike.

The gap is not in the thesis. It is that the paper gives no vocabulary for *recurring
structural choices*. Three decisions appear throughout the paper and the shipped
workspaces, are load-bearing, and are never named:

- that a stage is a **contract** before it is a prompt;
- that a stage's context is a **declaration** rather than a consequence of where files
  happen to sit;
- that the boundary between one stage and the next is a **directory**, not an event.

Because these are unnamed, they cannot be chosen deliberately, cannot be taught, and
cannot be reviewed. A practitioner who builds a workspace correctly has no vocabulary for
*why* it works, so the knowledge does not transfer; a practitioner who builds one
incorrectly has no checklist that would have caught it.

There is a second gap, and it is the one that costs more. The paper argues for
interpretability and against opacity, then offers **no way to examine a workspace that
has not yet run**. Every observability property it claims
([§5.3](../paper/05-discussion.md), [§6.2](../paper/06-future-directions.md)) is a claim
about a run. A design review is about a *folder*, before any model is called. Without
that, the only feedback available is a failed run, and a failed run has usually already
cost money and produced an artifact somebody trusts.

### 1.2 What a pattern is here, and what it is not

A **pattern** in this tree is a named, recurring structural solution to a recurring
problem, described in terms of ICM primitives only — stages, contracts, layers, folders,
files. It carries:

| Field | Purpose |
| --- | --- |
| **Problem** | the recurring situation, stated so a reader can recognise their own |
| **Forces** | the two or more things in tension that any solution trades between |
| **Structure** | the shape, using ICM primitives and no others |
| **Example** | a worked instance in `02_refactor` — a polyglot API rename — not in content production |
| **Consequences** | what you gain and what you give up, both stated |
| **Do not use when** | the applicability limit, stated as a condition |
| **Corrupted by** | the anti-patterns from [glossary §12](00-glossary.md#12-anti-pattern-catalogue) that produce a broken version of it |
| **Evidence** | the paper section, or the concern ID if v2 introduces it |
| **Universality** | which axes of [README §2](README.md#2-the-universality-bar) the pattern survives unchanged, and the written qualification for any it does not |

Three things a pattern is **not** here:

- **Not a template.** A pattern is a decision, not a directory to copy.
  [ICM-106 template](00-glossary.md#14-reserved-terms) is reserved for `icm init`
  scaffolding under concern C22, and a template that is wrong for your workflow will be
  copied anyway. Patterns are for recognising; templates are for starting.
- **Not a rule.** Rules are checkable and have a pass condition; §4 is the rules. A
  pattern with no check in §4 that enforces it is a preference, and is marked as one.
- **Not a layer or a stage.** Patterns compose; they do not nest in the folder tree.
  If a pattern wants a new folder, it says which layer it belongs to and why.

### 1.3 The relationship to the checklist

Every pattern in §2 is enforced by at least one item in §4, and every checklist item in
§4 traces to at least one pattern or to one of the paper's five principles. That
two-way property is what makes this file reviewable rather than decorative, and §6.1 is
the table that proves it. A pattern with no enforcing check is a description; a check with
no pattern is a one-off rule.

---

## 2. The pattern catalogue

Twelve patterns. Nine are extracted from the paper's own architecture; three are
formalised from mechanisms the paper *proposes* but does not build, and are marked as
such so that no reader mistakes a proposal for a shipped practice.

| # | Pattern | Origin |
| --- | --- | --- |
| [DP-01](#dp-01--numbered-stages-as-the-ordering-primitive) | Numbered stages as the ordering primitive | paper, §3.2 |
| [DP-02](#dp-02--contract-before-content) | Contract before content | paper, §3.3 |
| [DP-03](#dp-03--declared-context-scoping) | Declared context scoping | paper, §3.2 |
| [DP-04](#dp-04--folder-as-handoff) | Folder as handoff | paper, §3.2, §3.3 |
| [DP-05](#dp-05--the-edit-surface) | The edit surface | paper, §3.1 principle 4 |
| [DP-06](#dp-06--factory-and-product-separation) | Factory and product separation | paper, §3.1 principle 5 |
| [DP-07](#dp-07--recursive-routing) | Recursive routing | paper, §3.2 footnote 4 |
| [DP-08](#dp-08--conditional-layer-depth) | Conditional layer depth | paper, §3.2 |
| [DP-09](#dp-09--the-deterministic-escape-hatch) | The deterministic escape hatch | paper, §1, §4.2 |
| [DP-10](#dp-10--sub-workspace-delegation) | Sub-workspace delegation | paper, §4.1, §4.2 |
| [DP-11](#dp-11--verification-as-a-stage) | Verification as a stage | **proposed**, §6.2 |
| [DP-12](#dp-12--workspace-as-artifact) | Workspace as artifact | paper, §3.4 |

---

### DP-01 · Numbered stages as the ordering primitive

*Kind:* structural · *Evidence:*
[§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md) ·
*Universality:* survives all nine axes; **qualified on workflow shape and host** — see
the qualification below and [D-09](DEVIATIONS.md#d-09)

**Problem.** A workflow with several steps has no natural order in a folder. Files in a
directory are unordered; the person running the work and the agent doing the work
disagree about what "next" means.

**Forces.** Legibility of order against expressiveness of order. A total order is easy to
read and cheap to store. It cannot express "stage 05 does not need stage 04's output, so
it need not wait for it."

**Structure.** Every stage is a directory named `NN_name`, zero-padded to a fixed width.
Order is read off the name with a plain directory sort. No index file, no manifest, no
ordering field: the filesystem's own sort is the scheduler. Stage-local material lives
inside the stage directory; shared material lives outside the stage range.

**Example.** `02_refactor/` holds `00_knowledge/`, `01_inventory/`, `02_refactor/`,
`03_tests/`, `04_release/`. `ls 02_refactor/` yields the execution order to anyone with a
shell, including someone who has never seen the project.

**Consequences.** Gains: order is inspectable with no tooling; a new stage is added by
creating a directory; an existing stage is removed by deleting one. Gives up: concurrent
execution (§5.2 scopes it out), and any dependency that is not left-to-right. Renumbering
to reorder a pipeline changes paths, which changes every reference to them.

**Do not use when.** the workflow has a genuine cycle, a fan-in, or two stages that must
interleave. Numbering cannot express those, and pretending otherwise produces a workspace
whose stated order is a fiction. When that is the case, the declared dependency relation
is authoritative and numbering becomes a display order only — which is exactly what
[D-09](DEVIATIONS.md#d-09) requires, since the paper's *"Stage sequencing is the folder
numbering"* is a total order presented as a dependency relation.

**Qualification — host axis.** Ordering by name assumes the host's directory listing
sorts the way you expect: byte order for the zero-padded prefix. Two real cases break
this: case-insensitive filesystems, where `0A_` and `0a_` collide or sort unpredictably;
and network filesystems with no stable ordering guarantee at all. Neither changes the
pattern, but both require the reviewer to confirm the sort actually produces the intended
order rather than assuming it.

**Corrupted by** [AP-06](00-glossary.md#12-anti-pattern-catalogue) — unnumbered or
misordered stages.

---

### DP-02 · Contract before content

*Kind:* structural · *Evidence:*
[§3.3](../paper/03-interpretable-context-methodology/3.3-stage-contracts-and-handoffs.md) ·
*Universality:* survives all nine axes; qualified on artifact medium only in that a
contract is text and its **outputs need not be**

**Problem.** In a single-prompt workflow, behaviour lives in an instruction string that is
written once, run once, and then is gone. Nothing states what the step was supposed to do,
so it cannot be reviewed, reused, or corrected.

**Forces.** Autonomy per run against inspectability across runs. A prompt you never store
is easy to vary; a contract you must store is a document somebody has to keep correct.

**Structure.** Each stage owns a contract declaring three things: **what it reads**
([Inputs](00-glossary.md#icm-022--inputs-table)), **what it does**
([Process](00-glossary.md#icm-023--process-section)), **what it writes**
([Outputs](00-glossary.md#icm-024--outputs-section)). The contract is the source. The
prompt handed to the model on any given run is *derived* from it and is never edited or
stored — see [ICM-072](00-glossary.md#icm-072--prompt). The contract is prose a human
reads, which is the literate-programming property the paper claims.

**Example.** `02_refactor/02_refactor/CONTEXT.md` declares that it reads the API surface
inventory from `01_inventory/output/` and the naming policy from
`00_knowledge/naming.md`, and that it writes `refactor-plan.md` and `refactor.diff` to
`output/`. Nobody edits a prompt, because there is no prompt to edit.

**Consequences.** Gains: the workspace is self-documenting — reading the contracts top to
bottom explains the pipeline without running it; behaviour changes are reviewable diffs;
a stage can be tested by replacing its inputs. Gives up: per-run improvisation, and a
contract that has not been updated becomes worse than no contract, because it is
authoritative and wrong.

**Do not use when.** the step genuinely cannot be specified before it runs — exploration
whose shape is discovered during the work. That is a real case and it is not a reason to
abandon contracts: write the contract for the *discovery* and let its Outputs be the
finding. A stage whose contract can only be written afterwards has told you it is really
two stages.

**Corrupted by** [AP-05](00-glossary.md#12-anti-pattern-catalogue) — the contract as
documentation.

---

### DP-03 · Declared context scoping

*Kind:* structural · *Evidence:*
[§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md) —
*"Layer 2 is the control point of the entire system"* ·
*Universality:* survives all nine axes; **requires an agent that honours the
declaration** — see the qualification

**Problem.** An agent asked to work in a folder will read the folder. It will read what is
relevant, what is irrelevant, and what is stale, in proportions it decides at runtime. On
a large workspace that is slow and expensive; on a workspace with one poisoned file it is
also wrong.

**Forces.** Completeness against relevance. Loading everything is safe and expensive.
Loading a selection is cheap and can omit something needed. The declaration is what lets a
workspace have both.

**Structure.** Layer 2 — the contract — names each input explicitly, with the layer it is
in and the purpose it serves. Inputs are files or **deliberate globs**, never a whole
directory and never "whatever is relevant". A glob is allowed only with a stated
downstream need, because an unstated glob is the mechanism by which a workspace grows a
monolithic prompt without anybody deciding to. This is the paper's prevention rather than
compression ([ICM-044](00-glossary.md#icm-044--prevention-rather-than-compression)) and
the mechanism behind its central token claim.

**Example.** `01_inventory` declares the API inventory file by name. It does **not**
declare `00_knowledge/*.md`, even though four files live there, because three of them are
about naming policy and only one is about the API surface.

**Consequences.** Gains: the token budget is a consequence of the declaration rather than a
hope; the declaration is auditable against what the stage actually did; adding a file to
the workspace does not silently change what a stage sees. Gives up: maintenance. A new
reference file is inert until it is declared, and a declared file that is deleted becomes
a silent failure — which is why
[AP-09](00-glossary.md#12-anti-pattern-catalogue), silent skip, is the most load-bearing
defect in the catalogue and why the existence check is blocking in §4.

**Do not use when.** never, as a matter of design. The one case where a declaration cannot
be written is when you do not yet know what the stage needs — and the answer there is a
discovery stage whose output is the Inputs list for the next one.

**Qualification — execution-model axis.** A declaration is only a *constraint* if
something enforces it. Where the agent can be instructed to read a named set of files,
this pattern holds. Where the host gives the agent unrestricted access to a tree and no
means to restrict it, the Inputs section is **advisory**: it documents intent and shapes
the model, but it does not bound what is loaded. Under that condition the token claim in
§3.2 is not established by the declaration alone.

No check in this file can settle that, because enforcement is a property of the host and
not of the folder. What the file can do is make the workspace *say* which case it is in —
[DRC-32](#drc-32--the-hosts-input-enforcement-is-named) is blocking for that reason — and
keep the runtime question visible as
[DRC-47](#drc-47--declared-inputs-are-actually-enforced) rather than letting it pass
unasked.

**Corrupted by** [AP-03](00-glossary.md#12-anti-pattern-catalogue) — the empty or
decorative Inputs table.

---

### DP-04 · Folder as handoff

*Kind:* structural · *Evidence:*
[§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md),
[§3.3](../paper/03-interpretable-context-methodology/3.3-stage-contracts-and-handoffs.md) ·
*Universality:* survives all nine axes; **qualified on host** — object stores and
eventually-consistent filesystems do not provide this

**Problem.** Two processing steps need to exchange a result. The general solution is a
channel: a socket, a queue, a message bus, a function call. Each one introduces a
running component, a protocol, and a failure mode that has nothing to do with the work.

**Forces.** Decoupling against liveness. A folder needs nothing running, so it works
whether or not anything is alive. A message queue gives you delivery guarantees a
directory does not.

**Structure.** Every stage writes its declared outputs into its own `output/`
([ICM-028](00-glossary.md#icm-028--output-folder)) and reads its inputs from other
stages' `output/` directories. Coordination is one directory's content being another
directory's declared input. There is no caller, no listener, no registration. The consumer
picks up whatever is there — including a version a human edited.

**Example.** `01_inventory/output/api-surface.json` is the only thing
`02_refactor` reads from it. Delete that file and `02_refactor` has no inventory; there
is nothing else that could have supplied one.

**Consequences.** Gains: inspection is free — the intermediate result is a file with a
path; a human edit between runs is the normal case, not a race; stages can be replaced,
re-run, or skipped without the others knowing. Gives up: atomicity. A partially written
file is indistinguishable from a complete one unless something checks — the failure mode
that makes an ICM pipeline *fail plausibly* rather than fail loudly, recorded in
[ICM-073](00-glossary.md#icm-073--pipeline-unix-sense). Atomic writes, run identity and
locking are concern C10; until they land, this pattern's gives-up column is real.

**Do not use when.** the consumer must start before the producer finishes, or the
intermediate result must never be observable in a partial state. Both are legitimate
needs and both are *not* solved by this pattern — they are solved by a runtime, which is
the trade the paper makes deliberately in §5.2. If you need them, the honest answer is an
outer orchestration layer that preserves folder-as-state, per
[ICM-076](00-glossary.md#icm-076--workflow-engine--state-machine).

**Qualification — host axis.** "Write it into the folder and read it back" assumes a
filesystem with coherent reads. It holds on a local disk, a network filesystem with
standard consistency, and a container volume. It does not hold unchanged on an object
store where a list may be stale, nor on a filesystem that silently truncates on name
length. Neither case is exotic; both require the reviewer to confirm the medium rather
than assume it.

**Corrupted by** [AP-04](00-glossary.md#12-anti-pattern-catalogue) — unpinned outputs.

---

### DP-05 · The edit surface

*Kind:* interaction · *Evidence:*
[§3.1](../paper/03-interpretable-context-methodology/3.1-design-principles.md) principle
four, [§2.3](../paper/02-background-and-related-work.md) ·
*Universality:* survives all nine axes; **qualified on artifact medium** — see
[D-02](DEVIATIONS.md#d-02)

**Problem.** An automated pipeline that produces a final answer gives the human no place
to intervene until the answer exists, by which point correcting it means starting over.
The literature this paper cites is unambiguous that opaque automation produces exactly two
outcomes: blind trust or abandonment.

**Forces.** Automation against correction. Running more steps unattended is faster and
removes the human from the loop; it also removes the only mechanism by which the human
can be right.

**Structure.** Every output is a file the human can open, read, edit and save
([ICM-026](00-glossary.md#icm-026--edit-surface)), and the next stage reads whatever is
there rather than a stored original. The stage boundary is the intervention point, and it
is a *location*, not an event — which is why intervention survives a failed run, a
forgotten flag, and a different operator.

**Example.** Before running `03_tests`, a practitioner edits
`02_refactor/output/refactor-plan.md` to drop one call site from the migration.
`03_tests` reads the edited plan. Nothing had to be enabled; the surface was already
there.

**Consequences.** Gains: correction costs one edit rather than one re-run; the human's
best judgement enters the pipeline at full fidelity; a workspace improves without any
code change. Gives up: provenance. After the edit, the file is no longer what the stage
produced, and nothing records which it was — [D-01](DEVIATIONS.md#d-01). Until C21
lands, an edited output and a generated one look identical, and
[§6.3](../paper/06-future-directions.md)'s edit-source principle has no way to notice a
recurring edit pattern.

**Do not use when.** the output is not something a human should be able to change
silently — anything whose integrity downstream depends on its exact origin, such as a
signed artifact, a checksummed lockfile, or a recorded measurement. Those are not
[ICM-026](00-glossary.md#icm-026--edit-surface) material, and pretending otherwise trades a
correctness property for a convenience. The fix is a typed artifact whose type declares
it non-editable, which is concern C16.

**Corrupted by** [AP-07](00-glossary.md#12-anti-pattern-catalogue) — silent stage rewrite,
the failure mode where the surface is destroyed without a diff.

---

### DP-06 · Factory and product separation

*Kind:* structural · *Evidence:*
[§3.1](../paper/03-interpretable-context-methodology/3.1-design-principles.md) principle
five, [§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md) Table 2 ·
*Universality:* survives all nine axes; **qualified on execution model and team** — see
below, and on [ICM-077](00-glossary.md#icm-077--cache)

**Problem.** Two kinds of material sit in the same workspace: what is true regardless of
the run, and what this run produced. The model needs to treat them differently — the
first as constraints it obeys, the second as input it transforms — and a folder that
holds both invites it to treat both as input.

**Forces.** Reuse against specificity. Rules that mention this run are always more
accurate and never reusable; rules that mention no run are reusable and go stale
quietly.

**Structure.** Layer 3 holds reference material: stable, human-maintained, configured
once at workspace setup, and *never written by a run*. Layer 4 holds working artifacts:
per-run, machine-written, replaced each time. The distinction is not a naming convention
— it is **who writes it and when**. The paper's own test is worth restating because it is
the cheapest one available: *would this file be identical on a different topic?* If yes it
is Layer 3; if no it is Layer 4.

**Example.** `00_knowledge/naming.md` states the camelCase policy — Layer 3, written once,
identical for every rename. `01_inventory/output/api-surface.json` is this run's
findings — Layer 4, written by a stage, gone next run. A file containing both is the
defect, not a grey area.

**Consequences.** Gains: one set of rules serves every run, which is what makes the
workspace a repeatable factory rather than a lucky prompt; rules are reviewable as policy
rather than as output; changing a rule is a visible, versionable edit. Gives up: a Layer 3
file that is wrong is wrong for *every* future run until someone fixes it, and nothing
detects that. Layer 3 also becomes the place people dump things, because it is where
material is safely persistent.

**Do not use when.** never. The separation is what distinguishes a workspace from a pile
of prompts. What varies is where Layer 3 lives — `references/`, `_config/`, `shared/` are
all correct in the paper's Table 2, and the name is free.

**Qualification — execution-model and team axes.** Layer 3 being *human-maintained* is a
statement about who writes it. Under concurrent execution, a machine-written file that
landed in Layer 3 has changed the factory, and a cache promoted into Layer 3 does the same
— the most expensive single error in the contrast-term list
([ICM-077](00-glossary.md#icm-077--cache)). Under multi-user operation, "human-maintained"
means concurrently human-maintained, and Layer 3 gains the same conflict problems as any
shared file. Both are concern C10 and C35. Until they land, the rule is: **nothing a run
writes is Layer 3**, enforced by review ([DRC-18](#drc-18--no-layer-3-file-is-written-by-a-run)).

**Corrupted by** [AP-02](00-glossary.md#12-anti-pattern-catalogue) — the reference/working
blend.

---

### DP-07 · Recursive routing

*Kind:* structural · *Evidence:*
[§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md) footnote 4 ·
*Universality:* survives all nine axes; qualified on **scale** — below a threshold it is
pure overhead

**Problem.** A large Layer 3 collection cannot be loaded whole, and a flat list of its
contents costs more tokens to scan than the routing file that would point into it.

**Forces.** Discoverability against cost. Every file listed is every file loaded;
every file omitted is a file nobody can find.

**Structure.** The Layer 1 routing pattern applied *inside* Layer 3: a collection large
enough to need navigation gets its own small index file naming its members and what each
is for. The index is itself Layer 3 and is loaded first; only the members it points to are
loaded after. The paper notes this as an aside; in a workspace with a large reference set
it is load-bearing.

**Example.** `00_knowledge/CONTEXT.md` is a nine-line index: "this folder holds the API
migration reference set; `naming.md` is the naming policy, `inventory-schema.md` is the
shape of the inventory, the rest are per-service notes — read only when the stage names
that service."

**Consequences.** Gains: a large reference set stays affordable; the collection becomes
self-describing. Gives up: an index that is wrong routes wrongly, and — unlike a bad
Layer 3 file, which is merely bad content — a bad index is *structurally* bad, because it
makes the right files unreachable.

**Do not use when.** the collection is small enough to declare file by file in the stage
contract. An index over three files is a fifth thing to keep correct.

**Corrupted by** [AP-10](00-glossary.md#12-anti-pattern-catalogue) — stale identity, which
at this layer is an index that has not been updated.

---

### DP-08 · Conditional layer depth

*Kind:* structural · *Evidence:*
[§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md) — *"No agent
reads everything"* · *Universality:* survives all nine axes; **conditional on DP-03
actually holding**

**Problem.** Layered loading only pays if the layers are actually loaded selectively. If
every stage reads every layer, the hierarchy is documentation and the token claim is
false.

**Forces.** Uniformity against specialisation. A workspace where every stage loads
everything is simpler to reason about and pays the monolithic price.

**Structure.** A stage reads a prefix of the hierarchy and stops. Layers 0–2 are always
read — they are the structural context, ~1,300–1,600 tokens per the paper's own
arithmetic. Whether Layer 3 is read, and which files within it, is declared. Layer 4 is
read to the depth the stage needs: a stage that transforms a document reads it; a stage
that validates a manifest reads the manifest only. The depth is a property of the
contract, not of the workspace.

**Example.** `02_refactor` reads Layers 0–2, two Layer 3 policy files, and the Layer 4
inventory. `04_release` reads Layers 0–2 and its Layer 4 input. Neither reads the other's
material, and no stage reads the whole tree.

**Consequences.** Gains: the 2,000–8,000 token figure per stage is a structural
consequence rather than a hope, and the monolithic 30,000–50,000 comparison is the
counterfactual this pattern excludes. Gives up: there is no single place that describes the
whole run, so a question spanning all stages cannot be answered from one read — it needs
several, and that is the price of the pattern.

**Do not use when.** the step genuinely needs everything. That happens, and the honest
response is to split the step, not to exempt the stage. A stage that needs all of Layer 3
is a stage whose Inputs declaration has degenerated.

**Qualification.** This pattern is not a property a workspace has; it is a property a
*host* either honours or does not. It holds where DP-03's declaration is enforced and
fails silently where it is advisory — the same execution-model qualification as DP-03,
and the same two blocking checks.

**Corrupted by** [AP-08](00-glossary.md#12-anti-pattern-catalogue) — monolithic
accumulation — and [AP-03](00-glossary.md#12-anti-pattern-catalogue).

---

### DP-09 · The deterministic escape hatch

*Kind:* structural · *Evidence:* [§1](../paper/01-introduction.md),
[§4.2](../paper/04-working-implementations/4.2-script-to-animation-pipeline.md) ·
*Universality:* survives all nine axes; the boundary is a **criterion**, not a tool
list — see [ICM-034](00-glossary.md#icm-034--local-script)

**Problem.** Not every step needs a model. Formatting, sorting, validating, rendering,
moving and counting are fully determined by their input, and paying a model to perform
them costs money, adds latency, and introduces variance where there was none.

**Forces.** Uniformity against economy. Every step being a stage gives one contract format
and one review point. Routing determined work to a script gives back both the cost saving
and the uniformity.

**Structure.** Work whose output is fully determined by its input runs as a local script
inside or beside the stage, invoked by the contract's Process section. The test is the
**criterion, not the example**: if the output is a pure function of the input, it is a
script. The paper's examples are Python utilities, which would read as a language
requirement; v2 restates the term so it holds for formatters, linters, compilers, code
generators, schema validators, provisioners and test harnesses in any language, including
none.

**Example.** `03_tests/` has a model stage that reads the plan and writes
`failure-analysis.md`, and a script step that runs the test suite and writes
`results/` deterministically. The script's output is reviewable at the same boundary as
the stage's.

**Consequences.** Gains: determinism where determinism is possible; cost; the ability to
diff a result that *should* be identical between runs — a model output that changes when
nothing changed is a signal, and a script output that changes is a bug. Gives up: the
uniform contract format, and the model cannot reason about a script failure, so a failed
script step yields no diagnosis unless the contract says what to do about it.

**Do not use when.** the step needs judgement. The failure mode is putting judgement in
the script — a script that "decides" is neither deterministic nor reviewable, and it is
where the escape hatch turns into an undeclared framework.

**Corrupted by** — no direct anti-pattern; the failure here is drift, and
[DRC-25](#drc-25--deterministic-work-is-not-routed-through-a-model-stage) catches it.

---

### DP-10 · Sub-workspace delegation

*Kind:* execution · *Evidence:*
[§4.1](../paper/04-working-implementations/4.1-model-and-environment.md),
[§4.2](../paper/04-working-implementations/4.2-script-to-animation-pipeline.md) ·
*Universality:* **host-qualified** — this is the one pattern in the catalogue that a host
may make impossible

**Problem.** Some sub-tasks do not need the strongest model, and giving them the parent's
full context is both expensive and — by the paper's own argument — harmful, because
irrelevant context degrades performance.

**Forces.** Capability against context cost. The orchestrating model is the one that knows
the task; the sub-agent is the one suited to the step. Splitting them means the
sub-agent does not know the task unless it is told.

**Structure.** The sub-agent is given **a contract, not the parent's context**. It reads
its own Layer 0–2 from a sub-directory of the workspace and returns a file. The parent's
context is not inherited; what the sub-agent needs is written down where it can read it.
The paper's implementation delegates Opus→Sonnet with this structure.

**Example.** `02_refactor/02_refactor/CONTEXT.md` delegates "propose the mechanical
rename for the `client_id` field in all three languages" to a sub-agent that reads only
`00_knowledge/naming.md` and the inventory, and returns
`output/rename-plan.md`. It never sees the parent's reasoning about which call sites are
safe to skip.

**Consequences.** Gains: cheaper steps; sub-task context stays small by construction
rather than by hope; the parent's context is not diluted. Gives up: the sub-agent cannot
ask a question, and an ambiguity it resolves silently is invisible.

**Do not use when.** the host has no delegation mechanism. Then the pattern is not
unavailable — it becomes **manual**: a person runs the sub-agent and writes its output
file. What is *not* available is the automatic version. Recording which one you have is
the point, because the failure mode is assuming delegation happened.

**Qualification — host and execution-model axes.** Delegation is a host capability
([ICM-105 routing_rule](00-glossary.md#14-reserved-terms), concern C24). A workspace that
assumes it and runs on a host without it will either silently do the work in the parent
— paying full context cost for a step designed not to — or fail. Neither is observable
from the folder. This is why
[DRC-26](#drc-26--delegated-steps-are-declared-not-implied) exists.

**Corrupted by** [AP-05](00-glossary.md#ap-05--the-contract-as-documentation) — a
sub-contract written once at scaffolding time and never updated. The failure is worse than
for a stage contract, because a stale sub-contract is invisible in the parent's review:
the parent never sees the context the sub-agent actually used.

---

### DP-11 · Verification as a stage

*Kind:* structural · *Evidence:* **proposed by the paper, not built** —
[§6.2](../paper/06-future-directions.md) ·
*Universality:* survives all nine axes; requires **declared criteria**, which the paper
does not yet specify

**Problem.** The paper names a real and recurring failure — stage *n* drifts from stage
*n−2* — and describes an audit file that traces backward and flags inconsistencies by
hand. It then concedes the pattern is a proto-debugger and should be generalised into a
contract section. The generalisation is what turns one practitioner's workaround into a
property of the workspace.

**Forces.** Automatic checking against declared intent. A verifier can only check what
was written down. More criteria means more checks and a more brittle contract.

**Structure.** A contract gains a **`Verify`** section naming (a) which earlier stage
outputs are checked, and (b) the criteria they are checked against. The agent runs these
checks during the stage and flags discrepancies **before** the human reviews — so the
human sees disagreements, not just output. This is the shipped stage-3 audit file,
promoted from instance to pattern.

**Example.** `03_tests/CONTEXT.md` declares: *Verify — check `refactor.diff` against
`02_refactor/output/refactor-plan.md`; criteria: every symbol in the plan appears in the
diff; no symbol appears in the diff that is absent from the plan; the inventory's symbol
count is unchanged.*

**Consequences.** Gains: cross-stage disagreement is surfaced as data rather than
discovered by a reader; the criteria become a durable asset that improves with use; the
verifier is itself a contract, so it is reviewable like anything else. Gives up: it is a
check, not a proof — a criterion that is wrong produces a confident false pass, which is
worse than no check. And it adds contract surface that must be maintained, which is why
it is optional: a workspace with no declared cross-stage risk does not need one.

**Do not use when.** the stage has no upstream it can meaningfully disagree with, or the
"criteria" would be a restatement of the output. A verifier that checks the output against
the output is theatre, and it costs contract surface to maintain.

**Corrupted by** — the same drift as [DP-02](#dp-02--contract-before-content): a
`Verify` section written once and never updated becomes worse than none.

---

### DP-12 · Workspace as artifact

*Kind:* structural · *Evidence:*
[§3.4](../paper/03-interpretable-context-methodology/3.4-portability-and-reproducibility.md) ·
*Universality:* survives all nine axes; **qualified on host** — see below

**Problem.** Systems that need a server need a deployment, an environment, a dependency
tree and a runbook. The handover cost of such a system is not the software; it is the
operational context around it, and it does not transfer.

**Forces.** Portability against capability. A folder has no runtime, so it also has no
runtime's guarantees: no scheduler, no retries, no concurrency, no access control. Every
one of those is a real need that the folder deliberately declines.

**Structure.** The workspace is a folder. It carries its own prompts, context structure
and stage definitions. Copying the folder is a complete handover; committing it produces a
version history of the pipeline's behaviour; there is no separate deployment artifact and
nothing to install. The unit of versioning, sharing and backup is the folder.

**Example.** A consultancy hands a client a folder. The client edits the contracts to
match an evolving process and changes the reference material. No developer is involved,
and no environment is replicated.

**Consequences.** Gains: the paper's central portability claim holds as stated —
there is no server to configure, no environment to replicate, no deployment step. Gives
up: everything in the forces column, deliberately and by name.

**Do not use when.** the workflow needs a capability the folder cannot provide —
guaranteed execution, durable scheduling, real multi-writer concurrency, regulated
audit retention. These are not defects in the pattern; they are the trade. When one is
genuinely required, add an **outer layer that preserves folder-as-state** — the
invariants stay where the paper puts them, and the platform capability stays outside.
The test for a good outer layer is whether a practitioner can still answer *"what happened
in stage 2?"* by opening a file.

**Qualification — host axis.** "Copy the folder" assumes a filesystem with stable paths,
case sensitivity or an explicit case convention, no filename-length limit that would mangle
a deep stage tree, and no character set restriction on the names. These are ordinary
constraints that a laptop satisfies and some platforms do not. Where a host fails one, the
workspace is no longer copyable — which is a portability failure, not a correctness one,
and it is checked in [DRC-40](#drc-40--the-workspace-survives-a-copy).

---

## 3. Choosing between the patterns

The catalogue is not a menu. Most of the twelve are unconditional — a workspace that
omits DP-02 or DP-04 is not a differently-shaped ICM workspace, it is not one. The ones
that are genuine choices are few, and this section says which.

### 3.1 The ones that are choices

| Pattern | Adopt it when | Do not adopt it when |
| --- | --- | --- |
| [DP-01](#dp-01--numbered-stages-as-the-ordering-primitive) numbering | the workflow is linear or has a defensible default order | there is a cycle, a fan-in, or genuinely independent stages — then numbering is display order and `deps` is authoritative ([D-09](DEVIATIONS.md#d-09), C23) |
| [DP-07](#dp-07--recursive-routing) nested index | a Layer 3 collection has more members than a stage would declare anyway | the collection is small enough to declare file by file |
| [DP-09](#dp-09--the-deterministic-escape-hatch) script step | the output is a pure function of the input | the step needs judgement |
| [DP-10](#dp-10--sub-workspace-delegation) delegation | the host supports it and the sub-task does not need parent context | the sub-task needs the parent's reasoning; then run it in the parent and say so |
| [DP-11](#dp-11--verification-as-a-stage) `Verify` | there is a named upstream this stage can plausibly drift from | there is no upstream, or the criteria would restate the output |

Everything else — DP-02 through DP-06, DP-08, DP-12 — is part of the method. A workspace
reviewer who finds one of them absent records a finding; they do not record a preference.

### 3.2 Patterns that must combine

Four groups, each with a load-bearing dependency. Getting one member without another
produces a workspace that looks right and is not.

| Group | Members | The dependency |
| --- | --- | --- |
| **The contract trio** | DP-02 contract, DP-03 scoping, DP-08 depth | DP-03 is the *mechanism* by which DP-08 is achieved. A workspace with contracts but no scoping does not get selective loading; it gets documented non-selective loading, and the token claim fails while looking fine. |
| **The persistence pair** | DP-04 handoff, DP-05 edit surface | A handoff nobody can edit is a black box; an edit surface with no handoff is a file nobody downstream reads. Together they are what makes the pipeline observable at all — and what makes [D-01](DEVIATIONS.md#d-01) expensive, since neither records *what ran*. |
| **The factory pair** | DP-06 factory/product, DP-09 escape hatch | A script that writes into Layer 3 silently reconfigures the factory between runs. The separation must be stated with respect to scripts too, not only to stages. |
| **The portability pair** | DP-12 artifact, DP-01 numbering | DP-12's portability claim is partly a claim about paths being stable, and DP-01 makes the stage tree deep and named. They are checked together in [DRC-40](#drc-40--the-workspace-survives-a-copy) and [DRC-41](#drc-41--the-hosts-directory-sort-produces-the-intended-order). |

### 3.3 Where two patterns pull against each other

Two tensions are real and neither has a free resolution. They are recorded because a
reviewer who finds the tension is being asked to judge, not to score.

**DP-05 against provenance.** The edit surface is what makes human correction cheap, and
it is also what makes it impossible to tell an edited output from a generated one. The
paper's §6.3 resolves this in favour of editing output and treating recurring edits as a
diagnostic signal — but the *detection* of that signal does not exist. Until C21 gives
outputs a declared provenance, the honest position is: edit freely, and accept that
provenance is reconstructed by reading [§6.2](../paper/06-future-directions.md)'s audit
file. [DRC-20](#drc-20--every-editable-output-is-text-or-declares-its-projection)
records the exposure rather than resolving it.

**DP-12 against capability.** Every guarantee a runtime provides is a guarantee the folder
declines. When one is genuinely required, the pressure to put it *inside* the workspace is
strong, because inside is where the rest of the architecture lives. The constraint that
prevents that is narrow and absolute: **state stays in files.** An outer layer may
schedule, retry, authenticate and parallelise; it may not hold the state a practitioner
would need in order to answer a question about a past run. That is the test in
[ICM-076](00-glossary.md#icm-076--workflow-engine--state-machine), and it is the boundary
the paper's §5.2 non-goals draw.

**Corrupted by** [AP-06](00-glossary.md#ap-06--unnumbered-or-misordered-stages) — renaming
and reordering break the paths that every reference in the workspace depends on, and a
workspace with no version history has no way to find out which references broke. The
corruption is portability-shaped: it does not stop the workspace working on the machine it
was built on.

### 3.4 What is deliberately not in the catalogue

- **The anti-patterns.** [AP-01](00-glossary.md#12-anti-pattern-catalogue)–[AP-12](00-glossary.md#12-anti-pattern-catalogue)
  are *corruptions* of patterns, and each one names the pattern it corrupts. Listing them
  as patterns would invert the relationship.
- **The workspace-builder.** §4.4 describes a five-stage workspace whose output is a new
  workspace. That is an *instance* built from DP-01 through DP-12, not a pattern the
  others are instances of. The distinction matters: the builder enforces structure, and
  [AP-11](00-glossary.md#12-anti-pattern-catalogue) records that structure is not fitness.
- **The five principles.** [ICM-040](00-glossary.md#icm-040--one-stage-one-job) through
  [ICM-043](00-glossary.md#icm-043--configure-the-factory-not-the-product) are the axioms.
  Patterns are the recurring ways of satisfying them; §6.2 maps each to the principle it
  serves, so an axiom with no pattern is visible as a gap.

---

## 4. The design review checklist

### 4.1 What a review is, and when it runs

A design review examines **a folder, before it runs**. It answers one question: *is this
workspace shaped so that the paper's claims can hold for it?* It does not judge whether the
work is good, whether the prompts will work, or whether the output will be correct — those
are the subject of §4.5 and of concerns C27 and C7.

Three rules govern it:

1. **Blocking items gate the first run.** A workspace that fails a blocking item will not
   fail visibly; it will fail plausibly, which is more expensive. Nothing else in this
   file is as time-sensitive.
2. **Every item is answerable from the folder.** If answering it requires running
   something, it is not in this checklist — it is in the evaluation harness (C27) or the
   runtime concerns (C10, C11, C15). This keeps the checklist executable by a reviewer who
   has never seen the project.
3. **Conditional groups activate by axis.** Groups D1–D8 activate only when the workspace
   occupies that cell of [README §2](README.md#2-the-universality-bar). A single-stage
   workspace in one language on one laptop skips D1–D8 entirely. This is how the checklist
   meets the universality bar: it is not one list applied to everything, it is a core list
   plus nine conditional lists, so no cell is either over-served or silently skipped.

### 4.2 Levels

| Level | Means | Gate |
| --- | --- | --- |
| **L1** structural | answerable from the directory tree with `ls`, `find`, `grep` | before the first run |
| **L2** contractual | answerable from the contract text plus the tree | before the first run |
| **L3** semantic | requires judgement — a human reading the contract against the work | before the first unattended run |
| **L4** operational | requires a run | **not a design review**; listed in §4.7 so it is not mistaken for one |

### 4.3 Group A — structure (L1, always)

#### DRC-01 · Every stage folder carries a contract

- **Check.** Every directory matching `[0-9][0-9]_*/` contains exactly one `CONTEXT.md`.
- **How.** `for d in [0-9][0-9]_*/; do n=$(ls "$d"CONTEXT.md 2>/dev/null | wc -l); [ "$n" = 1 ] || echo "$d: $n"; done`
- **Fail.** A numbered directory with no contract, or with two. The stage's behaviour is
  then undeclared, and no review of it is possible.
- **Enforces.** DP-02 · catches AP-05

#### DRC-02 · No stage directory sits outside the stage range

- **Check.** Every top-level entry is either the stage pattern or a declared shared area
  (`references/`, `_config/`, `shared/`, `output/`, `_runs/`). A directory such as
  `draft/` or `final/` at the workspace root is a finding.
- **How.** `ls -d */ | grep -vE '^[0-9]{2}_|^references$|^_config$|^shared$|^output$|^_[a-z_]+$'`
- **Fail.** Unnumbered directories. Order becomes a matter of opinion, and a human's "next"
  and the agent's "next" diverge.
- **Enforces.** DP-01 · catches AP-06

#### DRC-03 · Outputs land inside the owning stage

- **Check.** No stage writes outside its own directory. Every `->` destination in a
  contract's Outputs section resolves inside that stage folder.
- **How.** Extract every `-> <path>` from every contract and confirm each resolves under
  its own stage directory.
- **Fail.** A destination that is the workspace root, a sibling stage, or an absolute
  path. The next stage's declared input no longer resolves and nothing reports it.
- **Enforces.** DP-04 · catches AP-04

#### DRC-04 · Stage names are unique and stably ordered

- **Check.** The zero-padded prefixes are unique and the directory listing sorts into the
  order the contracts imply. Confirm the sort rather than assume it — see the host
  qualification in [DP-01](#dp-01--numbered-stages-as-the-ordering-primitive).
- **How.** `ls -d [0-9][0-9]_*/ | sort` and compare against the order named in Layer 1.
- **Fail.** Duplicate prefixes, or a sort that disagrees with Layer 1's stated order. On a
  case-insensitive or non-ordering filesystem, this is the check that finds out.
- **Enforces.** DP-01

### 4.4 Group B — contracts (L2, always)

#### DRC-05 · Every contract has Inputs, Process and Outputs

- **Check.** All three sections are present in every contract, and none is empty.
- **How.** For each `CONTEXT.md`, assert one `## Inputs`, one `## Process`, one
  `## Outputs`.
- **Fail.** A missing section. The paper's three-part structure is the whole interface;
  the empty Inputs case is [D-03](DEVIATIONS.md#d-03), and the empty-Outputs case means
  the stage has no defined deliverable.
- **Enforces.** DP-02

#### DRC-06 · Inputs are labelled by layer

- **Check.** Every Inputs entry names its layer — Layer 3 or Layer 4 — as the paper's own
  worked example does.
- **How.** Each entry matches `Layer 3|Layer 4`.
- **Fail.** An entry naming only a path. The layer is what tells the model whether to
  *internalise* the material as a constraint or *process* it as input
  ([§3.2](../paper/03-interpretable-context-methodology/3.2-architecture.md) Table 2);
  an unlabelled entry loses that distinction, which is the whole point of the split.
- **Enforces.** DP-03, DP-06

#### DRC-07 · Every input path resolves

- **Check.** Every path named in Inputs exists, or is marked as produced by an upstream
  stage's Outputs.
- **How.** Resolve each path; cross-reference against the producing stage's Outputs.
- **Fail.** A path that resolves to nothing and has no producer. This is the static form
  of [AP-09](00-glossary.md#12-anti-pattern-catalogue).
- **Enforces.** DP-03 · catches AP-09

#### DRC-08 · Layer 1 routes to every stage that exists

- **Check.** Every stage in the tree is named in the workspace routing file, and every
  stage the routing file names exists.
- **How.** Extract stage names from both and diff.
- **Fail.** A stage that exists but is unreachable, or one that is routed but does not
  exist. Either way the agent's belief about where it is is wrong, which is the one thing
  Layer 0 exists to prevent.
- **Enforces.** DP-01, DP-07 · catches AP-10

#### DRC-09 · The process section states one describable outcome

- **Check.** One sentence completes: *"this stage produces ___."* If the honest answer
  needs an "and", the stage is doing two jobs.
- **How.** Read each `## Process` and write the sentence. Judge it, do not count tokens.
- **Fail.** No sentence fits. [AP-01](00-glossary.md#12-anti-pattern-catalogue), the god
  stage, and the single most common structural defect in workspaces that grow without
  anyone splitting them.
- **Level.** L3 — requires judgement about the work
- **Enforces.** DP-02, DP-08

#### DRC-10 · Outputs are pinned and named

- **Check.** Every Outputs entry names a file and ends in `-> output/`. Nothing is
  written to a wildcard location, and nothing is described only as "a summary".
- **How.** Every Outputs line matches `<name> -> output/`.
- **Fail.** An unpinned destination or an unnamed output. The next stage's declared input
  has nothing to resolve against.
- **Enforces.** DP-04 · catches AP-04

#### DRC-11 · Every Inputs entry carries a purpose

- **Check.** Each entry says what the stage needs the file *for*, not merely where it is.
- **How.** Read each entry; it should survive the question "why does this stage load
  this?" without a shrug.
- **Fail.** An entry that could be deleted with no change to what the stage can do. That
  entry is context the model pays for and does not use — the failure the paper's whole
  argument is against, arriving through the front door.
- **Enforces.** DP-03 · catches AP-03

#### DRC-12 · No input declaration is a whole directory or a bare glob

- **Check.** Every entry is a named file, or a glob with a stated downstream need.
- **How.** Any entry ending in `/` or containing `*` must be accompanied by a justification
  naming what consumes the extra files.
- **Fail.** `../01_research/output/` with no justification — which is, verbatim, the form
  the paper's own worked contract uses. A directory entry is the mechanism by which a
  workspace returns to a monolithic prompt while still looking designed.
- **Enforces.** DP-03, DP-08 · catches AP-03

#### DRC-13 · No contract names a model or a vendor product

- **Check.** No `CONTEXT.md` contains a model name, a vendor name, or a feature only one
  family of models is documented to have.
- **How.** Search contracts for known model and vendor strings.
- **Fail.** A hit. The protocol claims model-agnosticism
  ([D-08](DEVIATIONS.md#d-08)); a contract that hardcodes a model is not portable, and the
  portability is invisible until someone moves the workspace.
- **Level.** L2
- **Enforces.** DP-12 · the portability half of [ICM-050](00-glossary.md#icm-050--model-agnosticism)

#### DRC-14 · Every stage declares a review boundary, or declares that it has none

- **Check.** Each contract states whether a human reviews its output before the next stage
  consumes it. The default is *yes*; declaring *no* is allowed and must be explicit.
- **How.** Each contract contains an explicit review line.
- **Fail.** Silence. The paper's Figure 4 shows a review gate at every boundary
  ([§3.3](../paper/03-interpretable-context-methodology/3.3-stage-contracts-and-handoffs.md)),
  but that is a caption, not a mechanism — and the gate's absence from the folder is the
  thing that makes oversight claims checkable or not. Making it declarable is concern C6;
  until then this check records the intent in the folder, which is the precondition for
  that work.
- **Level.** L2
- **Enforces.** DP-05

### 4.5 Group C — content layers (L1–L2, always)

#### DRC-15 · Layer 3 files pass the different-topic test

- **Check.** For each reference file: *would this be byte-identical on a different topic?*
  Yes → Layer 3 is correct. No → it is run content in the wrong place.
- **How.** Apply the test per file. It is a judgement, but a cheap one.
- **Fail.** A reference file containing findings, measurements, or anything that changes
  per run.
- **Enforces.** DP-06 · catches AP-02

#### DRC-16 · No single file mixes rules and run content

- **Check.** Each file is entirely rules or entirely run content.
- **How.** For each file, ask whether the file would need rewriting if the *topic* changed
  but the *method* did not.
- **Fail.** A file that needs rewriting for both reasons. The model cannot be told which
  half to obey and which to transform, so it transforms both.
- **Enforces.** DP-06 · catches AP-02

#### DRC-17 · Every Layer 3 file is reachable

- **Check.** Each reference file is declared by at least one stage, or listed in a
  collection index ([DP-07](#dp-07--recursive-routing)).
- **How.** Build the set of declared inputs plus index members; diff against the Layer 3
  tree.
- **Fail.** An orphan. It is context nobody loads, or — worse — context somebody loads by
  hand once, which is how an undeclared dependency enters a workspace.
- **Enforces.** DP-06, DP-07

#### DRC-18 · No Layer 3 file is written by a run

- **Check.** No contract's Outputs destination resolves into a Layer 3 area, and no
  contract's Process section directs the agent to write to one.
- **How.** Resolve every Outputs destination and confirm it is under a stage's `output/`.
  Confirm no Process line names a reference path as a write target.
- **Fail.** Any hit. This is the direct form of
  [DP-06](#dp-06--factory-and-product-separation)'s central rule: **nothing a run writes
  is Layer 3.** A promoted cache or a machine-generated rule file changes the factory
  between runs and is the most expensive single error in the contrast-term list.
- **Enforces.** DP-06 · catches [ICM-077](00-glossary.md#icm-077--cache)

#### DRC-19 · Layer 0 and Layer 1 do not hardcode a vendor filename

- **Check.** The identity and routing files do not name a specific vendor's project file
  as *the* mechanism.
- **How.** Check what Layer 0 and Layer 1 actually instruct the agent to load.
- **Fail.** A hardcoded vendor filename presented as the universal mechanism. This is
  [D-07](DEVIATIONS.md#d-07) exactly: [Figure 1](../figures/figure-1.svg) prints
  `CLAUDE.md` and `CONTEXT.md` inside a diagram whose caption claims the protocol does not
  depend on any model-specific capability. A workspace that copies the figure inherits the
  contradiction.
- **Enforces.** DP-12 · a design review is the cheapest place to catch a figure's error
  propagating into a real workspace

#### DRC-20 · Every editable output is text, or declares its projection

- **Check.** Every file in a stage's `output/` is either text a human can open and edit,
  or has a companion projection file naming the tool, version and options used to derive
  the text.
- **How.** For each output file, either it is text, or a projection record exists for it.
- **Fail.** An opaque artifact with no projection record. The paper forbids binary formats
  ([§3.1](../paper/03-interpretable-context-methodology/3.1-design-principles.md)) and then
  ships a workspace that reads PDFs ([§4.3](../paper/04-working-implementations/4.3-course-deck-production.md))
  — [D-02](DEVIATIONS.md#d-02). Until the type system lands (C16), the workspace must at
  minimum record that a conversion happened and how. Without it nobody can tell whether the
  extraction dropped a table, and no diff exists when the source changes.
- **Enforces.** DP-05 · catches AP-12

### 4.6 Group D — conditional, by universality axis

These groups activate only when the workspace occupies that cell of
[README §2](README.md#2-the-universality-bar). Each item states its activation condition,
so a reviewer on a single-stage laptop workspace knows to skip the rest rather than guess.

#### D1 · Source language and codebase topology

*Activates when:* the workspace touches source code, or more than one language.

#### DRC-21 · A cross-language change is decomposed per language

- **Check.** Where one logical change spans languages, each language's edit is a separate
  stage or a clearly delimited section of one contract, and exactly one place owns the
  decision that they must agree.
- **Why it matters.** This is the case that separates ICM from a folder of scripts. The
  per-language stages are mechanical; the agreement between them is the judgement, and it
  has exactly one owner. A workspace where three language edits are one stage is a god
  stage ([AP-01](00-glossary.md#12-anti-pattern-catalogue)); a workspace where three
  language edits are three stages with no owner for the agreement will produce three
  individually-correct migrations that do not compile together.
- **How.** Name the stage that owns the cross-language decision and confirm it exists.
- **Enforces.** DP-01, DP-02

#### DRC-22 · Stage names are domain nouns, not tool names

- **Check.** No stage is named after a language, a tool, or a model (`02_python`,
  `03_claude`, `01_gpt_review`).
- **Why it matters.** Tool-named stages encode a host assumption into a structure whose
  selling point is that it survives the host. Renaming is then a breaking change to every
  path that referenced it.
- **Enforces.** DP-01, DP-12 · [ICM-050](00-glossary.md#icm-050--model-agnosticism)

#### DRC-23 · Vendored, generated and build-output trees are excluded by default

- **Check.** `vendor/`, `node_modules/`, `dist/`, `target/`, `.venv/`, generated clients
  and lockfiles are not declared as Inputs anywhere.
- **Why it matters.** A glob that reaches into a vendored tree makes the token count a
  function of someone else's dependency graph, and the workspace stops being portable at
  the moment a dependency changes.
- **How.** Grep Inputs for those path fragments.
- **Enforces.** DP-03, DP-08

#### DRC-24 · Every stage that reads source declares how it locates it

- **Check.** A stage whose job involves finding relevant source names the mechanism: a
  declared path set, a declared query, or a declared index. "Find the relevant files" is
  not a mechanism.
- **Why it matters.** This is where the paper's *no retrieval* position
  ([ICM-079](00-glossary.md#icm-079--retrieval-rag--code-intelligence)) meets real code,
  where the relevant files genuinely are not known in advance. Declaring the lookup keeps
  it inspectable. Replacing the lookup with a semantic index does not; that is concern C20
  and it changes the architecture rather than extending the contract.
- **Enforces.** DP-03

#### D2 · Artifact medium and division of labour

*Activates when:* any input or output is not plain text the human can open, **or** the
pipeline mixes work that needs a model with work that does not, **or** any step depends on
the host delegating it. The three conditions are grouped because they share one failure
shape — an expensive or invisible substitution happening where the folder says nothing —
and because a workspace meeting none of them simply finds no items in this group.

#### DRC-25 · Deterministic work is not routed through a model stage

- **Check.** For each stage, ask whether its output is fully determined by its input. If
  yes, it should be a script step, not a model call.
- **Why it matters.** The paper gives this as a local-script pattern; a design review is
  the cheapest place to catch a model being asked to sort, format, or validate. Two costs:
  money, and — worse — the introduction of variance where the output should be identical
  between runs. A deterministic step whose output changes when nothing changed is a
  diagnostic signal; a model step losing that signal is a lost diagnostic.
- **How.** Apply the criterion, not the tool list — see [ICM-034](00-glossary.md#icm-034--local-script).
- **Enforces.** DP-09

#### DRC-26 · Delegated steps are declared, not implied

- **Check.** Any step the host is expected to delegate names itself as delegated, with the
  sub-context it receives. A workspace does not assume delegation it has not verified on
  its actual host.
- **Why it matters.** Delegation is a host capability, not a workspace property
  ([ICM-105](00-glossary.md#14-reserved-terms), C24). A workspace that assumes it and runs
  where it is unavailable will silently do the work in the parent — paying full context
  cost for a step designed not to — and nothing in the folder records that this happened.
- **How.** Search contracts for a delegation declaration; if the host supports it, confirm
  at least one is present where the design calls for it.
- **Enforces.** DP-10

#### DRC-27 · Non-text inputs declare their projection

- **Check.** Every PDF, spreadsheet, image, audio or video input has a declared extraction
  naming the tool, its version, and its options — and the extraction is a named step in the
  pipeline, not something the agent improvises.
- **Why it matters.** [D-02](DEVIATIONS.md#d-02) is the paper contradicting itself: §3.1
  forbids binary formats, §4.3 consumes PDFs. A design review cannot fix that, but it can
  require that where it happens, the conversion is recorded — which is the difference
  between an inspectable extraction and an undeclared one ([AP-12](00-glossary.md#12-anti-pattern-catalogue)).
- **Enforces.** DP-05 · artifact-type enforcement is concern C16

#### D3 · Workflow shape

*Activates when:* the workflow is not a straight line.

#### DRC-28 · Non-linear workflows state default order, not dependency

- **Check.** If any stage does not depend on its predecessor, the workspace says so, and
  the numbering is labelled as a default order rather than a dependency relation.
- **Why it matters.** [D-09](DEVIATIONS.md#d-09). The paper states *"Stage sequencing is
  the folder numbering"*, which is a total order presented as a dependency. Where the two
  differ, someone will eventually rely on the wrong one. Declared dependencies are
  authoritative (C23).
- **Enforces.** DP-01

#### DRC-29 · The declared inputs contain no cycle

- **Check.** Build the graph from Inputs declarations and confirm it is acyclic.
- **Why it matters.** A cycle cannot be scheduled by reading a directory listing in any
  order, so a cyclic workspace has no defined execution at all. It will appear to work
  until the first run where the ordering matters, which is exactly the kind of latent
  failure this checklist exists to prevent.
- **How.** Topological sort of the stage graph; a leftover node is a finding.
- **Enforces.** DP-01, DP-04

#### DRC-30 · Every decision point names who decides and where it is recorded

- **Check.** Any conditional step states whether a human decides or a deterministic check
  decides, and names the file the decision is written to.
- **Why it matters.** Branching is out of scope per [§5.2](../paper/05-discussion.md), and
  the paper is right that automating it turns ICM into a framework. But a human branching
  by hand is *already* a branching mechanism, and it is undocumented by default. An
  unrecorded decision cannot be reviewed later — which is the interpretive property the
  paper is claiming. Conditionals are concern C29; this check only requires that the
  manual ones be visible.
- **Enforces.** DP-02, DP-05

#### D4 · Execution model

*Activates when:* runs can overlap, be scheduled, or be triggered by events.

#### DRC-31 · Overlapping runs write to run-scoped areas

- **Check.** If two runs of the same stage can overlap, their outputs do not share a path.
- **Why it matters.** [ICM-090 run_id](00-glossary.md#14-reserved-terms) and
  [ICM-091 lock](00-glossary.md#14-reserved-terms) are reserved by C10 and undefined today.
  The consequence is that *"the file in `output/`"* is ambiguous, and every downstream
  contract that names it is ambiguous. The static mitigation available now is a run-scoped
  directory; the mechanism is C10.
- **Enforces.** DP-04, DP-06

#### DRC-32 · The host's input enforcement is named

- **Check.** The workspace states how declared inputs are enforced on its actual host — a
  scoped mount, a tool that reads only the declared set, or an explicit admission that the
  declaration is advisory.
- **Why it matters.** [DP-03](#dp-03--declared-context-scoping)'s qualification. If the
  declaration is advisory, the token claim in §3.2 is not established by the workspace and
  the reviewer should say so in the review record rather than let it pass silently.
- **Enforces.** DP-03, DP-08

#### DRC-33 · Stages are idempotent, or declare how they are not

- **Check.** Re-running a stage with unchanged inputs produces the same files. If a stage
  cannot satisfy that, it says which part of its output is expected to vary.
- **Why it matters.** Idempotence is what makes re-running safe after an edit anywhere
  upstream, which is the paper's incremental-recompilation claim
  ([ICM-057](00-glossary.md#icm-057--incremental-recompilation)). Without it, a re-run
  silently replaces a human's edit — [AP-07](00-glossary.md#12-anti-pattern-catalogue) — and
  the workspace stops being safe to iterate on.
- **How.** Static form: confirm the Outputs list names files rather than appending
  (`log.txt` rather than `log-2.txt`); dynamic form is in Group E.
- **Enforces.** DP-05, DP-04

#### D5 · Team and tenancy

*Activates when:* the workspace has more than one author or operator.

#### DRC-34 · Layer 3 has a named owner

- **Check.** The workspace states who may change reference material, and who maintains it.
- **Why it matters.** Layer 3 is the factory
  ([ICM-016](00-glossary.md#icm-016--the-factory)); an unowned factory is changed
  incidentally by whoever is nearest, and the change is indistinguishable from a run's
  output in a commit.
- **Enforces.** DP-06

#### DRC-35 · Multi-writer operation states its conflict policy for Layer 3

- **Check.** If two people edit reference material concurrently, the workspace says how
  that resolves — merge by review, single-writer, or file-level ownership.
- **Why it matters.** Concurrency for Layer 3 is concern C35 and is unbuilt. Until it is,
  the honest options are the ones that already work: single-writer, or disjoint files per
  owner. What is not acceptable is discovering the policy during a conflict.
- **Enforces.** DP-06

#### DRC-36 · A stranger can answer "what happened in stage 2" from files

- **Check.** Someone who did not build the workspace can open the stage's folder and its
  contract and state what the stage did, what it read, and what it produced.
- **Why it matters.** This is the paper's portability and interpretability claims
  ([§3.4](../paper/03-interpretable-context-methodology/3.4-portability-and-reproducibility.md),
  [§5.3](../paper/05-discussion.md)) reduced to a single testable act. It is the cheapest
  proxy for [D-01](DEVIATIONS.md#d-01) available before run records exist: if the answer
  requires the operator's memory, the workspace is not self-describing.
- **How.** Hand the folder to a colleague and ask the question. Time-boxed.
- **Enforces.** DP-12, DP-02

#### D6 · Scale

*Activates when:* the workspace has many stages, or large artifacts.

#### DRC-37 · At ten or more stages, Layer 1 is an index

- **Check.** With many stages, the routing file names each stage with a one-line purpose
  rather than carrying the full contract inline.
- **Why it matters.** Layer 1's own cost grows linearly with the workspace, and it is
  loaded at every stage
  ([ICM-012](00-glossary.md#icm-012--layer-1--workspace-routing-file)). Past a threshold it
  stops being affordable without the [DP-07](#dp-07--recursive-routing) shape.
- **Enforces.** DP-07, DP-08

#### DRC-38 · Every stage's declared context has a stated ceiling

- **Check.** Each contract states an expected context size, or the workspace states one
  ceiling that applies to all stages.
- **Why it matters.** The paper's 2,000–8,000 tokens per stage is its central efficiency
  claim and it is currently an assertion with no measurement
  ([ICM-047](00-glossary.md#icm-047--context-window-composition)). Writing the ceiling into
  the contract makes the claim falsifiable by a reader today, even before the estimator
  exists. Enforcement is C15; this check only requires the number to exist and be honest.
- **Enforces.** DP-08

#### DRC-39 · The largest expected artifact is stated, and its overflow path is named

- **Check.** The workspace states what happens when a stage's output grows past what the
  next stage can consume in one context — a declared split point, a declared chunking
  rule, or an explicit acceptance that the workspace does not handle it.
- **Why it matters.** The paper's claim that Layer 4 *"rarely exceeds a few thousand tokens
  when the previous stage has done its job"* is a claim about a well-behaved pipeline.
  Real pipelines are not always well-behaved. Overflow handling is C8; naming it now is
  what makes C8's absence a decision rather than an oversight.
- **Enforces.** DP-08

#### D7 · Host

*Activates when:* the workspace runs anywhere other than the machine that built it.

#### DRC-40 · The workspace survives a copy

- **Check.** Copy the folder to a different path, on a different filesystem if possible,
  and confirm: no path exceeded a length limit, no filename depends on a character set
  the target rejects, no two names collide under case-insensitivity, and the routing file's
  relative paths still resolve.
- **Why it matters.** [DP-12](#dp-12--workspace-as-artifact)'s central claim is that a
  handover is a file copy. Deep stage trees with descriptive names are exactly the case
  that breaks on a constrained target, and it breaks *silently* — a truncated name is a
  different file, not an error.
- **Enforces.** DP-12

#### DRC-41 · The host's directory sort produces the intended order

- **Check.** `ls -d [0-9][0-9]_*/ | sort` on the actual host yields the intended execution
  order.
- **Why it matters.** [DP-01](#dp-01--numbered-stages-as-the-ordering-primitive)'s
  ordering is the host's sort, not a rule the workspace enforces. Case-insensitive
  filesystems, network mounts without ordering guarantees, and locales with unusual
  collation all change the answer. This is a two-second check that prevents a class of
  failure nobody would otherwise find.
- **Enforces.** DP-01

#### DRC-42 · The host provides the filesystem semantics folder-as-handoff assumes

- **Check.** Reads see whole files, and a directory listing is not stale. Where the host is
  an object store or an eventually-consistent mount, the workspace records the deviation
  and states how handoff visibility is achieved instead.
- **Why it matters.** [DP-04](#dp-04--folder-as-handoff)'s gives-up column — a partially
  written file is indistinguishable from a complete one — is acceptable on a local disk and
  dangerous elsewhere. Recording it is what prevents the workspace being ported to a host
  where its core assumption is false.
- **Enforces.** DP-04, DP-12

#### D8 · Compliance regime

*Activates when:* a regulated requirement applies to the output or the process.

#### DRC-43 · Structural alignment is claimed by principle, never as compliance

- **Check.** Where the workspace invokes the paper's oversight argument, it names the
  *principle* it aligns with and does not assert that it complies with a regime.
- **Why it matters.** [D-01](DEVIATIONS.md#d-01) and
  [ICM-067](00-glossary.md#icm-067--compliance-alignment). The paper is careful here — it
  says compliance *"is a legal question this paper does not attempt to answer"* — and a
  workspace that over-claims on its behalf converts a correct structural observation into a
  false legal statement. This is the check that keeps v2 from over-reaching on the point
  where the paper was most careful.
- **Enforces.** DP-12

#### DRC-44 · Retention obligations name their owner

- **Check.** If outputs must be retained for a stated period, the workspace names what is
  retained, for how long, and who is responsible.
- **Why it matters.** Interpretability is not retention. A workspace that is inspectable
  in the moment satisfies nothing that requires an artefact to still exist in six months,
  and under §3.4's Git-compatible-by-default property, retention is a decision about
  commits, not a property of the folder. Mechanism is C21.
- **Enforces.** DP-12

#### DRC-45 · Human intervention points name who may intervene

- **Check.** Where a review boundary exists, the workspace names who is expected to make
  the intervention, and what they are authorised to change.
- **Why it matters.** [ICM-097 role](00-glossary.md#14-reserved-terms) is reserved and
  undefined. The paper's oversight argument rests on the human being present; a regulated
  regime requires the human to be *identifiable*. Until roles exist, this is a written
  statement — which is not access control, and must not be recorded as if it were.
- **Enforces.** DP-05, DP-12

### 4.7 Group E — real, important, and not a design review

These five are the checks a practitioner most wants and this file cannot supply, because
each is only answerable after the workspace has run. They are listed with stable IDs so
they are never mistaken for done, and each names the concern that will own it.

**A blocking finding in Group A or B is not a reason to proceed. A missing Group E check
is not a reason to stop — it is a reason to know what you do not know.**

#### DRC-46 · Every declared input resolves at the moment the stage runs

- **Question.** Do all of a stage's declared inputs exist and parse when it starts?
- **Why it is not here.** It is a runtime fact; the static form ([DRC-07](#drc-07--every-input-path-resolves))
  cannot see a producer that failed.
- **Owner.** C5 — pre-stage input existence validation
- **Consequence while unbuilt.** [AP-09](00-glossary.md#12-anti-pattern-catalogue), silent
  skip: the agent proceeds on partial context and produces plausible output from nothing.
  The workspace cannot detect this today.

#### DRC-47 · Declared inputs are actually enforced

- **Question.** On this host, can the agent reach anything outside its declared set?
- **Why it is not here.** It is a property of the host and the agent, not of the folder.
  The workspace can only declare intent ([DRC-32](#drc-32--the-hosts-input-enforcement-is-named)).
- **Owner.** C12 — automated context scoping
- **Consequence while unbuilt.** The token claim in §3.2 is unverified for this workspace.
  A review should record it as unverified rather than pass it.

#### DRC-48 · Delivered context per stage is within the budget

- **Question.** How many tokens does each stage actually receive?
- **Why it is not here.** It requires an estimator that does not exist yet; the paper
  asserts the figures and computes nothing.
- **Owner.** C15 — token estimation and budgets
- **Consequence while unbuilt.** [DRC-38](#drc-38--every-stages-declared-context-has-a-stated-ceiling)
  checks that a number was written down. Nothing checks that the number is true.

#### DRC-49 · Each output records where it came from

- **Question.** Given a sentence in a stage-3 output, can you identify the instruction,
  reference file, or upstream output that produced it?
- **Why it is not here.** Provenance does not exist. The paper proposes it in §6.2 and
  says plainly that these ideas *"are not yet implemented"*.
- **Owner.** C21 (records) and C27 (evaluation); the proposal is §6.2's
  [`Verify` section](https://arxiv.org/html/2603.16021v2#S6.SS2) and proto-debugger
- **Consequence while unbuilt.** The paper's §6.3 edit-source principle has no input.
  Recurring output edits cannot be detected, so a workspace cannot learn from them, and
  [D-01](DEVIATIONS.md#d-01)'s observability claim stays structural rather than
  evidentiary.

#### DRC-50 · A mid-pipeline edit invalidates exactly the downstream stages that read it

- **Question.** Edit a stage-2 output and re-run; does stage 3 re-run, and does stage 4
  re-run if it reads stage 3?
- **Why it is not here.** It is an execution fact. The folder structure supports it
  (§6.1) and nothing enforces it.
- **Owner.** C23 — stage dependency graph and staleness
- **Consequence while unbuilt.** Stale downstream artifacts are read as current, which is
  [AP-08](00-glossary.md#12-anti-pattern-catalogue) and the most expensive failure in a
  long pipeline — it is invisible precisely because the pipeline is long.

---

## 5. The review record

A review that produces only findings is a checklist run. A review that produces a
**record** is a workspace artifact, and it is written in the workspace like everything else
— because a review nobody can re-read is how a workspace drifts back into the state the
review caught.

### 5.1 Format

The record is one file per review, stored outside the stage range so it is never mistaken
for a stage, and named so the ordering is readable:

```
reviews/2026-10-04-initial/CONTEXT.md
```

It carries the same three sections as a stage contract, which is deliberate: the paper's
literate-programming property should apply to the workspace's own maintenance artifacts,
or it is a property of the content stages only.

| Section | Contents |
| --- | --- |
| `## Inputs` | the workspace revision reviewed, the axes it occupies, and who reviewed it |
| `## Process` | the findings, each as `DRC-NN · verdict · note` |
| `## Outputs` | the resulting changes to the workspace, or an explicit decision not to change anything |

Verdicts are exactly three: **pass**, **finding** (a defect in the workspace), **unverified**
(the check could not be answered from the folder). **No verdict of "probably fine"**, and
no silent omissions — an item that was not reached is recorded as unverified, which is the
only honest way to record it.

### 5.2 Why `unverified` is a first-class verdict

Three of the checks in §4 require a judgement about work the reviewer may not understand
([DRC-09](#drc-09--the-process-section-states-one-describable-outcome),
[DRC-15](#drc-15--layer-3-files-pass-the-different-topic-test),
[DRC-21](#drc-21--a-cross-language-change-is-decomposed-per-language)). A reviewer who
records them as passes is asserting something they did not check.

Recording `unverified` instead has a cost — the review looks incomplete — and the cost is
worth paying, because it distinguishes *"I checked and it is correct"* from *"I could not
check"*. In a method whose entire thesis is that state should be inspectable, a review
format that cannot record its own epistemic state is a defect in the method.

### 5.3 Worked review — `02_refactor`

A three-line illustration of the record, to show the shape rather than the content. Stage
names are the polyglot API rename from the rest of this tree.

```markdown
# Design review — 02_refactor

## Inputs
- Workspace revision: `a1b2c3d` (post-migration-plan, pre-rename)
- Axes occupied: source language (TS, Go, SQL) · topology (monorepo) ·
  artifact medium (text + PDF vendor spec) · execution model (batch) ·
  team (two authors) · host (laptop + CI runner) · compliance (none)
- Reviewer: not the author of stage 02

## Process
- DRC-01 pass — five stages, five contracts.
- DRC-07 FINDING — `02_refactor` declares `../01_inventory/output/`; the stage also reads
  `inventory-schema.md`, which is not declared. Fixed: declared, or dropped if unused.
- DRC-09 pass — stage 02 produces "a rename plan and a diff". Two outcomes, one job:
  the plan is derived, the diff applies it.
- DRC-11 FINDING — `naming.md` is declared with no purpose. It is the file that decides
  whether generated DB columns are renamed. Purpose added.
- DRC-21 pass — TS, Go and SQL edits are three stages; stage 02 owns the agreement.
- DRC-26 unverified — reviewer cannot confirm the host delegates. Recorded as unverified
  rather than passed.
- DRC-40 pass — copied to a 200-character path; longest resolved path 61 chars.

## Outputs
- Added the missing declaration and the missing purpose to `02_refactor/CONTEXT.md`.
- `review/DRC-26-delegation.md` opened: confirm on the CI runner before the rename runs.
- Re-review scheduled after stage 02's first unattended run.
```

Two things in that record are worth naming. The `unverified` at DRC-26 is the honest
outcome and produces an *action* rather than a gap. And the review output is itself
written into the workspace, which is the difference between a review that improved
something and a review that happened.

---

## 6. Coverage

### 6.1 Every check enforces something; every pattern is enforced

| Check | Enforces | | Pattern | Enforced by |
| --- | --- | --- | --- | --- |
| [DRC-01](#drc-01--every-stage-folder-carries-a-contract) | DP-02 | | [DP-01](#dp-01--numbered-stages-as-the-ordering-primitive) | DRC-02, 04, 21, 28, 41 |
| [DRC-02](#drc-02--no-stage-directory-sits-outside-the-stage-range) | DP-01 | | [DP-02](#dp-02--contract-before-content) | DRC-01, 05, 09, 11, 36 |
| [DRC-03](#drc-03--outputs-land-inside-the-owning-stage) | DP-04 | | [DP-03](#dp-03--declared-context-scoping) | DRC-06, 07, 11, 12, 23, 24, 32 |
| [DRC-04](#drc-04--stage-names-are-unique-and-stably-ordered) | DP-01 | | [DP-04](#dp-04--folder-as-handoff) | DRC-03, 10, 29, 33, 42, 50 |
| [DRC-05](#drc-05--every-contract-has-inputs-process-and-outputs) | DP-02 | | [DP-05](#dp-05--the-edit-surface) | DRC-14, 20, 27, 33, 45 |
| [DRC-06](#drc-06--inputs-are-labelled-by-layer) | DP-03, DP-06 | | [DP-06](#dp-06--factory-and-product-separation) | DRC-06, 15, 16, 17, 18, 34, 35 |
| [DRC-07](#drc-07--every-input-path-resolves) | DP-03 | | [DP-07](#dp-07--recursive-routing) | DRC-08, 17, 37 |
| [DRC-08](#drc-08--layer-1-routes-to-every-stage-that-exists) | DP-01, DP-07 | | [DP-08](#dp-08--conditional-layer-depth) | DRC-12, 23, 32, 37, 38 |
| [DRC-09](#drc-09--the-process-section-states-one-describable-outcome) | DP-02, DP-08 | | [DP-09](#dp-09--the-deterministic-escape-hatch) | DRC-25 |
| [DRC-10](#drc-10--outputs-are-pinned-and-named) | DP-04 | | [DP-10](#dp-10--sub-workspace-delegation) | DRC-26 |
| [DRC-11](#drc-11--every-inputs-entry-carries-a-purpose) | DP-03 | | [DP-11](#dp-11--verification-as-a-stage) | — declared, no check yet |
| [DRC-12](#drc-12--no-input-declaration-is-a-whole-directory-or-a-bare-glob) | DP-03, DP-08 | | [DP-12](#dp-12--workspace-as-artifact) | DRC-13, 19, 22, 36, 40, 42, 43, 44 |
| [DRC-13](#drc-13--no-contract-names-a-model-or-a-vendor-product) | DP-12 | | | |
| [DRC-14](#drc-14--every-stage-declares-a-review-boundary-or-declares-that-it-has-none) | DP-05 | | | |
| [DRC-15](#drc-15--layer-3-files-pass-the-different-topic-test) | DP-06 | | | |
| [DRC-16](#drc-16--no-single-file-mixes-rules-and-run-content) | DP-06 | | | |
| [DRC-17](#drc-17--every-layer-3-file-is-reachable) | DP-06, DP-07 | | | |
| [DRC-18](#drc-18--no-layer-3-file-is-written-by-a-run) | DP-06 | | | |
| [DRC-19](#drc-19--layer-0-and-layer-1-do-not-hardcode-a-vendor-filename) | DP-12 | | | |
| [DRC-20](#drc-20--every-editable-output-is-text-or-declares-its-projection) | DP-05 | | | |
| [DRC-21](#drc-21--a-cross-language-change-is-decomposed-per-language) | DP-01, DP-02 | | | |
| [DRC-22](#drc-22--stage-names-are-domain-nouns-not-tool-names) | DP-01, DP-12 | | | |
| [DRC-23](#drc-23--vendored-generated-and-build-output-trees-are-excluded-by-default) | DP-03, DP-08 | | | |
| [DRC-24](#drc-24--every-stage-that-reads-source-declares-how-it-locates-it) | DP-03 | | | |
| [DRC-25](#drc-25--deterministic-work-is-not-routed-through-a-model-stage) | DP-09 | | | |
| [DRC-26](#drc-26--delegated-steps-are-declared-not-implied) | DP-10 | | | |
| [DRC-27](#drc-27--non-text-inputs-declare-their-projection) | DP-05 | | | |
| [DRC-28](#drc-28--non-linear-workflows-state-default-order-not-dependency) | DP-01 | | | |
| [DRC-29](#drc-29--the-declared-inputs-contain-no-cycle) | DP-01, DP-04 | | | |
| [DRC-30](#drc-30--every-decision-point-names-who-decides-and-where-it-is-recorded) | DP-02, DP-05 | | | |
| [DRC-31](#drc-31--overlapping-runs-write-to-run-scoped-areas) | DP-04, DP-06 | | | |
| [DRC-32](#drc-32--the-hosts-input-enforcement-is-named) | DP-03, DP-08 | | | |
| [DRC-33](#drc-33--stages-are-idempotent-or-declare-how-they-are-not) | DP-05, DP-04 | | | |
| [DRC-34](#drc-34--layer-3-has-a-named-owner) | DP-06 | | | |
| [DRC-35](#drc-35--multi-writer-operation-states-its-conflict-policy-for-layer-3) | DP-06 | | | |
| [DRC-36](#drc-36--a-stranger-can-answer-what-happened-in-stage-2-from-files) | DP-12, DP-02 | | | |
| [DRC-37](#drc-37--at-ten-or-more-stages-layer-1-is-an-index) | DP-07, DP-08 | | | |
| [DRC-38](#drc-38--every-stages-declared-context-has-a-stated-ceiling) | DP-08 | | | |
| [DRC-39](#drc-39--the-largest-expected-artifact-is-stated-and-its-overflow-path-is-named) | DP-08 | | | |
| [DRC-40](#drc-40--the-workspace-survives-a-copy) | DP-12 | | | |
| [DRC-41](#drc-41--the-hosts-directory-sort-produces-the-intended-order) | DP-01 | | | |
| [DRC-42](#drc-42--the-host-provides-the-filesystem-semantics-folder-as-handoff-assumes) | DP-04, DP-12 | | | |
| [DRC-43](#drc-43--structural-alignment-is-claimed-by-principle-never-as-compliance) | DP-12 | | | |
| [DRC-44](#drc-44--retention-obligations-name-their-owner) | DP-12 | | | |
| [DRC-45](#drc-45--human-intervention-points-name-who-may-intervene) | DP-05, DP-12 | | | |
| [DRC-46](#drc-46--every-declared-input-resolves-at-the-moment-the-stage-runs) | DP-03 | | | |
| [DRC-47](#drc-47--declared-inputs-are-actually-enforced) | DP-03, DP-08 | | | |
| [DRC-48](#drc-48--delivered-context-per-stage-is-within-the-budget) | DP-08 | | | |
| [DRC-49](#drc-49--each-output-records-where-it-came-from) | DP-05, DP-12 | | | |
| [DRC-50](#drc-50--a-mid-pipeline-edit-invalidates-exactly-the-downstream-stages-that-read-it) | DP-04 | | | |

Two rows in that table are deliberate and are the honest limits of this file:

- **[DP-11](#dp-11--verification-as-a-stage) has no enforcing check.** It cannot have one
  yet: the pattern is a *proposal* (§6.2), so no shipped workspace declares a `Verify`
  section. The check is written in C3's schema, where `Verify` becomes a real field.
- **[AP-01](00-glossary.md#ap-01--the-god-stage) through
  [AP-11](00-glossary.md#ap-11--builder-output-treated-as-correct-by-construction)** are
  each named by at least one check, and AP-07, AP-08 and AP-09 additionally appear in
  Group E, where the check that would catch them cannot yet run.

### 6.2 Patterns to the paper's five principles

| Pattern | One stage, one job | Plain text as the interface | Layered loading | Every output is an edit surface | Configure the factory |
| --- | --- | --- | --- | --- | --- |
| DP-01 | ● | | ○ | | |
| DP-02 | ● | ● | ○ | | ● |
| DP-03 | | | ● | | |
| DP-04 | ● | ○ | | | |
| DP-05 | | ○ | | ● | |
| DP-06 | | | ● | | ● |
| DP-07 | | | ○ | | ○ |
| DP-08 | ○ | | ● | | |
| DP-09 | ● | ● | | | ○ |
| DP-10 | ● | | ○ | | |
| DP-11 | ● | | ○ | ● | |
| DP-12 | | ● | | ● | ● |

● primary · ○ secondary. No principle is unclaimed, and no pattern serves none. Every
principle has at least one pattern making it structural rather than aspirational, which is
the test §1.1 set for what was missing from the paper.

### 6.3 What this checklist does not check

Recorded so the list is not read as complete coverage of quality.

- **Whether the work is good.** A workspace can be perfectly shaped and produce garbage.
  That is [ICM-109 rubric](00-glossary.md#14-reserved-terms), concern C27.
- **Whether the output is correct.** Out of scope for a design review by definition; the
  paper's own answer is a review boundary and, eventually, the `Verify` section.
- **Whether the model behaves as specified.** [D-08](DEVIATIONS.md#d-08): the protocol is
  model-agnostic, the evidence is one model family. No folder can answer this.
- **Security.** [DRC-44](#drc-44--retention-obligations-name-their-owner) and
  [DRC-45](#drc-45--human-intervention-points-name-who-may-intervene) name obligations and
  owners. They are not access control, and nothing here is. Mechanism is C21; the secret
  indirection is [ICM-099 secret_ref](00-glossary.md#14-reserved-terms).
- **Cost.** [DRC-38](#drc-38--every-stages-declared-context-has-a-stated-ceiling) records
  an intention. Measuring it is C15.

---

## 7. Change log for this file

| Version | Date | Change | Reason |
| --- | --- | --- | --- |
| 2.2.0 | 2026-10-04 | Initial file. 12 patterns, 50 review checks in 7 groups, review record format with three verdicts, coverage in both directions | C2 — Issues #2 |

Terms introduced by this file: none. It introduces no term that is not already in
[00-glossary.md](00-glossary.md), and it claims no new deviation; every qualification it
states resolves to an existing `D-##`. The glossary's §15 identifier space is unchanged by
this entry, and `ICM-106 template`, referenced in [§1.2](#12-what-a-pattern-is-here-and-what-it-is-not),
remains reserved to C22.