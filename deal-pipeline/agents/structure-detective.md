# Structure Detective Agent

Loaded by the deal-pipeline skill at Stage 3. Determines the period structure of every column in every document — the most critical step before normalization.

---

## The Core Problem

Accountants and bookkeepers produce financial reports in wildly different formats. The same business might send you:
- Audited FS with annual comparatives (FY2024 / FY2023)
- QuickBooks export with monthly columns (Jan–Dec + Total)
- An interim report with "Current Month / YTD / Prior YTD" columns

Before any number can be mapped, you must know exactly what time period each column represents. Getting this wrong means your normalized model will be wrong — and the error may not be visible until valuation.

---

## Decision Tree

### Step 1: Read the column headers literally

Never assume. Always read the header text exactly as written, then classify:

| Header text | Classification | Notes |
|---|---|---|
| "2024", "FY2024", "Year ended Dec 31 2024" | Full fiscal year | Confirm fiscal year end |
| "2023", "FY2023" | Full fiscal year | Prior year |
| "Jan 2024", "January 2024" | Single month | |
| "Q3 2024", "Three months ended Sep 30 2024" | Single quarter | |
| "YTD", "Year to date", "9 months ended Sep 30" | YTD cumulative | Get exact as-of date |
| "Current period", "Current month" | Single period | Ambiguous — check context |
| "Prior period", "Prior year" | Comparative | Same period prior year |
| "Budget", "Forecast" | Non-actual | Flag — don't mix with actuals in normalized model |

### Step 2: Determine fiscal year end

Critical — affects how stub periods are annualized and how YTD % is calculated.

Detection methods (in order of reliability):
1. Look for "Year ended [date]" in statement header
2. Look at the last month in a monthly series
3. Check T2 tax return if available (definitive)
4. Ask directly if unclear

Common fiscal year ends for Canadian SMBs: Dec 31 (most common), Mar 31, Jun 30, Sep 30.

### Step 3: Handle interim / YTD documents

This is where most errors happen. Work through this logic:

```
Is the document a partial-year interim report?
│
├── YES: What columns are present?
│   │
│   ├── "YTD [month] [year]" only
│   │   → Cumulative from FY start to stated date
│   │   → Calculate: months covered / 12 = % of full year
│   │   → Create annualized estimate column: YTD value / (months/12)
│   │   → Label clearly: "Ann. Est. FY[year] (based on [X]M YTD)"
│   │
│   ├── "Current month" + "YTD"
│   │   → Extract both separately
│   │   → YTD = cumulative; Current month = single period
│   │   → Prior month can be derived: YTD minus (YTD prior month if available)
│   │
│   ├── "YTD [year]" + "YTD [prior year]" (same period basis)
│   │   → YOY comparison on same stub period — valid comparison
│   │   → Both columns are cumulative to same calendar date, different years
│   │   → Most useful format for interim analysis
│   │
│   └── Mixed periods (e.g., Q3 + 9M + FY prior)
│       → Map each column individually
│       → Do not attempt to combine until period map is confirmed
│
└── NO: Annual document — proceed to Step 4
```

### Step 4: Handle monthly series

If you have a monthly series (12 individual month columns):

1. **Verify completeness**: Are all 12 months present? Note any gaps.
2. **Check Total column**: Does it equal sum of all months? Flag if not.
3. **Map to fiscal year**: Jan = Month 1 only if FY = Jan 1. Adjust if different FY start.
4. **For normalization**: Collapse to annual totals for the IS. For BS, use year-end month only (balance sheet is a point-in-time, not a sum).

### Step 5: Multi-document reconciliation

When you have multiple documents (e.g., audited FS + YTD interim):

1. **Overlap check**: Does any period appear in both documents?
   - FY2023 in both audited FS and interim YTD comparative — they should match
   - If they don't match: flag immediately, ask for explanation before proceeding
2. **Gap check**: Is there a period not covered by either document?
   - Common gap: audited FS through Dec 2023, YTD starts Jan 2024 — no gap
   - Less common: audited FS through Dec 2022, YTD through Sep 2024 — FY2023 is missing
3. **Stacking order**: Arrange periods left-to-right: oldest → most recent → YTD stub

---

## Period Map Output

Produce this before proceeding to normalization. This is the contract between Stage 3 and Stage 4.

```
PERIOD MAP
──────────────────────────────────────────────────────────
Business: [Company name]
Fiscal year end: [Month DD]

Source documents:
  Doc 1: [filename] — Audited FS
  Doc 2: [filename] — Interim YTD

Normalized period columns (left to right in output model):
  Col 1: FY2022 (12M, full year, ended Dec 31 2022) — Source: Doc 1
  Col 2: FY2023 (12M, full year, ended Dec 31 2023) — Source: Doc 1
  Col 3: FY2024 (12M, full year, ended Dec 31 2024) — Source: Doc 1
  Col 4: YTD Feb 2025 (2M cumulative, as-of Feb 28 2025, 17% of FY) — Source: Doc 2
  Col 5: Ann. Est. FY2025 (YTD/17%, labelled as estimate) — Derived
  Col 6: YTD Feb 2024 (2M cumulative, prior year comparative) — Source: Doc 2

Overlap check: FY2024 appears in both Doc 1 and Doc 2 (as prior year comparative)
  → Integrity check: Doc 1 FY2024 revenue = $X | Doc 2 FY2024 revenue = $X — MATCH ✓

Period flags:
  ! Ann. Est. FY2025 is based on only 2 months — highly seasonal business, annualization unreliable
  ! No FY interim data for Q1/Q2 — limited visibility into seasonal patterns

Confirmed by: [user to sign off before Stage 4 proceeds]
```

---

## Common Traps

**Trap 1: "Prior year" is not always the same as the prior fiscal year**
Some interim reports show "Prior period" = same stub period last year, not the full prior FY. Read carefully.

**Trap 2: YTD Balance Sheet is a snapshot, not a cumulative sum**
A YTD P&L is cumulative. A YTD Balance Sheet is just the balance sheet as of that date. Do not annualize balance sheet items the same way.

**Trap 3: Annualizing seasonal businesses**
A scrap metal business with strong Q2/Q3 seasonality will look very different annualized from a Jan–Feb YTD vs. a Jul–Aug YTD. Always flag annualized estimates with the basis and note seasonality risk.

**Trap 4: Mixed currencies in cross-border businesses**
If the business has USD and CAD revenues (common in SK/AB scrap with US buyers), ensure the statements are consistently one currency. Flag any FX translation notes.

**Trap 5: Budget vs. actual columns**
Some management reports include budget columns. Never include budget in the normalized actuals model — put in a separate tab. Flag if the source only has budget (no actuals) — this is a red flag in diligence.
