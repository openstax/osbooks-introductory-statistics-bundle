# MathML & Math-Notation Conventions (3e content review)

Rules established while converting/reviewing math notation for the 3e edition.
**Every content review (and any author content added afterward) must follow these.**

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

## 4. Spacing — no NBSP, let MathML space operators
- **Never use a non-breaking space for spacing inside MathML.** This breaks rendering
  ("Entity 'nbsp' not defined"). That means no `&nbsp;`, no `&#160;`, and no literal U+00A0
  inside `<m:math>`.
- **Do not insert whitespace-only spacer elements** like `<m:mtext> </m:mtext>` or `<m:mo> </m:mo>`.
  Let MathML provide natural spacing around operators (`<m:mo>=</m:mo>`, `<m:mo>+</m:mo>`, …).
- If an explicit space is genuinely required, use `<m:mspace>` — but prefer relying on operator spacing.

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
- Run `xmllint --noout` on every changed module (must be well-formed).
- Re-scan for: NBSP inside `<m:math>` (= 0), en dash inside `<m:math>` (= 0),
  **hyphen-minus `<m:mo>-</m:mo>` inside `<m:math>` (= 0)**, non-ordinal `<sup>` (= 0),
  and plain-text math left in prose (= 0).
- When converting, confirm a character-preservation invariant (stripping tags + whitespace, with
  en-dash↔minus normalized, must be unchanged) so no content is lost.

## 8. Web-render spot checks
- When validating in the web reader, **wait several seconds for MathML to finish rendering** before
  judging — early paints can show transient artifacts (e.g. missing mfenced parens) that resolve.
