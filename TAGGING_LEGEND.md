# CNXML Tagging Legend — Introductory / Business Statistics (3e)

Source: OpenStax "Statistics Tagging Legend" (updated 08/30/2023), provided by the author team.
**Every content review must follow these element/tagging conventions.** See also `MATHML_CONVENTIONS.md`
for math-notation specifics (the two together govern markup).

## Metadata / document-level
- **Module title** — set in the module Metadata `title` field (the manuscript `[H1]`).
- **Preface** — `<document class="preface">`.
- **Appendix** — `<document class="appendix">`.
- **Introductory (chapter intro) module** — `<document class="introduction">` and the opening
  `<content class="introduction">`.

## Chapter intro pieces
- **Chapter outline** — no tagging needed; generated from collated module titles.
- **Chapter objectives** (in each Introduction) —
  ```
  <note class="chapter-objectives">
    <title>Chapter Objectives</title>
    <para>After studying this chapter, you will be able to:</para>
    <list><item>objective 1</item> … (2–4 items, no end punctuation)</list>
  </note>
  ```
- **Splash image** (first image in the intro module only) — `<figure class="splash">` with `<media>` + `<caption>`.

## Headings
- Module title = `[H1]` → Metadata title field.
- `[H2]`/`[H3]`/`[H4]` → nested `<section><title>…</title> … </section>`.

## Tables
- Standard: `<table summary="ALT TEXT">` with an **empty `<title/>`**, `<tgroup cols="n">`, `<tbody>`, and a
  `<caption>` only when a caption is needed. **Do not** use `<thead>` if no title row is needed.
- Title row that spans columns: put it as an extra `<thead>` row (`<entry namest=… nameend=…>`), keep `<title/>` empty.
- Vertically merged cells: `<entry morerows="n">`.

## Images / figures (never use width/height constraints)
- Numbered figure: `<figure><title>(optional)</title><media alt="…"><image mime-type="image/png" src="…"/></media><caption>…</caption></figure>`.
- Centered, unnumbered: `<figure class="unnumbered">` (media only, no caption).
- Left-aligned, unnumbered: bare `<media alt="…"><image …/></media>` (no figure).
- Inline image: `<media display="inline" alt="…">`.
- iframe / embedded video or sim: `<media><iframe width="660" height="371.4" src="https://openstax.org/r/…"/></media>` — **not** inside a `<figure>`; use an OpenStax short link.
- Oversized in webview (usually staff, end of cycle): `<figure class="scaled-down">` (60%) or `class="scaled-down-30"` (30%); same classes exist on `<image>`.

## Features (notes)
- Collaborative Exercise — `<note class="statistics collab"><title>Collaborative Exercise</title>…</note>`.
- Try It — `<note class="statistics try"><title>Try It</title><exercise><problem>…</problem><solution class="solutions"><label/>…</solution></exercise></note>`. (`class="solutions"` enables end-of-chapter solution collation. Note: "Try It" is currently a note wrapping an exercise; may shift to a true `<exercise class="statistics try">` later.)
- Stats Lab — `<note class="statistics lab"><title>…</title>…</note>`.
- TI-83/84 calculator — `<note class="statistics calculator">…</note>`.

## End-of-chapter (EOC) collation — order matters (summary first)
- Key Terms / glossary — `<section class="glossary"><title>Key Terms</title><definition><term>…</term><meaning>…</meaning></definition></section>`.
- Chapter Review — `<section class="summary">` (**always first** in EOC order).
- Formula Review — `<section class="formula-review">`.
- Practice — `<section class="practice">` with `<exercise><problem>/<solution>`.
- Homework — `<section class="free-response"><title>HOMEWORK</title>…</section>`.
- Bringing It Together: Homework — `<section class="bring-together-homework">`.
- Bringing It Together: Practice — `<section class="bring-together-exercises">`.
- References — `<section class="references">`.
- **New for 3e:** Stats Labs — `<section class="stats-labs">` (wraps `<note class="statistics lab">`).
- **New for 3e:** Excel Labs — `<section class="excel-labs">`.

## Inline / miscellaneous
- Link (auto-generated "Figure #.#", "Table #.#", "Example #.#") — `<link target-id="id…" document="m…"/>`; use anchor text when linking chapters/modules/sections.
- Key term (bold + index) — `<term>…</term>`.
- Non-bold index term — `<term class="no-emphasis">…</term>`.
- Italic — `<emphasis effect="italics">…</emphasis>`.
- Subscript — `<sub>…</sub>` (e.g. H<sub>2</sub>O).
- Superscript — `<sup>…</sup>` (e.g. C<sup>3</sup>). **Note:** an element with *both* a subscript and a
  superscript (e.g. NH₄⁺) must be set as an **equation object** (MathML).
- Footnote — `<footnote>…</footnote>`.
- Blockquote — `<para><quote>…</quote></para>`.
- Example — `<example><exercise><problem><para>…</para></problem><solution><para>…</para></solution></exercise></example>`.
- Unnumbered equation — `<equation class="unnumbered">…</equation>`.

## Sub/superscript vs MathML — reconciliation (see MATHML_CONVENTIONS.md §2)
- **Inside a math expression** (anything in `<m:math>` — statistics variables, exponents, indices like
  `σ²`, `xᵢ`, `e^{−mx}`, `G₁`): use MathML `<m:msup>` / `<m:msub>`. The whole expression is the "equation object."
- **Outside math, in prose** (typographic/chemical sub-sup, and ordinals like "4ᵗʰ", "90ᵗʰ percentile", "2ⁿᵈ"):
  use plain `<sub>` / `<sup>` per this legend.
- Net: a sub/superscript that is part of a calculation belongs in MathML; a purely typographic one stays `<sub>`/`<sup>`.
