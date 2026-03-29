# Valuation & M&A Modeling Skill

## Purpose
Build acquisition models for Canadian SMB targets. EV/EBITDA comps, DCF, acquisition model, returns, LOI price guidance.

## Trigger
Valuation, DCF, comps, acquisition model, returns, IRR, MOIC, LOI pricing, "what would we pay."

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
| IRR | Solve for r in NPV = 0 |

Sensitivity table: Entry multiple (3.0x-6.0x) x Exit multiple (3.5x-7.0x).

### Paul's Return Thresholds
- IRR: >25% base case
- MOIC: >2.5x at 5 years
- Payback: <4 years on equity

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
Acquisition model: Excel (.xlsx) → `deals/[DealCode]/03_valuation/[DealCode]_AcqModel_[Date].xlsx`
Deal memo: Word (.docx) → `deals/[DealCode]/04_outputs/Deal_Memo_[Date].docx`
