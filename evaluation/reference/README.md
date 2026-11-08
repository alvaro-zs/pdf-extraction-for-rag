# Manually checked extraction references

Use this folder to record what is actually visible in the PDFs, using the source page as the authority rather than an extractor's answer. This is an annotation template, not a completed reference set or an implemented scoring system. The format is provisional while the benchmark design is being discussed.

The current scope is Subproject 1: convert all three complete PDFs and inspect their timings and outputs. You do not need to complete any reference annotations before those conversions. These references belong to Subproject 2, which will assess Subproject 1's outputs. Quality scores will follow when reviewed references are available; timing results and output counts alone do not establish accuracy.

## Where to save your work

Copy [page.template.json](./page.template.json) into this same folder once per annotated page. Leave the template unchanged.

Name each copy `<PDF filename without extension>__p<physical page number with three digits>.json`, for example `CarteiraTOP10_XP_dividendos__p001.json`.

Set `source_pdf` to the exact filename under [Sources/To_test](../../Sources/To_test/), including `.pdf`. Use the physical PDF page number starting at 1, even if the page has a different printed number. Rotate the view as needed to read sideways pages.

Keep `annotation_status` as `draft` while editing. After checking every included item directly against the PDF, set it to `reviewed` and enter `reviewed_by` and `reviewed_on` (`YYYY-MM-DD`). This status records self-review; it does not imply an independent second reviewer. Draft files and the template must be excluded from future scoring.

## What to annotate

When the quality-evaluation phase begins, agree on a sample covering prose, tables, charts, and both straightforward and difficult layouts. The final sample size and selection rules are still to be decided. The earlier suggestion of a six-page pilot is not an agreed requirement, and full-document conversion does not require transcribing all 51 pages.

`coverage` is `selected_regions` by default. It means omitted content is **unannotated**, not absent. Use `complete_page` only after annotating all content within the eventual agreed scope. Partial references can measure fidelity within selected targets; they cannot establish whole-page completeness or whole-page precision.

Delete unused example objects. For example, if you annotate no table, set `tables` to `[]`; this does not assert that the page contains no tables. Give each included region a distinct ID and a clear `locator`, such as `upper-left paragraph below the heading` or `bottom-right allocation chart`.

### Text

Transcribe each selected paragraph or heading exactly into `exact_text`. Preserve accents, punctuation, numerical separators, percentages, and currency symbols. Do not correct apparent source errors, translate, summarize, or complete missing text. Keep text blocks in their intended reading order.

JSON strings encode a line break as `\n`. Escape double quotes as `\"`. For example:

```json
{
  "id": "text_01",
  "locator": "First paragraph beneath the main heading",
  "exact_text": "Copy the source text here.\nCopy the next visible line here.",
  "notes": ""
}
```

Whitespace normalization for scoring will be decided separately; preserve the source at annotation time.

### Tables

Enter the table as `rows`, with one array per visible row and one string per cell, including headers. Preserve displayed formatting: `"1.234,56"`, `"12,5%"`, and `"R$ 100"` remain strings. Use `""` for a genuinely empty cell. Keep the number of cells consistent across rows.

Set `header_row_count` to the number of header rows, or `0` if there are none. Put any visible caption in `caption`; use `null` if none exists. Record relevant footnotes in `notes`, transcribing them without interpretation.

For a merged cell, place its text at the upper-left position and leave the covered cells empty. Add an entry such as `{"row": 0, "column": 0, "row_span": 1, "column_span": 2}` to `merged_cells`. Coordinates here start at 0 and count header rows.

Prefer a complete small table. If a large table is only partly annotated, set its `coverage` to `selected_rows` and explain exactly which source rows are included in `notes`. Do not describe this as a complete-table reference.

### Figures

Set `kind` to `chart`, `diagram`, `photo`, or `other`. Record a visible caption or `null`. The figure's locator and optional crop identify the visual asset to check for preservation; they are separate from its factual content.

For `facts`, write short questions with answers directly supported by the figure. Good targets include the chart title, an exact legend label, an explicitly labeled value, or a clearly visible relationship. Use one atomic fact per entry so later scoring can identify partial success.

Example structure only; these are not answers from the source PDFs:

```json
{
  "id": "fact_01",
  "question": "What percentage is explicitly labeled for Category A?",
  "expected_answer": "12,5%",
  "evidence_note": "Printed next to the Category A segment."
}
```

Do not infer exact values from an unlabeled bar or line. Record a supported relationship, such as which of two clearly identified categories is larger, or mark the region uncertain. A caption alone is not evidence that the tool captured information inside the figure.

If helpful, save a manually checked crop under `crops/` inside this folder and put its relative path in `reference_crop`. Otherwise leave it `null`. Crops are optional for this pilot; geometric image-matching metrics have not been selected.

### Uncertainty

Never invent an unreadable answer. Leave the uncertain target out of scored text, cells, or facts, and record it in `uncertain_regions`, for example:

```json
[
  {
    "locator": "Footnote below the bottom-right chart",
    "reason": "Too small to read confidently at available resolution."
  }
]
```

For a partly unreadable table, annotate only clearly legible rows under `selected_rows`, or omit that table from scoring. Never use an empty string to represent an unreadable cell: empty strings mean visibly blank cells.

## Review checklist

- The source filename and physical page number are correct.
- Every included answer was checked against the PDF, independently of extractor output.
- Text, numbers, units, punctuation, and table positions match the source.
- Every figure fact is directly visible and its evidence location is recorded.
- Uncertain content is excluded from expected answers and explicitly documented.
- Partial coverage is labeled correctly; unused template objects are removed.
- The JSON is valid, and the file is marked `reviewed` only after this check.

To check JSON syntax locally, replace the filename in this command with your copy:

```powershell
python -m json.tool "evaluation/reference/CarteiraTOP10_XP_dividendos__p001.json"
```
