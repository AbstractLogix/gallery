---
name: invoice-hs-classifier
description: Reads a commercial invoice, packing list, or proforma invoice (photo or PDF page) attached to the chat, extracts each line item, and suggests the most likely Harmonized Tariff Schedule of the United States (HTSUS) code for it, built on top of the WCO Harmonized System (HS) and its General Rules of Interpretation. Defaults to US import classification. Trigger this skill whenever the user shares an image or PDF of an invoice/packing list and asks for HS codes, HTS codes, tariff codes, Schedule B numbers, customs classification, or "harmonized codes" for the items on it.
license: MIT
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

| Ch | Section covers |
|----|-----------------|
| 01–05 | Live animals & animal products |
| 01 | Live animals |
| 02 | Meat and edible meat offal |
| 03 | Fish, crustaceans, molluscs |
| 04 | Dairy, eggs, honey, edible animal products n.e.s. |
| 05 | Animal products n.e.s. |
| 06–14 | Vegetable products |
| 06 | Live trees/plants, cut flowers |
| 07 | Edible vegetables, roots, tubers |
| 08 | Edible fruit and nuts, citrus/melon peel |
| 09 | Coffee, tea, maté, spices |
| 10 | Cereals |
| 11 | Milling products, malt, starches, gluten |
| 12 | Oil seeds, industrial/medicinal plants, straw, fodder |
| 13 | Lac, gums, resins, vegetable saps/extracts |
| 14 | Vegetable plaiting materials n.e.s. |
| 15 | Animal/vegetable fats, oils, waxes |
| 16–24 | Foodstuffs, beverages, tobacco |
| 16 | Meat/fish/crustacean preparations |
| 17 | Sugars and sugar confectionery |
| 18 | Cocoa and cocoa preparations |
| 19 | Cereal/flour/starch/milk preparations, pastry |
| 20 | Vegetable, fruit, nut preparations |
| 21 | Miscellaneous edible preparations |
| 22 | Beverages, spirits, vinegar |
| 23 | Food-industry residues, animal fodder |
| 24 | Tobacco, tobacco substitutes, nicotine products |
| 25–27 | Mineral products |
| 25 | Salt, sulphur, earths, stone, cement, lime |
| 26 | Ores, slag, ash |
| 27 | Mineral fuels/oils, bituminous substances, waxes |
| 28–38 | Chemicals |
| 28 | Inorganic chemicals, precious/rare-earth compounds |
| 29 | Organic chemicals |
| 30 | Pharmaceutical products |
| 31 | Fertilisers |
| 32 | Dyes, pigments, paints, varnishes, inks |
| 33 | Essential oils, perfumery, cosmetics, toiletries |
| 34 | Soap, waxes, polishes, candles |
| 35 | Albuminoidal substances, glues, enzymes |
| 36 | Explosives, pyrotechnics, matches |
| 37 | Photographic/cinematographic goods |
| 38 | Miscellaneous chemical products |
| 39–40 | Plastics and rubber |
| 39 | Plastics and articles thereof |
| 40 | Rubber and articles thereof |
| 41–43 | Hides, leather, furskins |
| 41 | Raw hides and skins, leather |
| 42 | Leather articles, bags, luggage, saddlery |
| 43 | Furskins, artificial fur |
| 44–46 | Wood and wood products |
| 44 | Wood and articles of wood, charcoal |
| 45 | Cork and articles of cork |
| 46 | Straw/esparto manufactures, basketware |
| 47–49 | Pulp, paper, printed matter |
| 47 | Wood pulp, recovered paper/paperboard |
| 48 | Paper and paperboard, articles thereof |
| 49 | Printed books, newspapers, pictures |
| 50–63 | Textiles and textile articles |
| 50 | Silk |
| 51 | Wool, animal hair, horsehair fabric |
| 52 | Cotton |
| 53 | Other vegetable textile fibres |
| 54 | Man-made filaments |
| 55 | Man-made staple fibres |
| 56 | Wadding, felt, nonwovens, twine, rope |
| 57 | Carpets and textile floor coverings |
| 58 | Special woven fabrics, lace, tapestry, embroidery |
| 59 | Impregnated/coated textile fabrics, industrial textiles |
| 60 | Knitted or crocheted fabrics |
| 61 | Apparel, knitted or crocheted |
| 62 | Apparel, not knitted or crocheted |
| 63 | Other made-up textiles, worn clothing, rags |
| 64–67 | Footwear, headgear, misc. |
| 64 | Footwear, gaiters, parts |
| 65 | Headgear and parts |
| 66 | Umbrellas, walking-sticks, whips |
| 67 | Feathers, artificial flowers, human-hair articles |
| 68–70 | Stone, ceramic, glass |
| 68 | Articles of stone, plaster, cement, mica |
| 69 | Ceramic products |
| 70 | Glass and glassware |
| 71 | Pearls, precious stones/metals, jewellery, coin |
| 72–83 | Base metals and articles |
| 72 | Iron and steel |
| 73 | Articles of iron or steel |
| 74 | Copper and articles thereof |
| 75 | Nickel and articles thereof |
| 76 | Aluminium and articles thereof |
| 77 | Reserved (unused) |
| 78 | Lead and articles thereof |
| 79 | Zinc and articles thereof |
| 80 | Tin and articles thereof |
| 81 | Other base metals, cermets |
| 82 | Tools, cutlery, spoons/forks of base metal |
| 83 | Miscellaneous articles of base metal |
| 84–85 | Machinery and electrical equipment |
| 84 | Machinery, mechanical appliances, boilers, reactors |
| 85 | Electrical machinery, electronics, AV equipment |
| 86–89 | Transport equipment |
| 86 | Railway/tramway rolling stock and fixtures |
| 87 | Vehicles (non-rail) and parts/accessories |
| 88 | Aircraft, spacecraft, and parts |
| 89 | Ships, boats, floating structures |
| 90–92 | Precision instruments, clocks, instruments |
| 90 | Optical, medical, measuring, precision instruments |
| 91 | Clocks and watches |
| 92 | Musical instruments |
| 93 | Arms and ammunition |
| 94–96 | Miscellaneous manufactures |
| 94 | Furniture, bedding, lighting, prefab buildings |
| 95 | Toys, games, sports requisites |
| 96 | Miscellaneous manufactured articles |
| 97 | Works of art, collectors' pieces, antiques |
| 98–99 | Reserved for each country's domestic/special-use provisions (not part of the international HS) |

(Chapter 77 is reserved and unused.)

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

| # | Invoice description | HS-6 | HTSUS-8 (rate line) | HTSUS-10 (statistical) | Chapter/heading title | Confidence | Why | Extra-duty flag |
|---|---|---|---|---|---|---|---|---|

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
