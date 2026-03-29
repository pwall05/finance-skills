# Financial Statement Analysis Skill

## Purpose
Deep analysis of normalized financial statements. QoE memo, margin analysis, working capital, KPIs, Diligence OS Module 2 output.

## Trigger
Financial statement review, QoE analysis, margin analysis, working capital, KPI benchmarking, "how does this business look financially."

## References
- `financial-statement-analysis/references/kpi-library.md`
- `financial-statement-analysis/references/diligence-os-structure.md`

---

## Analysis Framework

### 1. Revenue Quality
- Revenue CAGR (2-year, 3-year)
- Revenue by segment/service line if available
- Customer concentration — top 5 as % of revenue (request if not provided)
- Seasonality — flag if YTD stub distorts comparisons
- Contract vs. spot revenue mix
- Flag: single customer >20% revenue = concentration risk

### 2. Margin Analysis
For each normalized period compute:

| Metric | Formula |
|---|---|
| Gross Margin % | Gross Profit / Net Revenue |
| EBITDA Margin % | EBITDA / Net Revenue |
| EBIT Margin % | EBIT / Net Revenue |
| Net Margin % | Net Income / Net Revenue |

Compare to Canadian SMB benchmarks. Trend analysis: expanding or contracting — explain the driver.

### 3. Adjusted EBITDA Build (waterfall)
```
Reported Net Income
+ Income Tax Provision
+ Interest Expense
+ Depreciation & Amortization
= Reported EBITDA

QoE Adjustments:
+ Owner compensation above market (normalize to $150-185K)
+ Personal vehicle / personal expenses
+ One-time bad debt
+ Non-recurring losses / write-offs
- Non-recurring gains (asset disposals, insurance)
- COVID subsidies / government grants
+/- Related-party rent (normalize to market)
= Adjusted EBITDA
```

Present as waterfall table by year. Show low/base/high adjustment scenarios where judgment applies.

### 4. Working Capital Analysis
| Metric | Formula | Watch |
|---|---|---|
| Net Working Capital | Current Assets - Current Liabilities | Exclude shareholder receivables |
| Current Ratio | Current Assets / Current Liabilities | >1.2x healthy |
| DSO | AR / (Revenue / 365) | Trending up = collections issue |
| DPO | AP / (COGS / 365) | |
| Inventory Days | Inventory / (COGS / 365) | |
| NWC % Revenue | NWC / Revenue | Use for peg calculation |

### 5. Leverage & Debt
| Metric | Formula |
|---|---|
| Total Debt | Current LTD + Long-Term Debt |
| Net Debt | Total Debt - Cash |
| Debt / Adj. EBITDA | Net Debt / Adj. EBITDA |
| Interest Coverage | Adj. EBITDA / Interest |
| Fixed Charge Coverage | (Adj. EBITDA - Capex) / (Interest + Principal) |

### 6. Capex & Asset Intensity
- Maintenance capex estimate: use D&A as proxy if actuals not available
- Capex / Revenue %
- Net PPE / Revenue
- Equipment age / condition — request appraisal if PPE > 20% of deal value

### 7. Red Flag Summary
| # | Flag | Period | Amount | Severity | Recommended Action |
|---|---|---|---|---|---|

---

## Output
QoE memo as Word (.docx). Save to: `deals/[DealCode]/02_diligence/QoE_Memo_[Date].docx`

Structure: 1. Executive Summary (adj. EBITDA range, 3 key findings) 2. Revenue Quality 3. Margin Analysis 4. Adj. EBITDA Build 5. Working Capital 6. Leverage 7. Capex & Asset Intensity 8. Red Flag Register 9. Open Items
