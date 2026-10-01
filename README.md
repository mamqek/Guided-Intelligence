# Guided Intelligence thesis - Overleaf project

This directory follows the official UvA MSc Software Engineering LaTeX template.
Upload the accompanying ZIP to Overleaf and set `main.tex` as the main document.
In Overleaf, open **Menu**, set **Compiler** to **LuaLaTeX**, and recompile.
Bibliography processing uses Biber.

Current manuscript state:

- Chapters 1--9 contain the converted thesis manuscript.
- The implemented intent-contract registry is included as an appendix. The complete required-evidence audit is submitted separately as a companion file so that its 700 run-level judgements do not expand the main thesis PDF.
- Author, examiner, and reviewer remain placeholders. Empty abstract,
  declaration, acknowledgements, and figure-list pages are omitted until they
  contain thesis content.

The `source-markdown` directory contains a snapshot of the Markdown manuscript and thesis plan used for this export. Re-run `thesis/tools/build_overleaf_project.py` from the repository when a fresh export is needed.
