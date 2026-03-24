---
name: valuation-ma-modeling
description: Build and review valuation models (DCF, comps, precedent transactions) and M&A deal structures for Canadian SMB acquisitions. Use this skill whenever the user asks about valuing a business, building a DCF, running comparable company analysis, structuring an acquisition, analyzing an LOI, or working within the Diligence OS platform. Also trigger for purchase price discussions, EBITDA multiple benchmarking, returns modeling (IRR, MOIC), synergy analysis, or any "what would we pay for X" conversation. Trigger on phrases like "what's it worth", "build a model", "run the DCF", "what multiple", "diligence on X", or "acquisition model".
---

# Valuation & M&A Modeling

Paul focuses on Canadian SMB acquisitions — typically private companies in industrial/recycling sectors in Saskatchewan and Alberta. The primary platform is **Diligence OS** (a purpose-built deal diligence tool Paul is building). Models are typically built in Excel; memos in Word.

---

## Context: Canadian SMB M&A

- Typical targets: $1M–$20M EBITDA, private, owner-operated
- Common structures: asset purchase (preferred for tax), share purchase
- Valuation range: 3–7x EBITDA for industrial SMBs (adjust for growth, quality, customer concentration)
- Tax considerations: Canadian QSBC, capital gains exemption, earnouts, vendor take-backs (VTBs)
- Financing: senior debt (chartered banks), BDC, seller financing / VTB, equity

---

## Step 1 — Establish the Modeling Purpose

| Mode | What to build |
|---|---|
| Quick sizing | EBITDA multiple range + simple IRR at 3 entry prices |
| Full buy-side model | DCF + comps + returns model in Excel |
| Diligence OS module | Structured financial analytics per Diligence OS schema |
| LOI / term sheet review | Implied multiple, structure analysis, risk flags |
| Synergy model | Standalone + synergy case, value attribution |

---

## Step 2 — Valuation Methodologies

### DCF
- Projection period: 5 years standard (extend to 10 if business has long-cycle capex)
- WACC: For private Canadian SMBs, use 12–18% (higher for smaller/riskier; use CAPM + size premium + company-specific risk)
- Terminal value: Gordon Growth (2–3% terminal growth) or exit multiple method — show both
- Normalize FCF: start from EBITDA, deduct taxes (27% combined federal+provincial SK/AB), D&A, Δ WC, capex
- Sensitivity: always show 2×2 table — WACC vs. terminal growth rate

### Comparable Company Analysis
- Public comps: look for Canadian or US industrial/recycling/distribution comparables
- Key multiples: EV/EBITDA, EV/Revenue, P/E (use EV/EBITDA as primary for private SMBs)
- Apply private company discount: 15–25% for illiquidity/size
- Source benchmarks from: Capital IQ (if available), public filings, industry reports

### Precedent Transactions
- Search Canadian industrial sector M&A, past 5 years
- Key metrics: EV/EBITDA paid, deal structure (asset vs. share), earnout prevalence
- Note: Canadian private deal data is thin — supplement with US comparable sector transactions

### Returns Model (for acquisition)
- Entry: purchase price + transaction costs (1.5–2% of deal value)
- Financing: model debt schedule with amortization
- Exit: 5-year hold, exit at entry multiple (base), ±1x (bull/bear)
- Returns: IRR and MOIC at each scenario
- Minimum return threshold: 20%+ IRR for SMB buyout

---

## Step 3 — Diligence OS Integration

When working within Diligence OS context, structure outputs to match the platform's modules:

1. **Document Ingestion** — flag what financial documents are available and what's missing
2. **Financial Analytics** — normalized EBITDA build, historical trends, QoE flags
3. **Forward-Looking Model** — projection assumptions, scenario cases
4. **Acquisition Model** — entry price, structure, returns
5. **Risk Register** — key risks, mitigants, deal-breakers

See `references/diligence-os-structure.md` for full module schema.

---

## Step 4 — Output Standards

### Excel Model
Structure tabs in this order:
1. **Cover** — deal name, date, model version, key assumptions summary
2. **Inputs** — all assumptions centralized (yellow cells = inputs)
3. **Historical** — 3–5 years normalized financials
4. **Projections** — 5-year P&L, BS, CF
5. **DCF** — WACC build, FCF, terminal value, sensitivity
6. **Comps** — comparable company table with multiples
7. **Returns** — acquisition structure, debt schedule, IRR/MOIC
8. **Summary** — 1-page dashboard: valuation range, football field, key assumptions

### Word Memo
- 1-page executive summary: recommendation, price range, key risks
- Section 2: Business overview (1–2 pages)
- Section 3: Financial analysis (reference Excel; don't re-table everything)
- Section 4: Valuation (methodology, outputs, football field)
- Section 5: Deal structure considerations
- Section 6: Key risks and open items

---

## Reference Files

- `references/diligence-os-structure.md` — Full Diligence OS module schema
- `references/canadian-sme-benchmarks.md` — Valuation multiples and deal structure norms for Canadian SMBs
- `references/wacc-build.md` — Step-by-step WACC calculation for private Canadian companies
