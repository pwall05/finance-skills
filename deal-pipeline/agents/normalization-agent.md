# Normalization Agent

Loaded by the deal-pipeline skill at Stage 4. Maps raw extracted line items to the standard chart of accounts template.

---

## Normalization Rules

1. **Never drop a line item** — unclassifiable items go to "Other / unclassified" with the source label preserved
2. **Source label always preserved** — keep a "Source description" column in the output
3. **One standard account per row** — if multiple source rows map to same account, combine and note
4. **Sign convention** — Revenue = positive; all expense items = positive (model subtracts them); losses = show as negative in revenue section
5. **Subtotals are derived** — never copy source subtotals; recalculate from mapped line items
6. **Owner comp isolation** — always break out owner/family compensation into its own line regardless of where it appears in source

---

## Income Statement Mapping

### Standard IS template

```
REVENUE
  Product / service revenue lines (as granular as source allows)
  [Other revenue / miscellaneous]
TOTAL REVENUE

COST OF GOODS SOLD / COST OF SALES
  Direct materials / inventory cost
  Direct labour
  Direct freight / shipping
  [Other direct costs]
TOTAL COGS
GROSS PROFIT

OPERATING EXPENSES
  Salaries & wages (non-owner)
  Owner / family compensation          ← always isolated
  Benefits & payroll taxes
  Rent / occupancy
  Utilities
  Vehicle & fuel
  Repairs & maintenance
  Insurance
  Professional fees (accounting, legal)
  Advertising & marketing
  Office & admin
  Technology / software
  Depreciation & amortization          ← separate from EBITDA calc
  [Other operating expenses]
TOTAL OPERATING EXPENSES
EBIT (Operating income)

OTHER INCOME / EXPENSE
  Interest income
  Interest expense
  Gain / loss on asset sales
  [Other non-operating items]

EBT (Earnings before tax)
  Income tax expense
NET INCOME

── DERIVED METRICS ──
EBITDA = EBIT + D&A
Normalized EBITDA = EBITDA ± QoE add-backs (detail in QoE tab)
```

### Common mapping decisions

| Source label | Standard account | Notes |
|---|---|---|
| Sales, Revenue, Net sales | Product/service revenue | Split if ferrous/non-ferrous detail available |
| Cost of goods, Materials, Purchases | Direct materials/inventory cost | |
| Freight in, Inbound freight | Direct freight | |
| Wages, Salaries (all staff) | Salaries & wages (non-owner) | Split owner comp out separately |
| Owner salary, Shareholder draws, Management fees | Owner / family compensation | Flag for QoE normalization |
| Rent, Lease | Rent / occupancy | Flag if paid to related party |
| Truck expenses, Fleet costs | Vehicle & fuel | |
| Equipment repairs, Maintenance | Repairs & maintenance | Flag large one-time items |
| CCA, Amortization, Depreciation | Depreciation & amortization | |
| Bank charges, Interest on LOC | Interest expense | |
| Other income, Miscellaneous income | Other income | Flag large or unusual items |
| Gain on disposal | Gain / loss on asset sales | One-time — QoE flag |

### Scrap metal business specific mappings

| Source label | Standard account | Notes |
|---|---|---|
| Scrap purchases, Buy price, Metal cost | Direct materials / inventory cost | Core COGS item |
| Processing costs, Shredding, Baling | Direct labour or separate processing line | Depends on nature |
| Outbound freight, Shipping | Direct freight | May be netted in revenue — check |
| Scale fees, Weighing | Direct costs | |
| Environmental levy, Eco fees | Direct costs or operating expense | |
| Royalties | Operating expense | |
| Fuel (processing equipment) | Vehicle & fuel or utilities | Depends on scale |

---

## Balance Sheet Mapping

### Standard BS template

```
CURRENT ASSETS
  Cash & equivalents
  Accounts receivable (trade)
  Allowance for doubtful accounts      ← net against AR
  Inventory
  Prepaid expenses
  Other current assets
TOTAL CURRENT ASSETS

NON-CURRENT ASSETS
  Property, plant & equipment (gross)
  Accumulated depreciation             ← net against PP&E
  Net PP&E
  Intangible assets / goodwill
  Investments / deposits
  Other long-term assets
TOTAL NON-CURRENT ASSETS
TOTAL ASSETS

CURRENT LIABILITIES
  Accounts payable (trade)
  Accrued liabilities
  Current portion of long-term debt
  Income tax payable
  Deferred revenue
  Due to related parties               ← always flag
  Other current liabilities
TOTAL CURRENT LIABILITIES

LONG-TERM LIABILITIES
  Long-term debt / term loan
  Shareholder loans                    ← flag; may be quasi-equity
  Deferred tax liability
  Other long-term liabilities
TOTAL LONG-TERM LIABILITIES
TOTAL LIABILITIES

EQUITY
  Share capital
  Retained earnings
  Current year net income
TOTAL EQUITY

CHECK: Total Assets = Total Liabilities + Equity
```

### Balance sheet notes

- **Shareholder loans**: In Canadian SMBs, very common. Could be owner lending to company (liability) or drawings (effectively equity). Note direction and flag for QoE.
- **Due to/from related parties**: Always flag — could mask real expenses or hide cash
- **Real estate**: If owned real estate is on the BS, note separately — often valued below market and excluded from operating business valuation
- **Finance leases / ROU assets**: Under IFRS 16 / ASPE, leases may be capitalized — note if present

---

## Cash Flow Mapping

### Standard CF template

```
OPERATING ACTIVITIES
  Net income
  Add back: Depreciation & amortization
  Changes in working capital:
    (Increase) / decrease in AR
    (Increase) / decrease in inventory
    (Increase) / decrease in prepaid
    Increase / (decrease) in AP
    Increase / (decrease) in accruals
    [Other WC changes]
  Other non-cash items
NET CASH FROM OPERATIONS

INVESTING ACTIVITIES
  Purchase of PP&E (capex)
  Proceeds from asset disposals
  Acquisitions
  [Other investing]
NET CASH FROM INVESTING

FINANCING ACTIVITIES
  Draws on / repayment of debt
  Shareholder loan advances / repayments
  Dividends / distributions paid
  Share issuances / repurchases
NET CASH FROM FINANCING

NET CHANGE IN CASH
Opening cash balance
CLOSING CASH BALANCE

── DERIVED ──
Maintenance capex estimate: [flag — rarely disclosed; estimate as % of D&A or ask]
Growth capex: Total capex - maintenance capex
Free Cash Flow = Operating CF - Maintenance Capex
FCF Conversion = FCF / EBITDA
```

### Cash flow notes

- **Indirect vs. direct method**: Most SMB statements use indirect (starts from net income). If direct (starts from cash receipts), note it.
- **Maintenance vs. growth capex**: Almost never disclosed separately. Default: assume maintenance = D&A as a proxy, flag this assumption clearly.
- **Missing CF statement**: Very common in NTR / management-prepared FS for SMBs. If absent, reconstruct from IS + BS changes. Flag that it's reconstructed.

---

## QoE Add-Back Tab

Always create a separate QoE tab with this structure:

```
QOE ADD-BACK SCHEDULE
─────────────────────
                              FY2022   FY2023   FY2024
EBITDA (reported)              $X       $X       $X

Add-backs (increases EBITDA):
  Owner comp above market       $X       $X       $X
  Related party rent above mkt  $X       $X       $X
  One-time legal costs          —        $X       —
  Non-recurring restructuring   —        —        $X
  [Other add-backs]

Deductions (decreases EBITDA):
  Owner comp below market       —        —        —
  [Other deductions]

NORMALIZED EBITDA              $X       $X       $X

Notes:
  [Explanation of each add-back, source, and confidence level]
  [Flag any add-backs where data is insufficient to quantify]
```

---

## Normalization Output

Produce a structured Excel with these tabs:
1. `IS_normalized` — Income statement per template above
2. `BS_normalized` — Balance sheet per template above
3. `CF_normalized` — Cash flow per template above (or reconstructed)
4. `QoE_flags` — Add-back schedule + all QoE flags
5. `Source_data` — Raw extracted data, unmodified, with source labels
6. `Period_map` — The period map from Stage 3
7. `Mapping_log` — Each source line item → standard account mapping (audit trail)
