---
name: deal-pipeline
description: Master orchestration skill for private investment diligence. Runs a full 5-stage agentic pipeline: ingest financial documents (PDF or Excel, any format) → detect structure → extract raw data → normalize into standard IS/BS/CF → validate → hand off to model builder. Use this skill whenever the user is starting diligence on a new deal, uploads financial statements or interim reports, says "run the pipeline", "process these financials", "normalize the statements", "new deal", or "start diligence on X". This skill coordinates the other finance-skills; always load it first for any new deal workflow.
---

# Deal Pipeline — Master Orchestration Skill

This skill runs a 5-stage pipeline to transform raw financial documents (any format) into a clean, validated, normalized Excel model ready for valuation and analysis.

**Load order**: This skill coordinates sub-agents defined in `agents/`. Read this file first, then load the relevant agent file for each stage.

---

## Pipeline Overview

```
Stage 1: INGEST       → Identify what documents exist and what's missing
Stage 2: FORMAT       → Detect file type, layout, period structure
Stage 3: STRUCTURE    → Determine period logic (monthly/YTD/YOY/mixed)
Stage 4: NORMALIZE    → Map all line items to standard IS/BS/CF template
Stage 5: VALIDATE     → Check integrity, flag QoE issues, gate for review
         ↓
   Normalized Excel → model builder (valuation-ma-modeling skill)
```

Always run stages in order. Do not skip Stage 3 — period structure detection is the most error-prone step and must be explicit before normalization begins.

---

## Stage 1 — Ingest & Document Inventory

**Goal**: Know exactly what you have and what's missing before touching any numbers.

On receiving documents, produce a document inventory:

```
DOCUMENT INVENTORY
──────────────────
Received:
  □ [filename] — [type: audited FS / NTR / management prepared / interim]
                  [periods: FY2022, FY2023, FY2024 / YTD Nov 2024]
                  [format: PDF / Excel]
                  [quality: Clean / Some issues / Requires cleanup]

Missing (request from vendor):
  □ [what's missing and why it matters]

Flags:
  □ [anything immediately notable — gaps, restatements, different auditors, etc.]
```

**Common document types in Canadian SMB deals**:
- **Audited financial statements** (most reliable): T2 corp return attachment or standalone audit
- **Notice to Reader (NTR)** / Compilation: Accountant-prepared, no assurance — common for smaller businesses
- **Management-prepared**: Owner/bookkeeper prepared — treat with skepticism, higher QoE scrutiny
- **Interim / YTD**: In-house, often from accounting software (QuickBooks, Sage, NetSuite) — format varies wildly

**What to request if not provided**:
- 3 years of annual statements (minimum)
- Most recent YTD (within last 60 days ideally)
- Prior year YTD for same period (for YOY comparison)
- T2 corporate tax returns (cross-check to financial statements)
- AR aging, AP aging, inventory detail (working capital quality)

---

## Stage 2 — Format Agent

**Goal**: Detect the file type and layout pattern. Load `agents/format-agent.md` for detailed instructions.

Quick reference:

| Signal | Inference |
|---|---|
| PDF with column headers across top | Formal financial statement — parse as structured table |
| PDF with no clear table structure | Narrative/notes format — extract with care |
| Excel with named tabs (IS, BS, CF) | Already somewhat organized — map to template |
| Excel with one tab, all data | Raw export (QuickBooks, Sage) — detect layout first |
| Excel with columns = months | Monthly series — detect fiscal year start |
| Excel with "YTD" in header | Cumulative — identify as-of date |
| Columns like "2024 / 2023 / 2022" | Annual comparative — standard structure |
| Columns like "Current Period / YTD / Prior YTD" | Interim comparative — needs re-stacking |

---

## Stage 3 — Structure Detective (Critical)

**Goal**: Determine the period logic of EVERY column in every document before mapping any numbers. This is the hardest stage. Load `agents/structure-detective.md` for full logic.

**The five period structures you will encounter**:

### A. Annual comparative (most common in audited FS)
```
                    FY2024    FY2023    FY2022
Revenue             $X        $X        $X
```
→ Each column is a full 12-month fiscal year. Standard. Map directly.

### B. Monthly series (common in management reporting / NetSuite exports)
```
                    Jan       Feb       Mar    ...    Dec     Total
Revenue             $X        $X        $X            $X      $X
```
→ Columns are individual months. Sum to annual for normalized model. Verify Total column = sum of months (flags data integrity issues if not).

### C. YTD cumulative (common in interim reporting)
```
                    YTD Nov 2024    YTD Nov 2023
Revenue             $X              $X
```
→ Each column represents the cumulative period from fiscal year start to the stated date. Do NOT treat as a full year. Flag the as-of date and stub period clearly.

### D. YTD + current period (common in accounting software exports)
```
                    Nov 2024    YTD Nov 2024    YTD Nov 2023
Revenue             $X          $X              $X
```
→ First column is the single month; YTD columns are cumulative. Extract both — current month useful for run-rate, YTD for period comparison.

### E. Mixed / custom (most dangerous)
```
                    Q3 2024    9M 2024    FY2023    FY2022
Revenue             $X         $X         $X        $X
```
→ Columns represent different period lengths. Must re-stack explicitly. Annualize the stub period for comparability but label clearly as estimated/annualized.

**Period detection checklist** (run on every document):
- [ ] What is the fiscal year end? (Not always Dec 31 — ask or infer from dates)
- [ ] What does each column header represent exactly?
- [ ] Are any columns YTD cumulative vs. full-period?
- [ ] If interim: what is the as-of date and what % of fiscal year does it cover?
- [ ] Are prior period comparatives on the same period basis?
- [ ] Do monthly columns sum to the stated YTD/annual total?

**Output of Stage 3**: A period map table that will be used in Stage 4:
```
PERIOD MAP
──────────
Doc: [filename]
  Col A: "FY2024" → Full year, fiscal year ended [date]
  Col B: "FY2023" → Full year, fiscal year ended [date]
  Col C: "YTD Sep 2024" → Cumulative 9 months, as-of Sep 30 2024 (75% of FY)
  Col D: "Sep 2024" → Single month only
```

---

## Stage 4 — Normalization Agent

**Goal**: Map every source line item to the standard chart of accounts template. Load `agents/normalization-agent.md` for full mapping logic and the standard template.

**Normalization principles**:
1. **Never drop a line item** — if you can't map it, create an "Other / unclassified" bucket and flag it
2. **Preserve source labels** — in the normalized model, keep a "Source description" column alongside the standardized account name
3. **Respect the period map** — YTD columns get their own clearly labeled columns; do not blend with annual
4. **One row per account** — if the source has multiple rows that map to the same account, combine them and note it
5. **Sign convention**: Revenue = positive; expenses = positive (subtracted in subtotals); losses = negative income items
6. **Owner comp normalization**: Always create a separate row for owner/related party compensation — it will need normalization in QoE

**Standard output structure** (see `references/normalized-template.md` for full schema):

```
INCOME STATEMENT (normalized)
Period →           FY2022   FY2023   FY2024   YTD [date]  Ann. Est.
─────────────────────────────────────────────────────────────────────
Revenue
  [line items]
Total Revenue
Gross Profit
  [line items]
EBITDA (reported)
EBITDA (normalized)    ← add-back detail in QoE tab

BALANCE SHEET (normalized)
As of →            FY2022   FY2023   FY2024   Interim
[standard structure]
Check: Assets = Liabilities + Equity  ← must balance

CASH FLOW (normalized)
Period →           FY2022   FY2023   FY2024
Operating / Investing / Financing
FCF = Operating CF - Maintenance Capex
```

---

## Stage 5 — Validation + QoE Flags

**Goal**: Verify integrity and flag issues for human review before handing to model builder.

**Integrity checks** (automated):
- [ ] Balance sheet balances: Total Assets = Total Liabilities + Equity (flag if off by >$1)
- [ ] Cash flow ties: Ending cash on CF = cash on balance sheet
- [ ] Revenue ties: IS revenue = any revenue schedule provided
- [ ] YTD check: if monthly data present, sum of months = YTD total
- [ ] Prior year consistency: FY2023 in this year's statements = FY2023 in last year's statements

**QoE flags** (human judgment required — flag, don't resolve):
- [ ] Owner compensation: Is it disclosed? Is it above/below market rate for this role?
- [ ] Related party transactions: Management fees, rent to related party, loans to/from shareholders
- [ ] Revenue recognition: Any large single-period spikes? Deferred revenue balances?
- [ ] One-time items: Legal costs, restructuring, non-recurring capex, insurance proceeds
- [ ] Accounting policy changes: Different depreciation methods, inventory costing changes
- [ ] Missing periods: Gaps in the financial history — what happened?
- [ ] Auditor changes: Different auditor year-over-year — why?
- [ ] NTR/compilation vs. audit: Lower reliability — additional verification needed

**Validation output**:
```
VALIDATION REPORT
─────────────────
Integrity: PASS / FAIL (with specifics)
  □ BS balances: ✓ / ✗ [detail]
  □ CF ties: ✓ / ✗ [detail]
  □ YTD sums: ✓ / ✗ [detail]

QoE flags (requires human review):
  HIGH: [item] — [why it matters]
  MED:  [item] — [why it matters]
  LOW:  [item] — [note]

Confidence level: HIGH / MEDIUM / LOW
  HIGH = Audited statements, all periods, all checks pass
  MEDIUM = NTR or management-prepared, minor integrity issues
  LOW = Incomplete periods, integrity failures, or significant QoE flags

Ready for model builder: YES / NO (pending [list of issues])
```

---

## Human Review Gate

**Before passing to model builder, always surface**:
1. The period map (Stage 3 output) — confirm your period interpretation is correct
2. Any line items that couldn't be mapped (Stage 4 unclassified items)
3. The validation report (Stage 5) — especially any integrity failures
4. QoE flags that need resolution before modeling

This is not optional. A model built on misinterpreted periods or unresolved integrity issues will produce wrong outputs that are worse than no model.

---

## Handoff to Model Builder

When validation passes and human review confirms, output:

```
PIPELINE COMPLETE
─────────────────
Normalized Excel: [filename]-normalized.xlsx
  Tabs: IS_normalized / BS_normalized / CF_normalized / QoE_flags / Source_data / Period_map

Ready for: valuation-ma-modeling skill
  Suggested next step: "Load the normalized Excel and build a DCF + comps model 
  using the valuation-ma-modeling skill. Key assumptions to validate: [list]"
```

---

## Reference Files

- `agents/format-agent.md` — Detailed PDF and Excel extraction logic
- `agents/structure-detective.md` — Full period structure detection decision tree
- `agents/normalization-agent.md` — Line item mapping rules and standard chart of accounts
- `references/normalized-template.md` — Standard Excel output template schema
- `references/common-account-mappings.md` — Common source labels → standard account mappings
