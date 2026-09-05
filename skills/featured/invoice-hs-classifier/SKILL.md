---
name: invoice-hs-classifier
description: Reads a commercial invoice, packing list, or proforma invoice (photo or PDF page) attached to the chat, extracts each line item, and suggests the most likely Harmonized Tariff Schedule of the United States (HTSUS) code for it, built on top of the WCO Harmonized System (HS) and its General Rules of Interpretation. Defaults to US import classification. Trigger this skill whenever the user shares an image or PDF of an invoice/packing list and asks for HS codes, HTS codes, tariff codes, Schedule B numbers, customs classification, or "harmonized codes" for the items on it.
---

# Invoice HS Code Classifier

You are helping someone classify the goods on a commercial invoice for customs
purposes. Work directly from the attached image/PDF — do not ask the user to
retype the invoice unless it is unreadable.

## Step 1 — Read the invoice

From the attached page, extract a row for every distinct line item:

- Description exactly as written
- Quantity and unit (pcs, kg, m, etc.)
- Unit price and line total, if shown
- Material / composition, if stated (e.g. "cotton", "stainless steel")
- Country of origin, if stated

If the description is too vague to classify (e.g. "parts", "misc goods",
"accessories"), keep it in the table but flag it — don't invent specifics
that aren't on the invoice or in the conversation.

## Step 2 — Classify each item

Apply the HS General Rules of Interpretation (GRI), in order:

1. Classify by what the item **is** (its objective character — material,
   function, form), not by its brand, its intended market, or how the
   customer plans to use it, unless the wording of a heading requires that.
2. Use GRI 1: find the heading whose text most specifically covers the good.
3. If the good is unfinished/unassembled or a mixture/composite, apply GRI
   2–4 (essential character, most specific description, last heading in
   numerical order as a tie-breaker).
4. Use the chapter quick-reference below to find the right chapter (2-digit)
   first, then reason down to the heading (4-digit) and subheading (6-digit).
5. The 6-digit HS code is the internationally common part. Codes longer than
   6 digits (8–10 digit HTS/CN/ITC-HS lines) are set by each country's own
   tariff schedule, not the HS convention itself. **Default to the United
   States' schedule (HTSUS)** unless the user names a different destination
   country — see Step 3a.

## Step 3 — Chapter quick-reference (HS 2022, Chapters 1–97)

Use this chapter map to narrow by chapter first, then heading and subheading:

- Ch 01 to 05: Live animals and animal products
- Ch 06 to 14: Vegetable products
- Ch 15: Animal or vegetable fats, oils, and waxes
- Ch 16 to 24: Foodstuffs, beverages, and tobacco
- Ch 25 to 27: Mineral products
- Ch 28 to 38: Chemicals and related products
- Ch 39 to 40: Plastics, rubber, and articles thereof
- Ch 41 to 43: Hides, leather, and furskins
- Ch 44 to 46: Wood, cork, and basketware materials
- Ch 47 to 49: Pulp, paper, and printed matter
- Ch 50 to 63: Textiles and textile articles
- Ch 64 to 67: Footwear, headgear, umbrellas, and similar
- Ch 68 to 70: Stone, ceramic, and glass products
- Ch 71: Precious stones, precious metals, jewellery, coin
- Ch 72 to 83: Base metals and articles
- Ch 77: Reserved (unused)
- Ch 84 to 85: Machinery and electrical equipment
- Ch 86 to 89: Transport equipment
- Ch 90 to 92: Precision, medical, optical, clocks, musical instruments
- Ch 93: Arms and ammunition
- Ch 94 to 96: Miscellaneous manufactured articles
- Ch 97: Works of art, collectors' pieces, antiques
- Ch 98 to 99: Domestic special-use provisions (not part of international HS)

## Step 3a — Default to US HTSUS (10-digit)

Unless the invoice or the user names a different destination country, assume
the goods are being imported into the United States and give the fuller
HTSUS classification, not just the HS-6:

- **Digits 1–6** — the international HS subheading from Step 2.
- **Digits 7–8** — the US "legal" tariff rate line under that subheading
  (this is what actually carries the duty rate in the HTSUS).
- **Digits 9–10** — the US statistical suffix, needed on the entry summary
  but not rate-determining.
- Reason to the 8-digit rate line using the same GRI logic; treat the
  10-digit statistical suffix as lower-confidence unless the invoice gives
  enough detail (exact material %, size, etc.) to pick it with confidence.
- Flag, without guessing a number, when a line item is the kind of good that
  commonly carries duties *on top of* the base HTSUS rate — e.g. steel/
  aluminum articles (Ch. 72–76, Section 232), goods of Chinese origin
  (Section 301 lists), or goods that show up often in antidumping/
  countervailing duty (AD/CVD) orders. Tell the user to check the current
  Federal Register notices or CBP's AD/CVD search for the specific item
  rather than stating a rate yourself, since these change frequently.
- If the user says the destination is a different country, switch entirely
  to that country's schedule (or stop at HS-6 if you don't know its
  post-6-digit structure) and say so explicitly.

## Step 4 — Output format

Present a table with one row per line item:

Required columns, in this order:
- #
- Invoice description
- HS-6
- HTSUS-8 (rate line)
- HTSUS-10 (statistical)
- Chapter/heading title
- Confidence
- Why
- Extra-duty flag

- **Confidence**: High / Medium / Low, given per digit-level if it drops off
  (e.g. "High to 6, Medium to 8, Low on the statistical suffix"). Use Low
  whenever the description is generic, the material isn't stated, or the
  item could plausibly fall in more than one heading.
- **Why**: one short sentence pointing to the objective feature that drove
  the classification (material, function, or form).
- **Extra-duty flag**: note if the item is a plausible Section 301 / Section
  232 / AD-CVD candidate per Step 3a — don't state a rate, just flag it.
- After the table, list any items you flagged and ask the specific
  follow-up question needed to narrow them (e.g. "Is the bracket steel or
  aluminum?").
- If the user names a destination other than the US, redo the classification
  against that country's schedule (or stop at HS-6 if unsure of its
  post-6-digit structure) and say so explicitly.

## Guardrails

- Always end with a short note that these are suggested classifications for
  reference, not a binding customs ruling. Before filing, confirm the 8/10
  digit HTSUS line against the official Harmonized Tariff Schedule
  (hts.usitc.gov), check CBP's CROSS database for prior rulings on similar
  goods, and/or use a licensed customs broker — misclassification can carry
  back-duties and penalty consequences under 19 U.S.C. § 1592.
- Never present a Low-confidence guess as if it were certain.
- If the attached file has multiple invoice pages and only one was provided,
  say so and ask whether more pages are coming before finishing the table.
