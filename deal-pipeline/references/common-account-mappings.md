# Common Account Mappings — Quick Reference

Fast lookup for ambiguous source labels → standard accounts. Used by normalization agent.

## Revenue
| Source label | Standard account |
|---|---|
| Net sales, Gross sales, Net revenue | Product/service revenue |
| Service revenue, Fees earned | Service revenue |
| Other revenue, Miscellaneous | Other income |
| Interest income | Interest income (below EBITDA) |
| Gain on sale, Profit on disposal | Gain/loss on asset sales (below EBITDA, one-time) |
| Government grants, Subsidies | Other income (flag — recurring? COVID-era? one-time?) |
| Freight recovery, Freight billed | Net against freight cost, or revenue if consistently billed |

## COGS
| Source label | Standard account |
|---|---|
| Cost of goods sold, COGS | COGS — top level |
| Purchases, Raw materials, Inventory consumed | Direct materials |
| Direct labour, Production wages | Direct labour |
| Freight, Shipping, Delivery | Direct freight |
| Subcontractors (production-related) | Direct subcontract costs |
| Duty, Customs | Direct costs |

## Salaries / Compensation
| Source label | Standard account |
|---|---|
| Wages and salaries | Salaries & wages (non-owner) |
| Owner salary, Shareholder salary | Owner / family compensation |
| Management fees (paid to related entity) | Owner / family compensation — flag as related party |
| Bonuses (to owner/family) | Owner / family compensation |
| Bonuses (to staff) | Salaries & wages (non-owner) |
| Benefits, EI, CPP, WCB | Benefits & payroll taxes |

## Facilities
| Source label | Standard account |
|---|---|
| Rent, Lease payments | Rent / occupancy |
| Property tax | Rent / occupancy or separate line |
| Utilities, Hydro, Gas, Water | Utilities |
| Janitorial, Cleaning | Repairs & maintenance or office |

## Vehicles / Equipment
| Source label | Standard account |
|---|---|
| Fuel, Gasoline, Diesel | Vehicle & fuel |
| Vehicle insurance | Insurance |
| Truck repairs, Auto repairs | Repairs & maintenance |
| Equipment repairs, Maintenance | Repairs & maintenance |
| Small tools, Shop supplies | Repairs & maintenance or office |

## Professional / Admin
| Source label | Standard account |
|---|---|
| Accounting fees, Audit fees | Professional fees |
| Legal fees | Professional fees (flag large amounts — litigation risk?) |
| Consulting fees | Professional fees or owner comp (if related party) |
| Bank charges, NSF fees | Other operating |
| Interest on operating LOC | Interest expense (below EBITDA) |
| Interest on term debt | Interest expense (below EBITDA) |
| Office supplies, Stationery | Office & admin |
| Telephone, Internet | Office & admin or technology |
| Software subscriptions | Technology / software |
| Advertising, Marketing | Advertising & marketing |
| Travel, Meals & entertainment | Office & admin (flag — may be personal) |

## D&A
| Source label | Standard account |
|---|---|
| Depreciation, CCA, Capital cost allowance | Depreciation & amortization |
| Amortization of intangibles | Depreciation & amortization |
| Amortization of ROU asset (IFRS 16) | Depreciation & amortization (note: add back interest on lease liability separately) |

## Below EBITDA / Non-operating
| Source label | Standard account |
|---|---|
| Interest expense, Financing charges | Interest expense |
| Income tax, Corporate tax, Tax provision | Income tax expense |
| Deferred tax | Deferred tax (non-cash — flag) |

## Scrap Metal / Recycling Specific
| Source label | Standard account |
|---|---|
| Metal purchases, Scrap purchases, Buy cost | Direct materials (COGS) |
| Processing, Shredding, Baling | Direct costs (COGS) or operating depending on structure |
| Environmental fees, Eco levy | Direct costs or operating (confirm with management) |
| Royalties | Operating expense |
| Scale maintenance | Repairs & maintenance |
| Yard labour | Direct labour (if production) or wages (if yard staff) |
| Commodity hedging gain/loss | Other income/expense (flag — is this recurring?) |
