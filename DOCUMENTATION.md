# Resume format and local build

This file documents the layout of `samson-olusegun.tex` and how to compile it on this machine. The upstream `README.md` describes the original autoCV template and targets `cv.tex`; this resume uses `samson-olusegun.tex`, so `make` does not apply. Do not change the `Makefile`.

## Layout standard

The document follows the single-column, ATS-safe LaTeX resume pattern popularized by [jakegut/resume](https://github.com/jakegut/resume):

- One column. No sidebars, no multi-column tables for content, no icon glyphs in text-bearing elements.
- Small-caps section titles with a horizontal rule (`titlesec`).
- Ragged-right text (`\raggedright`). No justification.
- `\input{glyphtounicode}` and `\pdfgentounicode=1` make the PDF text machine-readable, so ATS parsers extract exact characters.
- Two pages maximum, A4, 11pt.

## Job entry format

Each role uses the `joblong` environment. It takes three arguments, in this order:

```latex
\begin{joblong}{Role title}{Mon YYYY -- Mon YYYY}{Company, Country (Work mode)}
\item Action verb + what you did + measurable outcome.
\item Next bullet.
\end{joblong}
```

- Argument 1: bold role title, left-aligned.
- Argument 2: dates, right-aligned on the same line.
- Argument 3: italic company and location line below the title.
- A `tabular*` with `\extracolsep{\fill}` aligns the dates to the right margin. This works under `\raggedright`, where `\hfill` does not reach the right edge.
- `\needspace{6\baselineskip}` keeps a job header with its first bullet across page breaks.
- `\parskip=0pt` inside the environment keeps the gap between the title lines and the first bullet tight. Normal paragraph spacing resumes after the entry.

Rules for bullets:

- Start with an action verb. State the outcome with a number where one exists.
- One claim per bullet. Do not repeat the same claim in two bullets within one role.
- Keep role keywords in the bullets; the SKILLS section restates them as a keyword index.

## Skills section

The SKILLS section is a `tabularx` with a bold label column and one row per category:

```latex
\textbf{Mobile} & Kotlin / React Native / Expo / EAS Update (OTA) / Fastlane \\
```

Values use slashes in this file. Keep each category on one rendered row where possible; wrapped rows are acceptable.

## Sections and order

TECHNICAL EXPERIENCE, OTHER EXPERIENCE, EDUCATION, SKILLS, INTEREST, LANGUAGES, CERTIFICATE. Roles inside each section run newest first.

## Build locally

The build uses a one-off Nix shell. Nothing is installed into the system profile; packages come from the Nix store and the NixOS cache.

```sh
cd ~/projects/personal/my-resume
nix-shell -p '(texlive.combine { inherit (texlive) scheme-medium enumitem fontawesome5 biblatex logreq xstring supertabular titlesec multirow needspace; })' biber poppler-utils --run "latexmk -pdf samson-olusegun.tex"
```

Notes:

- The first run downloads a few hundred megabytes. Later runs are fast and incremental.
- `latexmk -C` removes intermediate files. `latexmk -g -pdf ...` forces a full rebuild when a stale cache hides an error.
- The document needs `enumitem`, `titlesec`, `needspace`, `supertabular`, `fontawesome5`, and `biblatex` on top of `scheme-medium`. If compilation fails with `File 'X.sty' not found`, add package `X` to the `texlive.combine` list.
- `pdfinfo samson-olusegun.pdf | grep Pages` prints the page count.
- `pdftoppm -png -r 60 samson-olusegun.pdf /tmp/resume-page` renders pages to images for layout checks. Use `-r 150` with `-x -y -W -H` to crop and zoom into a region.

## Layout verification checklist

After any edit, rebuild and check:

1. Page count is 2.
2. No job header sits alone at the bottom of a page.
3. No large empty band at the bottom of page 1.
4. Section rules do not touch the first line below them.
5. Contact links stay on one line.

## References

External sources used for the layout rules in this file:

- [jakegut/resume](https://github.com/jakegut/resume) -- the canonical single-column ATS LaTeX template this layout follows.
- [Jake's Resume on Overleaf](https://www.overleaf.com/latex/templates/jakes-resume/syzfjbzwjncs) -- the same template, editable online.
- [sb2nov/resume](https://github.com/sb2nov/resume) -- the older classic template that `jakegut/resume` is based on.
- [engineering-resumes wiki](https://github.com/engineering-resumes/engineering-resumes/wiki) -- community rules for resume layout and bullet content.
