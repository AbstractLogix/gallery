---
name: invoice-hs-classifier
description: Extract line items from invoice images or PDF pages and suggest HS and HTSUS classifications for each item.
metadata:
  require-secret: true
  require-secret-description: Optional OCR API key (OCR.Space API key). If omitted, tool will parse provided OCR text only.
---

# Invoice HS Code Classifier

Use JavaScript extraction first, then classify.

## Instructions

Call the `run_js` tool.

Use script name `index.html`.

Pass `data` as a JSON string with this schema:
- `destination_country`: Optional string. Default `US`.
- `invoice_image_base64`: Optional string. Base64 image payload.
- `invoice_image_url`: Optional string. Public image URL.
- `invoice_pdf_url`: Optional string. Public single page PDF URL.
- `ocr_text`: Optional string. Pre extracted invoice text if available.
- `hints`: Optional object. Any known context like expected materials, product names, or known country of origin.

The JS tool returns normalized line items and extraction warnings.

Use the JS output as the source of truth for extraction fields, then classify each item using HS rules.

## Classification rules

Classify each line item by objective characteristics:
- material
- function
- form

Apply GRI in order:
1. GRI 1 first, by heading text
2. GRI 2 to 4 when unfinished, mixtures, or composites require it
3. determine chapter, then heading, then HS 6 digit subheading

If destination country is not provided, assume US import and provide:
- HS 6 digit
- HTSUS 8 digit rate line
- HTSUS 10 digit statistical suffix when possible

For HTSUS 10 digit confidence, downgrade when invoice detail is insufficient.

Flag possible extra duty exposure without quoting rates:
- Section 232 candidates for steel and aluminum related goods
- Section 301 candidates for goods of China origin
- possible AD CVD exposure candidates

## Output format

Return one row per line item with these columns:
- #
- Invoice description
- HS 6
- HTSUS 8 rate line
- HTSUS 10 statistical
- Chapter heading title
- Confidence
- Why
- Extra duty flag

After the rows:
- list flagged items
- ask specific follow up questions needed to increase confidence

## Guardrails

Do not invent missing invoice facts.

Keep vague descriptions in output and mark low confidence.

If extraction warns about possible multi page invoice with only one page provided, ask whether more pages are coming.

End with this short compliance note in your own words:
these are suggested classifications for reference only, not a binding customs ruling; verify against HTSUS and CBP ruling sources before filing.
