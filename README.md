# Highlighting Author Names in Bibliographies

This repo contains demos to test highlighting author names in 
bibliographies. There are two demos:

1. `demo_selected_refs.qmd` cites selected refs to include in 
the bibliography for each section (the citations themselves are not shown since 
they are added in divs with id `refs-<bib_label>`).
2. `demo_all_refs.qmd` all refs from the bibliography corresponding to a 
section are included, without having to cite individual refs (thanks to @dicook 
for the tip).

The output is set to GitHub markdown, so it can be viewed in the browser, 
however the demos also work for HTML, docx and PDF (other formats not tested).

Current capabilities
 - Can create multiple bibliographies in sections
 - Can highlight in bold a given set of author names

Current limitations:
 - Only works with author-date CSL styles 
 ([hint on how to fix](https://stackoverflow.com/a/66075292)).
 - Only works with CSL styles that format author names exactly as 
 "<family name>, <initial>". The default Quarto style will only work for the 
 first author, which is formatted as  "<family name>, <given name>": later 
 authors are formatted as "<given name> <family name>" so are not detected.
 - Given name must match style used in bibliography, e.g. given name "D." vs 
 given name "Dianne".

## Multible bibliographies

This is implemented using the [multibib Quarto extension](https://github.com/pandoc-ext/multibib). After installing the extension, the minimal requirements for use are

```md
---
bibliography:
  biblio1: bib1.bib
  biblio2: bib2.bib
filters:
  - multibib
citeproc: false
validate-yaml: false
---

# Bib1

::: {#refs-biblio1}
@Majumder_2025
:::

# Bib2

::: {#refs-biblio2}
@Goodwin_2024
:::
```

Note the suffix of the `refs` labels matches the options of the `bibliography` 
field, not the names of the bibliographies!

To cite all references in each bib add

```yaml
nocite: |
  @*
```

to the YAML. Note this applies to all bibliographies, so you have to either 
select specific references each time or select all references in each 
bibliography.

## Bold author names

This uses the `bold-author` Lua filter given in this [Stackoverflow post](https://stackoverflow.com/a/76429867). 

It only works after a filter like multibib, since this is designed to replace 
the standard use of citeproc by Quarto. By default Lua filters added in Quarto 
are incoporated into the "main" Lua filter which is run before citeproc, so 
you  can't operate on the bibliographic entries. But multibib runs citeproc 
for each bibliography, so by using this filter first (and setting 
`citeproc:false`), we can write a Lua filter to modify the bibliographic 
entries.

To use the `bold-author` filter, we add the filter to the list, turn off 
citeproc and specify the family and given name (initial) for each name we want 
to format in bold. Given the limitations noted at the start, we need to use 
a CSL like `american-geophysical-union.csl` that only uses initials. So the 
extra YAML required may be something like:

```yaml
---
csl: american-geophysical-union.csl
filters:
  - multibib
  - bold-author.lua
bold-auth-name:
  - family: Cook
    given: D.
  - family: Goodwin
    given: S.
---
```

No further change is required to the body of the document and the file is 
rendered as usual.