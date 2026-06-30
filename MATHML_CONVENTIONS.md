# MathML & Math-Notation Conventions (3e content review)

Rules established while converting/reviewing math notation for the 3e edition.
**Every content review (and any author content added afterward) must follow these.**

> For non-math element tagging (figures, tables, notes/features, EOC sections, links, sub/sup, etc.),
> see **`TAGGING_LEGEND.md`** (the author team's CNXML tagging legend). The two docs together govern markup;
> in particular, math sub/superscripts use MathML `<m:msup>`/`<m:msub>` while purely typographic ones use
> `<sub>`/`<sup>` (see §2 here and the reconciliation note in the legend).

## 1. All math goes in MathML
- Inline math must be MathML (`<m:math>…</m:math>`), not plain text and not `<emphasis effect="italics">`.
  - Single variables (`x`, `X`, `n`, `σ`, …) → `<m:mi>`; numbers → `<m:mn>`; operators → `<m:mo>`.
  - This includes math inside table cells, captions, and list items.
- `<emphasis effect="italics">` is for *prose* emphasis only (defined terms, book/journal titles,
  instruction lines like "Use the following information…"). Never use it to italicize a variable.
- **Do not interleave MathML with plain-text math inside one expression.** A whole equation/inequality
  must live in a single `<m:math>` — not split like `0.3 = (<m:math>k−1.5</m:math>) (0.4)` (where the
  `0.3 = (` and `) (0.4)` are plain text). Mixed fragments render with inconsistent font/weight
  (e.g. a bold `<emphasis>` wrapper applies to the plain parts but not the MathML). Put the entire
  expression in one `<m:math>`; if it's meant to be bold, that's a separate styling decision applied
  to the whole element — don't leave half of it as bold text and half as MathML.
- **Probability / distribution notation with worded arguments.** Expressions like `P(event description)`,
  `P(A AND B)`, `P(x | y)`, `X ~ B(n, p)` must live entirely in one `<m:math>` — not `<m:math>P</m:math>(event)`
  with the argument in prose. Inside:
  - `P`, `X`, single-letter events (`A`, `B`, `F`, `M`) → `<m:mi>`; `(` `)` `=` `+` `~` `|` `<` `>` `≤` `≥` `%` → `<m:mo>`; numbers → `<m:mn>`.
  - A **descriptive event name is one whole-phrase `<m:mtext>` chunk** — `<m:mtext>Person is in their twenties</m:mtext>`,
    not a separate tag per letter/word. Keep logical words (`AND`/`OR`/`NOT`/`GIVEN`) inside that text run
    (`<m:mtext>type O OR Rh-</m:mtext>`); only a literal conditional bar becomes `<m:mo>|</m:mo>`.
  - Fill-in blanks (`___`) inside an argument are `<m:mtext>___</m:mtext>`.
  - A range like "1–33" stays an en dash inside `<m:mtext>` (it's prose text, not a minus operator).
- **Never spell a word out as separate single-letter `<m:mi>` tokens.** Logical words and any
  multi-letter label must be ONE token, not one `<m:mi>` per letter. The worst offender seen in 3e was
  the word "AND"/"OR" encoded as `<m:mi>A</m:mi><m:mi>N</m:mi><m:mi>D</m:mi>` /
  `<m:mi>O</m:mi><m:mi>R</m:mi>` (often with whitespace/newlines between the tags, so a naive
  contiguous grep misses them). That renders as garbled *italic* letters with invisible-times between
  them — e.g. `P(A AND B)` shows as "A·A·N·D·B". The symptom in the reader is an italic run where you
  expect upright spaced text.
  - Fix: logical operators → spaced `<m:mtext> AND </m:mtext>`, `<m:mtext> OR </m:mtext>`,
    `<m:mtext> NOR </m:mtext>`, `<m:mtext> NOT </m:mtext>`, `<m:mtext> GIVEN </m:mtext>` (keep the
    surrounding spaces inside the mtext so it doesn't glue to the operands).
  - A genuine multi-letter *symbol* (e.g. `RR`, `BR`, `IQR`, `np`) is a single `<m:mi>RR</m:mi>` —
    again never one tag per letter.
- **Bold math:** when MathML sits inside a bold `<emphasis>` (i.e. `<emphasis>` with no `effect`, or
  `effect="bold"`), wrap the math's inner content in `<m:mstyle mathvariant="bold">…</m:mstyle>` so it
  renders bold to match the author's intent. (HTML bold does not propagate into a `<m:math>` element.)
  Keep the `<emphasis>` wrapper; do **not** try to bold by replacing/removing the emphasis with a
  regex that spans across `</emphasis>` — that corrupts structure.

## 2. Superscripts
- A **math** superscript (an exponent) must be `<m:msup>`, e.g. `e^{−mx}`, `σ²`, `p^x`, `(x − x̄)²`.
- Keep plain `<sup>` **only** for non-math superscripts — ordinals such as "4ᵗʰ", "90ᵗʰ percentile",
  "2ⁿᵈ". (`th`/`st`/`nd`/`rd`.)

## 3. Minus sign — never an en dash *or* a hyphen
- The math operator "minus" must be **U+2212 MINUS SIGN (`−`)**, written `<m:mo>−</m:mo>`.
- Do **not** use the en dash `–` (U+2013), the hyphen-minus `-` (U+002D), or `&#8211;` as a minus
  inside `<m:math>`. (En dashes/hyphens remain fine in ordinary prose — ranges, compound words, etc.)
- Note the hyphen form `<m:mo>-</m:mo>` *renders* as a minus, so it's an invisible defect — it won't
  look wrong in the reader, but it's still incorrect for accessibility/semantics. Scan for it explicitly
  (see §7); don't rely on visual checks to catch it.

## 4. Spacing inside MathML
- **Never use the *named* entity `&nbsp;`** inside `<m:math>` — it isn't defined without a DTD and breaks
  parsing ("Entity 'nbsp' not defined"). When you need a non-breaking space, use the **numeric** form
  `&#160;` (valid XML, always safe).
- **Operators space themselves — don't add manual spaces around `<m:mo>`.** `<m:mo>=</m:mo>`, `<m:mo>+</m:mo>`,
  `<m:mo>−</m:mo>` already render with correct spacing. Do **not** insert whitespace-only spacer elements
  like `<m:mtext> </m:mtext>` or `<m:mo> </m:mo>`.
- **Text next to a symbol needs a protected space — the renderer trims regular spaces at `<m:mtext>` edges.**
  The rex-web/MathJax renderer (which matches production) **collapses a leading/trailing ordinary space
  inside `<m:mtext>`**, so `…<m:mi>X</m:mi><m:mtext> AND </m:mtext><m:mi>Y</m:mi>…` renders glued as
  "XANDY", and `<m:mi>R</m:mi><m:mtext> on 1st draw</m:mtext>` renders "Ron 1st draw".
  - Fix: put `&#160;` at the mtext **edge that touches a symbol** — `<m:mtext>&#160;AND&#160;</m:mtext>`,
    `<m:mtext>&#160;on 1st draw AND&#160;</m:mtext>`, `<m:mtext>&#160;on 2nd draw</m:mtext>`.
  - **Internal** spaces inside the mtext (between words) are fine as ordinary spaces — only the edges collapse.
  - Don't add `&#160;` at the very start/end of a whole `<m:math>` (no symbol there to glue to).
  - This is invisible in a source diff but very visible in the reader; verify with the width test (§7) or by eye.

## 5. Inequalities / relational operators
- `<`, `>`, `≤`, `≥` inside math must be `<m:mo>` (with `<` written as `&lt;`, `>` as a literal `>` is
  fine in text but should be `<m:mo>&gt;</m:mo>` inside MathML). Don't leave `P(x > 7)` with the
  `> 7)` part sitting in plain text outside the `<m:math>`.

## 6. Things that are intentional (don't "fix")
- `<m:mfenced>` is valid and renders correctly in the production reader; it does **not** need to be
  converted to explicit `<m:mo>(</m:mo>…<m:mo>)</m:mo>`. (A transient archive-preview rendering glitch
  can briefly drop mfenced parens before the page finishes rendering — not a content bug.)
- Multi-letter abbreviations used as labels (e.g. *Min*, *Med*, *Max*, *Median*) are left as text/emphasis,
  not converted to `<m:mi>` — they are labels, not operands. Genuine multi-letter symbols used in
  calculations (`IQR`, `RF`, `np`, outcome codes like `HH`, `Exp`, `ln`) *are* MathML.

## 7. Verification after any math edit
- Run `xmllint --noout` on every changed module (must be well-formed). This is the one check that
  reliably catches structural corruption (e.g. a regex that merged across `</emphasis>`); the
  char-preservation invariant below does **not** catch tag-structure damage.
- Re-scan each changed chapter for all of these (target count = 0):
  - the **named** entity `&nbsp;` or whitespace-only spacer elements (`<m:mo> </m:mo>`, `<m:mtext> </m:mtext>`)
    inside `<m:math>`. (Numeric `&#160;` is *expected* at mtext edges per §4 — do **not** flag it.)
  - en dash `–` / `&#8211;` used as a minus inside `<m:math>` (en dash inside `<m:mtext>` ranges is OK).
  - hyphen-minus `<m:mo>-</m:mo>` inside `<m:math>` (renders fine, invisible defect — scan, don't eyeball).
  - non-ordinal `<sup>` (math superscripts that should be `<m:msup>`).
  - plain-text math left in prose — tell-tale `</m:math>(` (a `(` immediately after a math close) and
    `<m:math>P</m:math>(` (probability notation split out of its math).
  - **words spelled as single-letter `<m:mi>` runs** — whitespace-tolerant scan, e.g.
    `<m:mi>A</m:mi>\s*<m:mi>N</m:mi>\s*<m:mi>D</m:mi>` (AND), `<m:mi>O</m:mi>\s*<m:mi>R</m:mi>` (OR),
    and the same idea for NOT/NOR/GIVEN.
  - **`<m:mtext>` with a regular (non-NBSP) leading/trailing space adjacent to a symbol** — these render
    glued (see §4); the edge space should be `&#160;`.
- When converting, confirm a character-preservation invariant (stripping tags + whitespace + `&#160;`,
  with en-dash↔hyphen↔minus normalized, must be unchanged) so no content is lost — and ideally diff the
  tags-stripped text against `git show HEAD:<path>` so the only changes are markup, not visible text.
- **Width test for mtext spacing:** to confirm a space actually renders (not just that it's in the source),
  in the rendered page compare an `<mjx-mtext>`'s box width to its text measured with vs without edge spaces
  (canvas `measureText`); box ≈ trimmed-width means the space collapsed. (NBSP-vs-regular A/B test: a
  `<mtext>&#160;AND&#160;</mtext>` typesets visibly wider than `<mtext> AND </mtext>`.)

## 8. Web-render spot checks
- **The rex-web preview's *final* rendered state matches production exactly.** Once the page has fully
  rendered, what you see is what ships — a persistent rendering problem is real, not a "preview-only"
  artifact, and must be fixed. (Only mid-load paints are transient — see next bullet.)
- **Wait several seconds for MathML to finish rendering** before judging — early paints can show
  transient artifacts (e.g. momentarily missing mfenced parens) that resolve once layout settles.
- A baked archive/preview snapshot only reflects edits made **before** it was built — fixes made after
  a bake won't appear until the next rebuild. Don't re-flag something already fixed in source.
- A freshly baked preview often **404s on the first visit** to a page; refresh/re-navigate and it loads.
  (The host can also throw transient "Application Error" pages — wait and retry.)
- **Don't judge spacing from `get_page_text`** — text extraction collapses whitespace, so "A AND B"
  and a glued "AANDB" look the same in the dump. Use a screenshot/zoom or the §7 width test. (Regular
  edge spaces in mtext genuinely DO render glued; `&#160;` is what makes them show.)
