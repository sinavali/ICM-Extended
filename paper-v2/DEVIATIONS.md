# Deviations — defects and tensions inherited from the verbatim paper

**Purpose.** `../paper/` is preserved unedited. This register records every point where
that paper is internally inconsistent, self-contradictory, or tied to a specific tool
rather than to the method, so that an auditor can always see the difference between the
published record and v2. Nothing here is a criticism of the paper's thesis; every entry is
a place where a careful reader would stop and ask.

**Status values:** `open` — correction owed, owner named · `carried` — recorded, resolved
in v2, paper left alone · `noted` — informational, no action needed.

| Ref | Sev | Concern | Owner |
| --- | --- | --- | --- |
| [D-01](#d-01) | high | §5.3 argues there is no logging layer to build; production traceability needs one | C25 |
| [D-02](#d-02) | high | §3.1 forbids binary formats; §4.3 consumes PDFs | C16 |
| [D-03](#d-03) | high | "Inputs table" is called a table twice and shown as a bullet list | C3 |
| [D-04](#d-04) | medium | §6.2 cites Section 4.4 for a result reported in §4.5 | C27 |
| [D-05](#d-05) | medium | Ten subsection headings are plain paragraphs | restated in v2 |
| [D-06](#d-06) | medium | §3.1 promises "markdown and JSON files"; no JSON is ever named | C3 |
| [D-07](#d-07) | medium | Figure 1 hardcodes `CLAUDE.md` and `CONTEXT.md` for Layers 0–1 in a protocol declared model-agnostic | C3 |
| [D-08](#d-08) | medium | §4.1 asserts model-agnosticism; §4.6 admits single-family testing | C13 |
| [D-09](#d-09) | medium | "Stage sequencing is the folder numbering" — numbering encodes a total order, not a dependency | C23 |
| [D-10](#d-10) | low | Figure 3 caption says monolithic exceeds 40,000; §3.2 says 30,000–50,000 | C7 |
| [D-11](#d-11) | medium | Figure 5 plots an ordinal scale but the caption attributes counts to 33 practitioners | C27 |
| [D-12](#d-12) | low | The method is stated once, in English prose, with no formal definitions anywhere | C1 → carried |

---

## D-01

**Observability is claimed as a free side effect, but no run record exists.**
`../paper/05-discussion.md` §5.3: *"There is no logging layer to build, no dashboard to
configure, no special tooling to inspect pipeline state. You open a folder and read the
files."*

The claim is true and is one of the paper's best arguments. But it is a claim about
**inspection**, and the worklist asks for **accounting** — which model ran, what it cost,
which files it read, in what order, under which version of the contract. Those are
different products, and "you open a folder and read the files" does not produce them: a
run that overwrote its own output twice leaves no trace that it did.

**v2 resolution (C25).** Split the property in two and keep both. *Inspection* stays a
filesystem property and needs no tooling. *Accounting* is a per-run append-only record
that is not derivable from the files, and it gets an explicit artifact. The paper's
argument is not weakened — it is stated precisely, and the thing it does not cover is
named.

---

## D-02

**The plain-text principle forbids binaries; a shipped workspace consumes them.**
`../paper/03-interpretable-context-methodology/3.1-design-principles.md`: *"Stages
communicate through markdown and JSON files. No binary formats, no database connections,
no proprietary serialization."*

`../paper/04-working-implementations/4.3-course-deck-production.md`: *"takes unstructured
source material (**PDFs**, papers, lecture notes, rough outlines)"*.

Both cannot be the rule as written. Either the course-deck workspace breaks the second
principle of the methodology, or "plain text as the interface" was never meant to forbid
inputs — only *inter-stage communication*.

**v2 resolution (C16).** Distinguish **artifact** from **interface**. The interface between
stages stays text. A binary input becomes text by *projection* — an explicit, declared,
reproducible extraction — and the projection is itself a stage artifact that can be
reviewed, diffed and corrected like any other. §4.3 is then consistent with §3.1, and
"no binary formats" is restated as "no undeclared conversion": the paper's actual
principle is auditability, not ASCII.

---

## D-03

**The contract's central field is described as a table and shown as prose.**
`../paper/03-interpretable-context-methodology/3.2-architecture.md`: *"Each stage contract
includes an Inputs **table** that specifies exactly which files from Layers 3 and 4 the
agent should load, and which sections of those files are relevant."*
`../paper/03-interpretable-context-methodology/3.3-stage-contracts-and-handoffs.md`:
*"The Inputs **table** distinguishes between Layer 3 files and Layer 4 files."*

The only worked example in the paper is a bullet list:

```markdown
## Inputs
- Layer 4 (working): ../01_research/output/
- Layer 3 (reference): ../../_config/voice.md
- Layer 3 (reference): references/structure.md
```

There is no table, no column definitions, and no statement of whether a path is
required, optional, or generated. Since the Inputs declaration is the mechanism that
determines everything the model sees, this is the single highest-leverage place to be
vague.

**v2 resolution (C3).** The Inputs section gets a real grammar: mandatory field order,
typed path entries, required/optional/generated qualifiers, glob rules, and a validator
that refuses to run a stage whose declared inputs do not resolve. The paper's phrase
"inputs table" is treated as naming a *structure that the paper did not write down*, which
is why it recurs as Issues #3 and Enhancements #5.

---

## D-04

**A cross-reference points at the wrong section.**
`../paper/06-future-directions.md` §6.2: *"This is the source of the U-shaped intervention
pattern observed in **Section 4.4**."*

The U-shaped pattern is reported in **§4.5** (*Early Practitioner Experience*). §4.4 is
*Building New Workspaces*. There is no U-shape in §4.4.

**v2 resolution (C27).** Corrected wherever v2 restates the finding. `../paper/` keeps the
error.

---

## D-05

**Ten subsection headings are body text.**
In `02.1–2.3`, `5.1–5.4` and `6.1–6.3`, the numbered title is an ordinary paragraph, not
a markdown heading. Consequences: no document outline, no table of contents below section
level, no anchor links, and no way to cite a subsection from another document.

**v2 resolution.** v2 restates §2, §5 and §6 with real headings. `../paper/` keeps the
original form, which is faithful to the arXiv HTML.

---

## D-06

**A file format is promised and never shown.**
`../paper/03-interpretable-context-methodology/3.1-design-principles.md`: *"Stages
communicate through markdown and JSON files."* No JSON file appears anywhere in the paper,
is never named, and has no described schema.

**v2 resolution (C3).** The structured artefact — the workspace manifest — becomes a
specified file with a published schema. See [D-07](#d-07) for the naming problem this
exposes.

---

## D-07

**Figure 1 names specific vendor filenames inside a model-agnostic protocol.**
Figure 1 labels Layer 0 **`CLAUDE.md`** and Layer 1 **`CONTEXT.md`**. §4.1 then declares
ICM *"designed to be model-agnostic… does not depend on any model-specific capability."*

`CLAUDE.md` is one harness's convention, not the method's. As drawn, a reader following the
figure on a non-Anthropic tool creates a workspace that the paper's own portability claim
does not cover.

**v2 resolution (C3).** Layers 0 and 1 get *roles* (global identity file, workspace
routing file) and a naming convention that is a protocol decision rather than a vendor
default, with the Anthropic filenames recorded as one instantiation. This is the clearest
single example of the universality bar failing in the published paper.

---

## D-08

**Model-agnosticism is asserted as a property and admitted as untested.**
`../paper/04-working-implementations/4.1-model-and-environment.md`: *"ICM is designed to be
model-agnostic. The protocol specifies folder structure, file formats, and naming
conventions. It does not depend on any model-specific capability."*

`../paper/04-working-implementations/4.6-threats-to-validity.md`: *"All testing was
conducted using a single model family… Cross-model evaluation is a natural next step."*
§5.4 raises the same question as open.

The paper is honest — it says the equivalence question is empirical and unanswered. The
residual issue is that the *protocol* claim is stronger than the protocol can yet support,
because several of the protocol's own rules are tuned to how one model family handles
long prompts: the 2,000–8,000 token target, the Inputs-scoping convention, and the
one-instruction-per-stage assumption.

**v2 resolution (C13).** Model-specific prompt adapters and cross-model validation, so the
claim becomes testable rather than merely declared.

---

## D-09

**Numbering encodes an order, but the paper calls it sequencing.**
`../paper/03-interpretable-context-methodology/3.2-architecture.md`: *"Stage sequencing is
the folder numbering."* Elsewhere: *"The numbering encodes execution order."*

Folder numbering yields a **total order**. Sequencing in the general case requires a
**partial order** — stage 04 may be able to run before stage 03 finishes if it does not
read stage 03's output. §5.2 concedes that branching is human-decided, so the paper is not
wrong today; the phrasing simply over-generalises and will mislead the moment anyone tries
to parallelise or reorder stages.

**v2 resolution (C23).** Numbering is stated as the *default* order, and an explicit
declared dependency relation becomes the authoritative one. C17 and C29 build on the
declared relation, not on the numbering.

---

## D-10

**The monolithic baseline is quoted at two different magnitudes.**
Figure 3's caption: a monolithic approach produces a context window *"exceeding 40,000
tokens."* §3.2's prose: it *"can easily reach 30,000 to 50,000 tokens."*

A single representative figure that *exceeds* 40,000 sits at the bottom of a range
beginning at 30,000. One of the two is describing a different thing — probably the bar's
total against the per-stage bars' totals, rather than the same quantity measured twice.

**v2 resolution (C7).** Resolved by measurement. The token-efficiency benchmark counts the
actual prompt the method produces instead of quoting a range.

---

## D-11

**Figure 5 cannot be read as the data its caption describes.**
`../paper/04-working-implementations/4.5-early-practitioner-experience.md` reports concrete
numbers: 33 practitioners, 30 reporting a U-shaped intervention pattern, 3 reporting
roughly equal editing. Figure 5's caption repeats *"reported by 33 practitioners"* and
marks the values *"approximate and based on practitioner self-report."*

The figure's axis is an **ordinal five-point scale** — Never, Rarely, Sometimes, Often,
Almost always — with no counts on any bar. It cannot represent the 30/33 split, cannot
show which of the three dissenters sat, and cannot be reconciled with any single
distribution. A reader who treats the bar heights as data will read a precision that does
not exist.

**v2 resolution (C27).** The evaluation harness records counts per stage per respondent,
so a future figure of this kind carries its distribution. Until then the figure is quoted
as an illustration of a shape, never as a measurement.

---

## D-12

**No formal definition exists anywhere in the paper.** *(carried by C1)*

Every load-bearing term — *stage*, *stage contract*, *working artifact*, *reference
material*, *handoff*, *edit surface*, *review gate* — is introduced in passing prose and
never defined. The same words are used with slightly different meanings in different
sections: *Inputs table* (§3.2, §3.3) versus *Inputs section* (§6.2's proposed `Verify`
paragraph); *skill file* as a Layer 3 content type (§3.2) with no definition anywhere.

This is not pedantry. Every other item in the worklist depends on a shared vocabulary —
a schema cannot be written against inconsistent field names, and a validator cannot report
a contract as malformed if the contract has no grammar. **Status: carried**, resolved in
[00-glossary.md](00-glossary.md), which is the formal term register v2 builds against.