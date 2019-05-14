# Convert Document with Pandoc

[Pandoc](https://pandoc.org/index.html) is a universal document converter, such as PDF, Docx, and others.

## Install

Download from the [Windows package installer](https://github.com/jgm/pandoc/releases/tag/2.7.2)

Alternatively, you can install pandoc using [Chocolatey](https://chocolatey.org/):

```bat
choco install pandoc
```

Chocolatey can also install other software that integrates with Pandoc. 
For example, to install rsvg-convert (from librsvg, covering formats without SVG support), Python (to use Pandoc filters), and MiKTeX (to typeset PDFs with LaTeX):

```bat
choco install rsvg-convert python miktex
```

## Example Usages

Convert Markdown to Docx

```bat
pandoc -s -o output.docx input.md
```

Convert Markdown to PDF

```bat
pandoc -s -o output.pdf input.md
```

## Reference

- [Installing pandoc](https://pandoc.org/installing.html)
- [Pandoc Demo](https://pandoc.org/demos.html)
- [Pandoc User Guide](https://pandoc.org/MANUAL.html#)
- [Install chocolatey](https://chocolatey.org/install)
