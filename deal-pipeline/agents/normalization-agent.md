# Normalization Agent

## Role
Map every source line item to the standard IS/BS/CF template. Log every mapping. Flag every item that requires judgment.

## Inputs Required
- Confirmed Period Map (from structure-detective)
- Common account mappings (from references/common-account-mappings.md)
- Raw financial data

## Standard Income Statement Template

```
REVENUE
  Gross Revenue
  Less: Returns & Allowances
Net Revenue

COST OF GOODS SOLD
  Direct Labour
  Materials & Supplies
  Subcontractors
  Equipment Operating Costs
  Fuel
  Other Direct Costs
Total COGS

GROSS PROFIT
Gross Margin %

OPERATING EXPENSES
  Compensation — Owner/Shareholder [FLAG ALWAYS]
  Compensation — Management & Admin
  Rent & Occupancy
  Insurance
  Professional Fees
  Vehicle & Equipment (non-COGS)
  Travel & Entertainment
  Advertising & Marketing
  Telephone & Utilities
  Bad Debt Expense
  Other Operating Expenses
Total Operating Expenses

EBITDA
  Less: Depreciation & Amortization
EBIT
  Less: Interest Expense
  Add: Interest Income
EBT
  Less: Income Tax Provision
NET INCOME
```

## Standard Balance Sheet Template

```
ASSETS
Current Assets
  Cash & Cash Equivalents
  Accounts Receivable (trade)
  Inventory
  Prepaid Expenses & Deposits
  Due from Related Parties [FLAG]
  Other Current Assets
Total Current Assets

Non-Current Assets
  PPE — Gross
  Less: Accumulated Depreciation
Net PPE
  Intangibles / Goodwill
  Other Long-Term Assets
Total Non-Current Assets
TOTAL ASSETS

LIABILITIES
Current Liabilities
  Accounts Payable
  Accrued Liabilities
  Current Portion LTD
  Income Taxes Payable
  Due to Related Parties [FLAG]
  Other Current Liabilities
Total Current Liabilities

Long-Term Liabilities
  Long-Term Debt (net of current)
  Other Long-Term Liabilities
Total Long-Term Liabilities
TOTAL LIABILITIES

EQUITY
  Share Capital
  Retained Earnings
Total Equity
TOTAL LIABILITIES & EQUITY
```

## Normalization Rules
1. Map first, flag second — map every account before adjusting
2. Log everything — every mapping goes into Mapping_log tab
3. Judgment items get a flag — ambiguous mappings go to QoE_flags
4. Never delete source data — Source_data tab preserved unmodified
5. Owner comp always flagged regardless of amount
