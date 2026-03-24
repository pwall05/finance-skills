---
name: financial-statement-analysis
description: Analyze income statements, balance sheets, and cash flow statements for operational and investment purposes. Use this skill whenever the user shares financials, asks about margins, profitability, working capital, quality of earnings, revenue bridges, or any historical financial performance. Trigger on phrases like "analyze the financials", "review the P&L", "look at the books", "walk me through the IS", "what does the balance sheet look like", or when financial data (even raw or messy) is pasted into the conversation. Also trigger for NetSuite exports, ROM system outputs, or Excel dumps from the scrap metal business.
---

# Financial Statement Analysis

Paul works in finance within the scrap metal recycling industry (Saskatchewan & Alberta). His stack is NetSuite + ROM (SQL-connected) + Excel + Power BI. He leads FP&A. Analyses may be for internal management reporting, M&A diligence (Diligence OS), or personal investment research.

## Step 1 — Understand the Context

Before diving in, establish:
- **Purpose**: Internal ops review / M&A diligence / investment research?
- **Period**: Monthly, quarterly, annual? YTD?
- **Source**: NetSuite export / ROM / uploaded file / pasted data?
- **Comparators available?**: Prior period, budget, industry benchmarks?

If the purpose isn't stated, default to a **management reporting lens** (ops-focused, variance-driven).

---

## Step 2 — Analytical Framework

Work through the statements in this order: **IS → BS → CF**. Don't just describe — interpret and flag.

### Income Statement
- Revenue: growth rate, mix shift, volume vs. price drivers (critical in scrap — commodity price vs. tonnage)
- Gross margin: absolute and %, trend, any structural changes
- EBITDA bridge: from prior period or budget — volume, price, cost, mix
- Below-the-line: D&A, interest, one-time items — normalize where needed
- Net income quality: is it cash-generative or accounting-driven?

### Balance Sheet
- Working capital: AR days, AP days, inventory turns — flag anything unusual
- Capex vs. D&A: is the business investing or harvesting?
- Leverage: net debt, debt/EBITDA, covenant headroom if known
- Any off-balance-sheet items or related-party items to flag

### Cash Flow Statement
- Cash conversion: EBITDA → FCF walk
- Investing: growth capex vs. maintenance
- Financing: debt draws/repayments, dividends
- Net cash position change and trend

---

## Step 3 — Quality of Earnings Flags

Always run a QoE lens, especially in M&A context. Flag:
- [ ] Revenue recognition issues (timing, one-time contracts)
- [ ] Normalized vs. reported EBITDA (add-backs: owner comp, related party, one-time)
- [ ] Working capital normalization (seasonal peaks, stuffing)
- [ ] Non-recurring items above/below EBITDA
- [ ] Related-party transactions
- [ ] Deferred revenue or expense manipulation

---

## Step 4 — Output Format

Match the output to the stated purpose:

| Purpose | Primary output | Format notes |
|---|---|---|
| Internal ops review | Excel summary + narrative | Dense, numbers-first, variance callouts |
| M&A diligence | Word memo + Excel support | Follow Diligence OS structure (see references/diligence-os-structure.md) |
| Investor research | Word or Notion page | Thesis-linked, comp-contextualized |
| Management presentation | PPTX | 1 slide per statement + summary bridge slide |

### Excel output standards
- Use a clean summary tab: IS / BS / CF side by side with % columns and YoY variances
- Color code: green = favorable, red = unfavorable, grey = neutral/calculated
- Include a KPI dashboard tab with the 8–10 most relevant metrics for the business
- Flag outliers with comments, not just colors

### Word output standards
- Lead with a 1-paragraph executive summary (what matters most)
- Findings in order: Revenue → Margins → Working Capital → Cash → Red Flags
- Use tables for numbers, prose for interpretation
- End with "Key Questions / Open Items" section

---

## Reference Files

- `references/kpi-library.md` — Standard KPIs for scrap metal / recycling businesses
- `references/diligence-os-structure.md` — Diligence OS module structure for M&A context
- `references/netsuite-field-map.md` — Common NetSuite export field names and their meaning

Load a reference file only if the task requires it.
