## How does it work?
The universal document converter [`pandoc`](https://pandoc.org/) does all the heavy lifting. For example:

1. `make pdf` (the code under `pdf: ...` in [`Makefile`](./Makefile)) runs `pandoc` which takes as input
    1. the markdown files which contain the writing content: `input/*.md`
    1. a yaml file with metadata: `input/metadata.yml`
    1. a LaTeX template: `style/template.tex`
    1. a LaTeX header: `style/preamble.tex`
    1. a BibTeX file of your references: `input/references.bib`
    1. a csl style file for citations: `style/ref_format.csl`
    1. a bunch of options which change the output e.g. `--number-sections`
1. the output produced is:
    1. the generated pdf: [`output/thesis.pdf`](./output/thesis.pdf)
    1. logs (which contain the `.tex` which was compiled): `pandoc.pdf.log`    

`pandoc` uses the latex template provided to create a `.tex` file, then compiles it. In detail, `pandoc` processes the input files in the following way (the file names in quotes aren't visible to you, but are named for the purpose of understanding):
1. Make replacements within the markdown files `input/*.md` e.g.:
    * references to figures, captions, and sections are handled: `@fig:my_fig` -> `\ref{fig:my_fig}`
    * equations are converted to LaTeX and numbered: `$f(x) = ax^3 + bx^2 + cx + d$ {#eq:my_equation}` -> `\begin{equation}f(x) = ax^3 + bx^2 + cx + d\label{eq:my_equation}\end{equation}`
    * citations are handled: `[@Cousteau1963]` -> `(Cousteau Jacques & Dugan James 1963)`
    * see `input/*.md` for more examples!
1. Create "`body.tex`" by:
    * converting all the `*.md` files **in the order that they were stated** in the `pandoc` call
1. Create "`main.tex`" from `style/template.tex` by running code wrapped in `$` signs. The important things to note are:
    * this populates `style/template.tex` with metadata from `input/metadata.yml` and the arguments from the `pandoc` call
    * "`body.tex`" is pasted in verbatim in place of `$body$`
1. Create "`references.tex`" by converting `./input/references.bib`
1. Concatenate files together to create the final `thesis.tex` = `style/preamble.tex` + "`main.tex`" + "`references.tex`"
1. Compile `thesis.tex` (you can see the logs for this process, and what "`thesis.tex`" would look like in `pandoc.pdf.log`)

## What else?

Some useful points, in a random order:
- if you only care about generating `theis.pdf` you can always fall back on writing LaTeX within the markdown files (but note that `theis.html` and other outputs will not be able to render this)
- the markdown files you write (i.e. your chapters) will be compiled in alphabetical order, so keep the filenames sorted in the order you want them to appear e.g. `01_chapter_1.md`, `02_chapter_2.md`, etc. This is required because of the way we have written `make pdf`. You can change this behaviour by writing a custom `pandoc` command instead of using `make pdf`.
- each chapter must finish with at least one blank line, otherwise the header of the following chapter may not be picked up.
- add two spaces at the end of a line to force a line break.
- the template uses [John Macfarlane's Pandoc](http://johnmacfarlane.net/pandoc/README.html) to generate the output documents. Refer to this page for Markdown formatting guidelines.
- To change the citation style, just overwrite ref_format.csl with the new style. Style files can be obtained from [citationstyles.org/](http://citationstyles.org/)

