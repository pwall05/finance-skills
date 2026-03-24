# Diligence OS — Module Structure

Reference schema for structuring outputs within Paul's Diligence OS platform (Canadian SMB acquisition diligence tool).

## Platform Overview

Diligence OS is a professional-grade investment banking / private equity due diligence platform targeting Canadian SMB acquisitions. It has five core modules:

---

## Module 1: Document Ingestion & Normalization

**Purpose**: Ingest and normalize source documents into structured data.

**Inputs accepted**:
- Financial statements (PDF, Excel, Word)
- Tax returns (T2, personal if provided)
- Management-prepared financials
- CIM / teaser / information memorandum

**Output structure**:
```
/documents
  /raw          — Original files as uploaded
  /normalized   — Standardized Excel template (IS, BS, CF)
  /flags        — Document quality issues, gaps, inconsistencies
```

**Key tasks**:
- Map source line items to standardized chart of accounts
- Flag missing periods, restatements, or auditor qualifications
- Note accounting policies (cash vs. accrual, depreciation methods)

---

## Module 2: Financial Analytics

**Purpose**: Historical financial analysis — the "what happened" module.

**Standard outputs**:
- 3–5 year normalized IS, BS, CF (Excel)
- EBITDA bridge (YoY and vs. budget if available)
- Working capital analysis
- KPI trend table (revenue/tonne, margin %, WC days, etc.)
- Quality of Earnings memo (Word)

**QoE add-back categories**:
1. Owner compensation normalization (above/below market)
2. Related-party transactions (rent, management fees)
3. One-time items (legal, restructuring, non-recurring capex)
4. Non-cash items (stock comp, non-cash write-offs)
5. Revenue timing / recognition issues

---

## Module 3: Forward-Looking Model

**Purpose**: Project the business forward — the "what could happen" module.

**Standard scenarios**:
- **Base**: Management plan with modest haircut (−5–10% to revenue, assume cost inflation)
- **Bull**: Management plan largely achieved; volume + price expansion
- **Bear**: Revenue decline scenario (−15–25%); stress test to cash breakeven

**Projection period**: 5 years standard

**Key assumptions to document**:
- Revenue drivers (volume growth, price assumptions, new contracts)
- Margin assumptions (commodity price passthrough, labour, fuel)
- Capex: maintenance vs. growth split
- Working capital: normalized days as % of revenue

---

## Module 4: Acquisition Model

**Purpose**: Structure the deal and test returns.

**Components**:
- Enterprise value range (from Module 2 valuation)
- Sources & uses of funds
- Debt schedule (senior, VTB, any mezzanine)
- Returns model (IRR / MOIC at 3 entry prices × 3 exit multiples)
- Sensitivity: returns vs. EBITDA growth × exit multiple

**Standard deal structure template**:
```
Purchase price:          $X
  Less: Net debt         ($X)
  Less: NWC adjustment   ($X)
Equity value:            $X

Financed by:
  Senior debt:           $X  (___x EBITDA)
  VTB:                   $X  (___%)
  Equity:                $X  (___%)
```

---

## Module 5: Risk Register

**Purpose**: Catalogue, score, and track deal risks.

**Risk categories**:
1. Financial (earnings quality, leverage, WC)
2. Operational (key person, customer concentration, supplier dependency)
3. Environmental (contamination, regulatory, site liability)
4. Legal (litigation, IP, contracts, employment)
5. Market (commodity price, competitive dynamics)
6. Integration (post-close execution)

**Risk scoring**: High / Medium / Low × Likelihood × Mitigant available Y/N

**Output**: Word table or Notion database with risk, score, mitigant, owner, status.
