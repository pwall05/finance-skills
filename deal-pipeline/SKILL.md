# Deal Pipeline Skill

## Purpose
8-step agentic pipeline for ingesting, normalizing, and validating financial statements from Canadian SMB acquisition targets. Produces a clean normalized Excel workbook ready for the valuation model.

## Trigger
Activate when Paul says: "run the pipeline", "new deal", "start diligence", or uploads financial documents.

## Sub-Agents — Fetch as Needed
- Format detection: `deal-pipeline/agents/format-agent.md`
- Period classification: `deal-pipeline/agents/structure-detective.md`
- Normalization: `deal-pipeline/agents/normalization-agent.md`
- Account mappings: `deal-pipeline/references/common-account-mappings.md`

---

## The 8 Steps

### Step 1 — Confirm Skill Loaded
Confirm this skill file has been fetched. Announce: "Deal pipeline active. Starting Step 2."

### Step 2 — Document Inventory
Produce a table of every file received:
- File name
- Statement type (IS / BS / CF / Other)
- Periods visible
- Status (received / missing)

Then list what is MISSING from a full diligence package:
- Cash flow statement
- T2 corporate tax returns (3 years)
- Equipment schedule / appraisal
- Customer concentration data
- Shareholder loan agreements (if related-party balances present)

List IMMEDIATE RED FLAGS spotted on intake — do not wait for normalization. Common flags:
- Owner salary (likely above market)
- Personal vehicle or expenses running through opex
- Gain/loss on asset disposals in revenue or EBITDA
- Related-party receivables/payables
- One-time items (lawsuits, bad debt, COVID relief)
- YTD stub period that could be confused with annual

### Step 3 — Format Detection
Fetch `deal-pipeline/agents/format-agent.md`. For each file:
- File type (xlsx, pdf, csv)
- Tab/sheet name(s)
- Layout (vertical accounts / horizontal periods, or transposed)
- Header row number
- Data start row number
- Subtotal rows present (yes/no)
- Section header rows present (yes/no)

### Step 4 — Period Map STOP HERE
Fetch `deal-pipeline/agents/structure-detective.md`.

Classify every data column across all files:

| Column | File | Label as Received | Classification | Notes |
|---|---|---|---|---|

Classification options:
- `Annual` — full 12-month fiscal year (specify dates)
- `Stub` — partial year YTD (specify months, do NOT annualize without flagging)
- `Trailing Twelve Months (TTM)` — rolling 12 months
- `Budget / Forecast` — forward-looking, not actuals
- `Unknown` — cannot determine, flag for Paul

**HARD STOP: Show period map to Paul and wait for explicit confirmation before proceeding.**
Never blend YTD and annual columns. Never annualize a stub without labeling it clearly.

### Step 5 — Normalization
After Paul confirms period map, fetch:
- `deal-pipeline/agents/normalization-agent.md`
- `deal-pipeline/references/common-account-mappings.md`

Map every line item to the standard IS/BS/CF template.

**Income Statement template:**
Revenue (Gross Revenue, Less Returns) → Net Revenue → COGS (Direct Labour, Materials, Subcontractors, Equipment, Fuel) → Gross Profit → OpEx (Owner Comp flagged, Admin, Rent, Insurance, Professional Fees, Vehicle, T&E, Advertising, Utilities, Bad Debt, Other) → EBITDA → D&A → EBIT → Interest → EBT → Tax → Net Income

**Balance Sheet template:**
Current Assets (Cash, AR, Inventory, Prepaid, Related Party flagged) → Non-Current (PPE gross, Accum Depr, Intangibles) → Total Assets || Current Liabilities (AP, Accrued, Current LTD, Related Party flagged) → LT Liabilities (LTD) → Total Liabilities → Equity (Share Capital, Retained Earnings) → Total L+E

**Cash Flow (if available):** Operating / Investing / Financing / Net change

### Step 6 — Validation and QoE Flags
Run validation checks:
- [ ] IS net income reconciles to BS retained earnings change
- [ ] BS balances (Assets = Liabilities + Equity) each period
- [ ] Revenue directionally consistent YoY
- [ ] No negative revenue or COGS
- [ ] Subtotals foot correctly

QoE flags — for every flag specify: account, period(s), amount, nature, recommended treatment.

Common Canadian SMB QoE flags:
- Owner/family comp above market (benchmark: $150-185K total comp for owner-operator)
- Personal vehicle, travel, cell, meals through business
- Gain/loss on asset disposal — exclude from EBITDA
- Management fees to holdco — investigate, often add-back
- Non-recurring: legal settlements, insurance recoveries, COVID subsidies (CEWS, CEBA)
- Related-party rent — normalize to market
- One-time bad debt — add back if isolated

**STOP if any integrity failure is unresolved.**

### Step 7 — Produce Normalized Excel
File name: `[DealCode]_Normalized_[Date].xlsx`

Required tabs:
1. `IS_normalized` — normalized IS, all periods, QoE adjustments layered
2. `BS_normalized` — normalized BS
3. `CF_normalized` — cash flow if available
4. `QoE_flags` — full flag table
5. `Source_data` — raw numbers unmodified
6. `Period_map` — confirmed period map
7. `Mapping_log` — every account to category mapping

Save to: `deals/[DealCode]/01_normalized/`

### Step 8 — Handoff
Announce: "Normalization complete. [X] QoE flags identified. Adjusted EBITDA range: $[LOW]-$[HIGH]. Ready for valuation model — fetch valuation-ma-modeling/SKILL.md to proceed."

---

## Hard Rules
- Never skip the Step 4 period map confirmation
- Never blend YTD and annual columns without explicit labeling
- Never pass to model building if any integrity check fails
- Always flag owner comp regardless of amount
- Canadian context throughout: CAD, CCPC tax rates, QSBC eligibility where relevant
