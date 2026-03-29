# Valuation & M&A Modeling Skill

## Purpose
Build acquisition models for Canadian SMB targets. EV/EBITDA comps, DCF, acquisition model, returns, LOI price guidance. Also builds 10-year driver-based forward financial models with adjustable assumptions.

## Trigger
Valuation, DCF, comps, acquisition model, returns, IRR, MOIC, LOI pricing, "what would we pay," forward model, 10-year model, financial forecast.

## References
- `valuation-ma-modeling/references/canadian-sme-benchmarks.md`
- `valuation-ma-modeling/references/diligence-os-structure.md`

---

## Step 1 — Establish Adjusted EBITDA
Confirm before valuing:
- LTM Adjusted EBITDA (most recent 12 months)
- FY Adjusted EBITDA (last 3 fiscal years)
- Forward EBITDA estimate if available

If normalization not done — fetch `deal-pipeline/SKILL.md` first.

## Step 2 — EV/EBITDA Comps

Reference ranges (fetch full benchmarks from canadian-sme-benchmarks.md):
- EBITDA $1M-$3M: 3.0-4.5x
- EBITDA $3M-$7M: 4.0-5.5x
- EBITDA $7M-$15M: 5.0-7.0x

Multiple adjustments:
| Factor | Direction |
|---|---|
| Recurring / contracted revenue | + |
| Customer concentration >30% single customer | - |
| Owner-dependent operations | - |
| Strong management team in place | + |
| Asset-heavy (trucks, equipment) | - |
| Growing market (SK/AB energy recovery) | + |
| Cyclical or declining revenue | - |
| Reviewed/audited financials | + |
| Internal/unreviewed books | - |

## Step 3 — Acquisition Model

### Sources & Uses
```
USES                              SOURCES
Purchase Price (EV)               Senior Debt (chartered bank)
+ Transaction Costs (2-3%)        Vendor Take-Back (VTB)
+ Working Capital Peg             Equity (Paul / co-investors)
= Total Uses                      = Total Sources
```

### Financing Assumptions (Canadian, SK/AB)
- Senior debt: 2.5-3.5x Adj. EBITDA (BDC, TD, RBC, ATB)
- Interest rate: Prime + 1.5-2.5% variable or 6-8% fixed
- Amortization: 5-7 years
- VTB: 10-20% of purchase price, 5-7% interest, subordinated
- Equity: balance of sources

### Returns Model
| Metric | Formula |
|---|---|
| Entry EV | Adj. EBITDA x Entry Multiple |
| Exit EV | Forward EBITDA x Exit Multiple |
| Equity Proceeds | Exit EV - Remaining Debt |
| MOIC | Equity Proceeds / Equity Invested |
| IRR | MOIC^(1/hold_years) - 1 (approximation) |

Sensitivity table: Entry multiple (3.0x-6.0x) x Exit multiple (3.5x-7.0x).

### Paul's Return Thresholds
- IRR: >25% base case
- MOIC: >2.5x at 5 years
- Payback: <4 years on equity

---

## Step 3.5 — 10-Year Driver-Based Forward Financial Model

**ALWAYS build this when a forward model is requested.** All key assumptions must be adjustable inputs (blue cells) in a dedicated Assumptions tab. This is standard for every model.

### Context-Gathering (Required Before Building)

First, read the normalized financial statements from `01_normalized/` to understand:
- Revenue segmentation and historical CAGR by segment
- COGS mix and gross margin trends (3-year)
- OpEx line-by-line as % of revenue
- D&A as % of revenue
- Working capital ratios (DSO, DPO, inventory days, prepaid %, accrued %)
- Existing debt balance and repayment schedule from BS

If context is ambiguous or key drivers are unclear, ask Paul these targeted questions before building:
1. Revenue growth assumption approach — use historical CAGR, industry benchmarks, or custom?
2. Are there specific segments expected to grow faster/slower?
3. Any known planned capex (fleet, equipment, facilities)?
4. Target leverage at exit — affects financing assumptions
5. Is there a cash flow statement available? (needed for capex actuals)

Do NOT proceed to build if the historical statements haven't been normalized first.

### Model Architecture (4 Tabs)

**Tab 1: Assumptions** — All blue input cells. Standard sections:
- Revenue drivers: Annual growth % by segment × 10 years (adjustable per year)
- COGS drivers: Each line as % of net revenue × 10 years
- OpEx drivers: Each line as % of revenue (or $ for owner comp) × 10 years
- D&A: % of net revenue
- Capex: Maintenance (% of revenue) + Growth (% of revenue increment), both year-by-year
- Working capital: DSO, DPO, Inventory days, Prepaid %, Accrued % (single values)
- Debt: Opening balance, annual repayment, interest rate
- Tax: Effective tax rate (single value)
- Acquisition inputs: Entry multiple, exit multiple, debt %, VTB %, transaction costs, rates, hold period
- Color code: Blue = adjustable inputs, Yellow background = key assumptions requiring attention

**Tab 2: IS_Model** — Income statement. Columns: Historical FY-2, FY-1, FY0 | Forecast FY1-FY10.
Key rows:
- Revenue by segment (each grows from prior year × growth rate assumption)
- Non-recurring items shown separately, zeroed in forecast
- Net Revenue (normalized) = sum of recurring segments only (KEY DRIVER ROW)
- COGS by line = Net Revenue × respective % assumption
- Gross Profit and Gross Margin %
- OpEx by line = Net Revenue × respective % assumption (owner comp = $ assumption)
- EBITDA (as modeled) and EBITDA margin %
- D&A = Net Revenue × D&A %
- EBIT, Interest (from debt schedule), EBT, Tax, Net Income
- QoE adjustments section (historical add-backs shown for context; forecast = $0)
- Adjusted EBITDA and Adjusted EBITDA margin % (KEY OUTPUT ROW)
- Summary metrics: Revenue growth YoY %, EBITDA growth YoY %, Adj EBITDA growth YoY %
- Debt schedule: Opening, repayment, closing, average, interest (drives interest expense row)

**Tab 3: FCF** — Free cash flow and working capital.
- Working capital schedule: AR, Inventory, Prepaid, AP, Accrued → Net WC → Change in NWC
- Capex schedule: Revenue, Revenue increment, Maintenance capex, Growth capex, Total capex
- Free cash flow: Adj EBITDA - Cash taxes - Total capex - Change in NWC = Unlevered FCF
- Levered FCF: Unlevered FCF - After-tax interest - Mandatory repayment
- Cumulative Levered FCF (shows payback timeline)

**Tab 4: AcqModel** — Acquisition returns.
- Entry valuation: Entry EBITDA, entry multiple → EV
- Sources & uses: Senior debt, VTB, equity, transaction costs
- Acquisition debt repayment schedule (year-by-year opening/closing/interest)
- Returns: Bear (3yr) / Base (5yr) / Bull (7yr) — each showing exit EBITDA, exit EV, remaining debt, equity proceeds, MOIC, IRR
- MOIC sensitivity table: Entry multiple (3.0-6.0x) rows × Exit multiple (3.5-6.0x) cols

### Formula Conventions
- Historical data: Hardcoded blue inputs
- Forecast formulas: Black (drive from assumptions via `=Assumptions!$B$row` or `=Assumptions!col_letter$row`)
- Cross-sheet references: Green text
- Key assumptions with material impact: Yellow background
- Assumption reference helpers: Single-value = `=Assumptions!$B$row`, year-varying = `=Assumptions!$col_letter$row`
- Net Revenue for forecast: `=TotalRevenue + Less_NonRecurring` (gain on disposal rows zero out automatically)

### Default Assumptions (Moderate Stance — Always Adjustable)
Calibrate to historical actuals from normalized IS. As a general template:
- Revenue growth: Step down from historical CAGR over 5 years, then stabilize at 5% LT
- COGS %: Hold at FY0 actuals with modest efficiency assumption (0-1% margin expansion)
- Owner comp: Normalize to market ($150-200K base + 3% CPI annual escalation)
- Maintenance capex: 5-6% of revenue (proxy if no CF statement; note the caveat)
- Growth capex: 15-20% of revenue increment in early years, step down to 10% years 6-10
- DSO/DPO: From FY0 balance sheet (AR/Revenue*365 and AP/COGS*365)
- Debt: From normalized BS; standard chartered bank terms (5-7yr am)
- Tax: Effective rate from 3-year average (CCPC, SK/AB — typically 22-25%)

**IMPORTANT**: Never hardcode a specific growth rate without a note explaining the rationale anchored to historical actuals. Always show the historical CAGR as context in the Assumptions tab.

---

## Step 4 — DCF (if forward model available)
- Forecast: 5 years
- Terminal growth: 2.0-3.0%
- WACC: 12-16% (private company, SK/AB, size-adjusted)
- Terminal value: Gordon Growth Model
- Mid-year discounting

Sanity check: if DCF diverges >20% from comps — explain the driver, do not average blindly.

## Step 5 — LOI Price Guidance

| Scenario | Adj. EBITDA | Multiple | EV | Equity Required | IRR (5yr) |
|---|---|---|---|---|---|
| Bear | $X | 3.5x | $X | $X | X% |
| Base | $X | 4.5x | $X | $X | X% |
| Bull | $X | 5.5x | $X | $X | X% |

If walk price > target price at base EBITDA — deal doesn't work, explain clearly.

## Step 6 — Canadian Tax Considerations

### Asset vs. Share Purchase
| Factor | Asset Purchase | Share Purchase |
|---|---|---|
| Buyer preference | Generally preferred — step-up in ACB | Avoids assumed liabilities |
| Seller preference | Less preferred (recapture, no LCGE) | Preferred (LCGE eligibility) |
| QSBC / LCGE | N/A | Seller may qualify — $1.016M LCGE (2024 limit) |
| Purchase price allocation | Required (Class 8, 10, 14.1, etc.) | Not required |
| GST/HST | May apply — check ETA s.167 election | No GST on share transfer |

Key items: CCPC small business rate (Fed 9% + SK 1% or AB 2% on first $500K), shareholder loan must be resolved pre-close, Due from shareholder = non-operating asset adjust purchase price.

## Output
10-year model: Excel (.xlsx) → `deals/[DealCode]/03_valuation/[DealCode]_10yr_Model_[Date].xlsx`
Acquisition model: Excel (.xlsx) → `deals/[DealCode]/03_valuation/[DealCode]_AcqModel_[Date].xlsx`
Deal memo: Word (.docx) → `deals/[DealCode]/04_outputs/Deal_Memo_[Date].docx`
