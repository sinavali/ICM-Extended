# Interpretable Context Methodology: Folder Structure as Agent Architecture

**Jake Van Clief, David McDermott**  
theceo@eduba.io  
Affiliation: Eduba, University of Edinburgh, Palm Coast, Florida, USA — [arXiv:2603.16021v2](https://arxiv.org/html/2603.16021v2)

The full article is preserved verbatim in [`../original_paper.md`](../original_paper.md),
with the original page saved as [`../original_paper.html`](../original_paper.html).
The files below are that text split by section for reading and review — no wording was
changed, only the file boundaries.

## Contents

- [Abstract](00-abstract.md) — 171 words
- [1. Introduction](01-introduction.md) — 638 words
- [2. Background and Related Work](02-background-and-related-work.md) — 1376 words
- [3. Interpretable Context Methodology (overview)](03-interpretable-context-methodology/README.md) — 23 words
- [3.1. Design Principles](03-interpretable-context-methodology/3.1-design-principles.md) — 354 words
- [3.2. Architecture](03-interpretable-context-methodology/3.2-architecture.md) — 1246 words
- [3.3. Stage Contracts and Handoffs](03-interpretable-context-methodology/3.3-stage-contracts-and-handoffs.md) — 479 words
- [3.4. Portability and Reproducibility](03-interpretable-context-methodology/3.4-portability-and-reproducibility.md) — 189 words
- [4. Working Implementations (overview)](04-working-implementations/README.md) — 65 words
- [4.1. Model and Environment](04-working-implementations/4.1-model-and-environment.md) — 225 words
- [4.2. Script-to-Animation Pipeline](04-working-implementations/4.2-script-to-animation-pipeline.md) — 302 words
- [4.3. Course Deck Production](04-working-implementations/4.3-course-deck-production.md) — 109 words
- [4.4. Building New Workspaces](04-working-implementations/4.4-building-new-workspaces.md) — 256 words
- [4.5. Early Practitioner Experience](04-working-implementations/4.5-early-practitioner-experience.md) — 733 words
- [4.6. Threats to Validity](04-working-implementations/4.6-threats-to-validity.md) — 273 words
- [5. Discussion](05-discussion.md) — 1286 words
- [6. Future Directions: Compilation, Debugging, and Source Integrity](06-future-directions.md) — 1587 words
- [7. Conclusion](07-conclusion.md) — 194 words

## How the files are organised

- One file per top-level section. The two longest sections (§3, §4) are split by
  subsection into a folder, because they run to several pages each; the folder
  `README.md` holds that section's opening text and links to its subsections.
- Each file opens with its section title as a heading and closes with the footnotes it
  cites, so any single file can be read on its own.
- Figures live in [`../figures/`](../figures) and are referenced with relative paths,
  so the files render correctly wherever this folder is checked out.
- Citations such as `(25)` still use the paper's own reference numbering. The
  bibliography was left out of the source conversion, so those numbers have no target
  here; use the arXiv page for the reference list.

## Suggested reading paths

- **The argument in one pass** — [§1 Introduction](01-introduction.md), [§3.1 Design principles](03-interpretable-context-methodology/3.1-design-principles.md), [§3.2 Architecture](03-interpretable-context-methodology/3.2-architecture.md), [§7 Conclusion](07-conclusion.md)
- **Building one yourself** — [§3.2 Architecture](03-interpretable-context-methodology/3.2-architecture.md), [§3.3 Stage contracts and handoffs](03-interpretable-context-methodology/3.3-stage-contracts-and-handoffs.md), [§4 Working implementations](04-working-implementations/README.md)
- **Sceptical read** — [§4.5 Early practitioner experience](04-working-implementations/4.5-early-practitioner-experience.md), [§4.6 Threats to validity](04-working-implementations/4.6-threats-to-validity.md), [§5 Discussion](05-discussion.md)
- **Where it goes next** — [§6 Future directions](06-future-directions.md)
