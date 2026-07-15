# Semantic Summary

## Semantic Overview

### Business Subject Area
Cisco bookings and revenue performance analytics.

### Data Domain Description
This schema is a star-schema dimensional model centered on booked order lines. It supports analysis of bookings across time, customers, products, partners, geography, sales representatives, and contracts. The glossary confirms that the model is intended for business-facing analytical reporting rather than transactional processing.

### Business Value Proposition
The model enables a consistent semantic layer for analyzing booking performance, pricing, discounting, contract value, renewal behavior, channel impact, and regional sales outcomes. It supports downstream ontology generation, semantic search, metadata management, and BI/AI use cases.

### Business Capabilities Enabled
- Booking revenue analysis by time, customer, product, partner, and geography
- Renewal and contract value analysis
- Sales performance analysis by representative and team
- Channel and route-to-market reporting
- Product portfolio and technology-domain analysis
- Fiscal and calendar period reporting

### Business Questions
1. What is total booking amount, ACV, and TCV by fiscal quarter, region, and product family?
2. How do renewal bookings and discount percentages vary by customer segment, contract type, and sales team?
3. Which partners, products, and geographies generate the highest booking amounts and quantities over time?

## Semantic Metrics Report

### Validation Summary
- DDL readability: Passed
- Glossary readability: Passed
- Missing tables: None
- Duplicate table definitions: None
- Duplicate glossary terms: None detected from provided glossary structure
- Invalid column definitions: None
- Missing primary keys: None
- Broken foreign keys: None
- Invalid glossary mappings: 2 naming mismatches detected (`geography_key` vs glossary `geo_key`; glossary-only attributes not present in DDL)

### Overall Metrics
| Metric | Value |
|---|---:|
| Total Tables | 8 |
| Fact Tables | 1 |
| Dimension Tables | 7 |
| Lookup Tables | 0 |
| Bridge Tables | 0 |
| Total Columns | 58 |
| Primary Keys | 8 |
| Foreign Keys | 7 |
| Composite Keys | 0 |
| Total Relationships | 7 |
| Total Domains | 4 |
| Total Entities | 8 |
| Total Attributes | 48 |
| Total Measures | 5 |
| Glossary Terms (tables + attributes) | 60 |
| Mapped Terms | 56 |
| Coverage % | 93.3% |
| Unmapped DDL Columns | 1 |
| Unmapped Glossary Terms | 3 |
| Ambiguous Mappings | 0 |
| Duplicate Mappings | 0 |

### Schema Statistics
| Table | Type | Columns | PK Count | FK Count |
|---|---|---:|---:|---:|
| dim_date | Dimension | 6 | 1 | 0 |
| dim_customer | Dimension | 8 | 1 | 0 |
| dim_product | Dimension | 7 | 1 | 0 |
| dim_partner | Dimension | 6 | 1 | 0 |
| dim_geography | Dimension | 4 | 1 | 0 |
| dim_sales_rep | Dimension | 6 | 1 | 0 |
| dim_contract | Dimension | 5 | 1 | 0 |
| fact_bookings | Fact | 16 | 1 | 7 |

### Measures Identified
| Measure | Table | Type | Aggregation |
|---|---|---|---|
| quantity | fact_bookings | Additive | SUM |
| booking_amount_usd | fact_bookings | Additive | SUM |
| acv_usd | fact_bookings | Additive | SUM |
| tcv_usd | fact_bookings | Additive | SUM |
| unit_list_price_usd | fact_bookings | Rate / non-additive | AVG / MIN / MAX |

`discount_pct` is treated as a derived analytical rate and should typically use AVG or weighted discount logic rather than SUM.

### Domain Metrics
| Domain | Entity Count | Attribute Count | Measure Count | Glossary Coverage % | Domain Status |
|---|---:|---:|---:|---:|---|
| Time | 1 | 6 | 0 | 85.7% | Thin |
| Customer & Sales | 3 | 20 | 0 | 100.0% | Healthy |
| Product & Contract | 2 | 12 | 0 | 100.0% | Healthy |
| Bookings Analytics | 2 | 10 | 5 | 92.3% | Healthy |

### Glossary Coverage Statistics
#### Fully mapped tables
- dim_customer
- dim_product
- dim_partner
- dim_sales_rep
- dim_contract
- fact_bookings

#### Partial mappings
- dim_date: glossary contains `day_of_week`, `month_number`, and `calendar_quarter` not present in DDL
- dim_geography / fact_bookings: glossary uses `geo_key`, while DDL uses `geography_key`

#### Unmapped DDL columns
- geography_key (mapped semantically to glossary `geo_key`, but technical name mismatch lowers confidence)

#### Unmapped glossary terms
- day_of_week
- month_number
- calendar_quarter

## Semantic Quality & Reasoning Report

### Design Observations
- The physical model is a clean Kimball-style star schema with a single central fact table and conformed dimensions.
- Grain is explicitly stated: one row per booked order line. This is a strong semantic foundation.
- All dimension joins are single-column surrogate-key foreign keys from the fact table.
- There are no bridge tables or many-to-many constructs in the provided schema.
- The model supports both calendar and fiscal reporting, which is critical for enterprise performance analytics.
- Degenerate dimensions exist in the fact table: `order_number` and `order_line_number`.
- The glossary is strong and table-aligned, but not fully synchronized with the DDL for date and geography naming.

### Reasoning Metrics
| Metric | Value |
|---|---:|
| Deepest relationship chain | 1 hop from fact_bookings to any dimension |
| Orphan tables | 0 |
| Many-to-many relationships | 0 |
| Nullable column percentage | 74.1% |
| Circular relationships | 0 |
| Orphan entities | 0 |

#### Nullable Column Analysis
Only the following are explicitly NOT NULL in the DDL:
- dim_date.full_date
- dim_customer.customer_id
- dim_product.product_id
- dim_partner.partner_id
- dim_sales_rep.rep_id

All primary keys are inherently non-null. Most descriptive and measure fields are nullable, which increases flexibility but requires data quality controls in reporting.

### Glossary Health
| Metric | Value |
|---|---:|
| Total Glossary Terms | 60 |
| Unmapped Glossary Terms | 3 |
| Ambiguous Mappings | 0 |
| Domain Coverage | 93.3% |
| Coverage Gaps | Date attribute enrichment and geography key naming consistency |

### Strong Semantic Domains
- Customer & Sales: clear business meaning, complete glossary alignment, strong analytic usefulness
- Product & Contract: well-defined product and contract descriptors with good semantic clarity
- Bookings Analytics: central fact with explicit business measures and transactional grain

### Weak Semantic Domains
- Time: useful but semantically thinner than glossary expectations because several glossary-described attributes are not in the DDL

### Sparse Modeling / Coverage Gaps
- Date dimension lacks glossary-described attributes `day_of_week`, `month_number`, and `calendar_quarter`
- Geography key naming is inconsistent between DDL (`geography_key`) and glossary (`geo_key`)
- No explicit unique constraints on natural business identifiers such as `customer_id`, `product_id`, `partner_id`, or `rep_id`

### Broken Lineage
No broken referential lineage was detected in the DDL. All declared foreign keys point to existing dimension primary keys.

### Suggested Join Paths
- fact_bookings -> dim_date on `date_key`
- fact_bookings -> dim_customer on `customer_key`
- fact_bookings -> dim_product on `product_key`
- fact_bookings -> dim_partner on `partner_key`
- fact_bookings -> dim_geography on `geography_key`
- fact_bookings -> dim_sales_rep on `sales_rep_key`
- fact_bookings -> dim_contract on `contract_key`

### Recommendations
1. Align glossary and DDL naming by standardizing `geography_key` and `geo_key` to a single preferred term.
2. Extend `dim_date` or revise the glossary so date semantics are fully synchronized.
3. Add explicit business-key uniqueness constraints where operationally appropriate (`customer_id`, `product_id`, `partner_id`, `rep_id`).
4. Define semantic aggregation guidance for `discount_pct` and `unit_list_price_usd` to prevent misuse in BI tools.
5. Consider documenting dimensional hierarchies explicitly, such as Region -> Theater -> Country and Fiscal Year -> Fiscal Quarter -> Date.
6. Introduce data quality checks for nullable measures and foreign keys in `fact_bookings`.

### Data Quality Watch-outs
- Null foreign keys in fact records would reduce analytic completeness by dimension.
- Summing `discount_pct` or `unit_list_price_usd` would be semantically incorrect.
- Missing date attributes may cause reporting teams to recreate inconsistent calendar logic.
- Lack of unique constraints on natural identifiers may allow duplicate dimensional members.
- `is_renewal` is modeled as integer rather than constrained boolean/flag domain.
