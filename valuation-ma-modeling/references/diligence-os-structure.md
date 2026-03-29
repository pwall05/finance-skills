# Diligence OS — Module Structure

Diligence OS is Paul Wall's professional deal diligence platform for Canadian SMB acquisitions.
All structured diligence outputs follow this 5-module architecture.

## Module 1 — Document Ingestion
- Document inventory (received / missing / flagged)
- File format profiles
- Engagement level (audited / reviewed / compiled / internal)
- Initial red flags on intake

## Module 2 — Financial Analytics
- Normalized IS / BS / CF (3-year + stub if available)
- Adjusted EBITDA build (waterfall)
- Margin analysis vs. benchmarks
- Working capital analysis
- Leverage & coverage ratios
- QoE flag register

## Module 3 — Forward-Looking Model
- Revenue forecast (base / bull / bear)
- Margin assumptions with justification
- Capex plan
- Working capital normalization target
- EBITDA bridge (LTM to forward)

## Module 4 — Acquisition Model
- Entry price scenarios (3x-7x Adj. EBITDA)
- Sources & uses
- Financing structure (senior debt / VTB / equity)
- Returns model (IRR / MOIC at 3yr, 5yr exit)
- Sensitivity table (entry multiple x exit multiple)
- Canadian tax considerations (QSBC, asset vs. share purchase, ACB)

## Module 5 — Risk Register
- Risk item
- Category (financial / operational / market / legal / environmental)
- Severity (High / Medium / Low)
- Probability
- Mitigation / diligence action required
- Owner (Paul / advisor / seller)
- Status (open / in progress / resolved)

---

## Output File Naming Convention
| Module | Output | Location |
|---|---|---|
| 1 | Document_Inventory_[Date].docx | 02_diligence/ |
| 2 | QoE_Memo_[Date].docx | 02_diligence/ |
| 2 | [DealCode]_Normalized_[Date].xlsx | 01_normalized/ |
| 3+4 | [DealCode]_AcqModel_[Date].xlsx | 03_valuation/ |
| 4 | Deal_Memo_[Date].docx | 04_outputs/ |
| 5 | Risk_Register_[Date].xlsx | 02_diligence/ |
