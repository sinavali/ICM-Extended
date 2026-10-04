# ICM — Interpretable Context Methodology

A reference copy of the paper
**“Interpretable Context Methodology: Folder Structure as Agent Architecture”**
(Jake Van Clief and David McDermott, Eduba / University of Edinburgh), in Markdown
so it can be read, reviewed and cited without the journal-style layout.

- Paper: [arXiv:2603.16021v2](https://arxiv.org/html/2603.16021v2)
- Upstream protocol and implementation:
  [RinDig/Interpretable-Context-Methodology](https://github.com/RinDig/Interpretable-Context-Methodology-ICM-)

## The idea in brief

ICM replaces multi-agent framework orchestration with filesystem structure. A
workspace is simply a folder:

- **Numbered folders are stages** — `01_research/`, `02_script/`, `03_production/`
- **Plain markdown files carry the prompts and context** that tell a single agent what
  role to play at each step
- **Local scripts do the mechanical work** that needs no AI at all

Each stage reads a defined input, writes a defined output, and stops for human review.
The agent picks up whatever the human left in the previous `output/` folder, so the
intermediate state is always a file you can open, edit and diff.

Context reaches the agent in five layers, and only the ones the current stage needs are
loaded:

| Layer | Holds | Changes between runs |
| --- | --- | --- |
| 0 | Global identity — which workspace this is and what it contains | no |
| 1 | Workspace-level task routing | no |
| 2 | The stage contract — inputs, process, outputs | no |
| 3 | Reference material, “the factory”: voice rules, conventions, design systems | no |
| 4 | Working artifacts, “the product”: this run's research, drafts, specifications | yes |

Because a stage only loads its own files, it typically sees 2,000–8,000 tokens, where a
monolithic prompt carrying every instruction and every prior output reaches 30,000–50,000
tokens — and the paper argues the extra tokens are mostly irrelevant context, which
degrades the model. Layer 3 is separated from Layer 4 so the model can treat persistent
rules as constraints and per-run material as input, rather than sorting them out itself.

## What is in this repository

| Path | What it is |
| --- | --- |
| [`original_paper.md`](original_paper.md) | The complete article in one file — the reference copy |
| [`paper/`](paper/) | The same text split by section, with an [index and reading paths](paper/README.md) |
| [`figures/`](figures/) | The paper's five figures, extracted as SVG |
| [`original_paper.html`](original_paper.html) | The original arXiv page the Markdown was converted from |

This repository currently holds the paper and its study corpus.

## Reading it

Start at [`paper/README.md`](paper/README.md): it lists every file with a word count and
suggests reading paths — the argument in one pass, building a workspace yourself, a
sceptical read, and where the work goes next.

Every file is self-contained: it opens with its section title as a heading and closes
with the footnotes it cites, so any single file can be read on its own. The two longest
sections, 3 and 4, are split further by subsection.

## Conversion notes

The Markdown was converted from the arXiv HTML, with the rich-text structure mapped to
native Markdown — headings, two real Markdown tables, a fenced code block, footnotes and
relative figure links.

It is verified lossless: every paragraph, table cell, caption and footnote in the source
HTML appears in the Markdown, the heading sequence matches section for section, and the
one code listing is byte-identical to the original.

Left out, because they add nothing to the argument: the reference list, the keyword list
and the ACM CCS classifications. In-text citations such as `(25)` keep the paper's own
numbering, so use the arXiv page for the reference list.

The figures are SVG. Some of their labels are drawn with `foreignObject`, which some
Markdown renderers — GitHub included — omit; open an SVG directly to see the complete
diagram.

## Attribution

The paper and the ICM protocol are by Jake Van Clief and David McDermott. The protocol is
released under the MIT license. This repository is a Markdown rendering of that paper,
kept for reading and review.