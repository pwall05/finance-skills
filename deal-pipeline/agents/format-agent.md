# Format Agent

Loaded by the deal-pipeline skill at Stage 2. Handles detection and extraction of financial data from PDF and Excel source documents.

---

## PDF Extraction

### Identifying PDF structure type

**Type 1: Formal financial statement (most common)**
Signals: Column headers at top (years/periods), rows = line items, subtotals present, footer has accountant/auditor signature block.
Strategy: Treat as a structured table. Extract header row first (period labels), then each data row as (account label, values...).

**Type 2: Accounting software print-to-PDF**
Signals: Often landscape, tight column spacing, may have page breaks mid-statement, headers repeat on each page.
Strategy: Same as Type 1 but watch for repeated headers on page 2+ — deduplicate.

**Type 3: Scanned / image PDF**
Signals: No selectable text, slightly skewed rows, handwritten annotations possible.
Strategy: Flag immediately. OCR required. Confidence will be lower — flag all extracted values for manual verification.

**Type 4: Narrative with embedded tables**
Signals: Mix of prose and financial data. Tables may be embedded in notes section.
Strategy: Extract all table structures. Identify which tables are IS/BS/CF vs. supplementary schedules.

### PDF extraction process

1. **Page inventory**: List all pages and identify which pages contain IS, BS, CF, notes, schedules
2. **Header extraction**: Pull period/column labels from the top of each statement
3. **Row extraction**: For each row: capture (indentation level, label, values for each column)
   - Indentation signals hierarchy: top-level = category, indented = line item, double-indented = sub-item
   - Subtotal rows typically bold or have a line above — flag these as subtotals, don't double-count
4. **Sign handling**: Watch for parentheses = negative in formal statements (e.g., "(1,234)" = -1,234)
5. **Unit check**: Confirm whether values are in $, $000s, or $millions — look for "in thousands" or similar note at top of statement

### Common PDF extraction failure modes

| Failure | Signal | Fix |
|---|---|---|
| Merged cells | Value appears in wrong column | Re-align by checking subtotal rows |
| Footnote numbers | "1,234 ¹" extracted as "1,234 1" | Strip trailing superscripts |
| Page break mid-row | Row split across pages | Detect continuation markers |
| Multi-currency | $ and foreign currency mixed | Flag; ask which is functional currency |
| Rounding differences | Subtotals off by $1-2 | Normal in rounded statements; don't flag as integrity failure |

---

## Excel Extraction

### Tab inventory (do this first)

List all tabs, identify likely content:
- Tabs named "IS", "Income", "P&L", "Profit" → Income Statement
- Tabs named "BS", "Balance", "Position" → Balance Sheet  
- Tabs named "CF", "Cash", "Cashflow" → Cash Flow
- Tabs named "TB", "Trial Balance" → Raw trial balance — requires more work to normalize
- Tabs named after months ("Jan", "Feb"...) → Monthly series
- Tabs named after years ("2022", "2023"...) → Annual by tab — stack into single multi-period view
- Single tab with everything → Raw export — layout detection required

### Layout detection for single-tab exports

Common accounting software export layouts:

**QuickBooks / Sage horizontal layout**:
```
Row 1: [blank]     Jan 2024   Feb 2024   ...   Total
Row 2: Revenue
Row 3:   Product Sales   $X   $X   ...   $X
```
→ First column = account labels, subsequent columns = periods. Standard horizontal layout.

**QuickBooks / Sage vertical layout** (less common):
```
Col A: Date
Col B: Account
Col C: Debit
Col D: Credit
Col E: Balance
```
→ This is a transaction-level export, not a summary. Requires pivot/aggregation before normalization. Flag this — it needs more processing time.

**NetSuite export** (common in more sophisticated businesses):
- Usually clean tabular format
- May have department/location splits as additional columns
- Watch for: consolidated vs. entity-level — confirm you have the right entity

### Excel-specific extraction rules

1. **Empty rows**: Skip blank rows that are just visual spacers
2. **Bold rows**: Typically subtotals or category headers — flag as such
3. **Formulas**: If cells have formulas (=SUM...), extract the calculated value, not the formula
4. **Hidden rows/columns**: Check for hidden rows — they often contain detail that's been collapsed
5. **Multiple currency**: If cells have mixed currency symbols, flag immediately
6. **#REF or #VALUE errors**: Flag these cells — the source model has broken references

### Output of Format Agent

A structured data object (describe in text, pass to Stage 3):

```
FORMAT AGENT OUTPUT
───────────────────
File: [filename]
Type: PDF (Type 1 — formal FS) / Excel (horizontal layout, 3 tabs)

Statements identified:
  Income Statement: [location — e.g., "Page 2-3" or "Tab: IS"]
  Balance Sheet: [location]
  Cash Flow: [location — note if ABSENT]

Column headers detected:
  IS: [list of column headers as they appear in source]
  BS: [list of column headers]
  CF: [list of column headers or "Not found"]

Units: $000s / $ / $M
Sign convention: Parentheses = negative / Minus sign = negative

Extraction confidence: HIGH / MEDIUM / LOW
Issues flagged: [list any problems found]

→ Pass to Structure Detective with column headers for period analysis
```
