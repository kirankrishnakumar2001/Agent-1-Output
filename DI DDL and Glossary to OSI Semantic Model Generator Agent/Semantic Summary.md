# Semantic Summary

## Semantic Overview

### Business Subject Area
Cisco Bookings analytics for order-line level booking transactions in a Kimball-style star schema.

### Data Domain Description
This dataset models customer bookings by linking a central bookings fact table to descriptive dimensions for date, customer, product, partner, geography, sales representative, and contract. It supports commercial, financial, channel, product, customer, and geographic analysis.

### Business Value Proposition
The semantic model enables consistent business analysis of booking performance, contract value, pricing, discounting, channel contribution, customer segmentation, sales execution, and regional trends.

### Business Capabilities Enabled
- Booking revenue analysis by time, customer, product, partner, geography, and sales organization
- Annual and total contract value reporting
- Renewal and booking type analysis
- Discount and pricing analysis
- Customer segment and industry performance tracking
- Channel and route-to-market effectiveness analysis
- Sales team and representative performance analysis

### Business Questions
1. What is total booking amount, ACV, and TCV by fiscal quarter, region, and product family?
2. Which customer segments, industries, and partner tiers generate the highest booking revenue and quantity?
3. How do renewal bookings and discount percentages vary by contract type, sales team, and route to market?

## Semantic Metrics Report

### Validation Summary
- DDL readability: Passed
- Glossary readability: Passed
- Missing tables: None detected
- Duplicate table definitions: None detected
- Duplicate glossary terms: None detected at table/attribute name level in supplied glossary text
- Invalid column definitions: None detected
- Missing primary keys: None detected
- Broken foreign keys: None detected in DDL
- Invalid glossary mappings: Warning on geography key naming mismatch (`geography_key` in DDL vs `geo_key` in glossary for `dim_geography` and `fact_bookings`)
- Additional warning: Glossary contains attributes not present in DDL (`day_of_week`, `month_number`, `calendar_quarter` in `dim_date`)

### Overall Metrics
| Metric | Value |
|---|---:|
| Total Tables | 8 |
| Total Fact Tables | 1 |
| Total Dimension Tables | 7 |
| Total Columns | 57 |
| Total Primary Keys | 8 |
| Total Foreign Keys | 7 |
| Total Relationships | 7 |
| Total Domains | 8 |
| Total Measures | 6 |
| Glossary Terms (table + attribute entries represented in source) | 62 |
| Mapped Terms | 58 |
| Coverage % | 93.5% |
| Unmapped Columns | 0 |
| Unmapped Glossary Terms | 4 |
| Ambiguous Mappings | 2 |

### Schema Statistics
- Composite Keys: None explicitly defined
- Lookup Tables: All dimensions function as lookup/reference structures; strongest lookup/reference candidates are `dim_date`, `dim_geography`, `dim_contract`
- Bridge Tables: None detected
- Fact Tables: `fact_bookings`
- Dimension Tables: `dim_date`, `dim_customer`, `dim_product`, `dim_partner`, `dim_geography`, `dim_sales_rep`, `dim_contract`
- Degenerate Dimensions in fact: `order_number`, `order_line_number`
- Nullable Column Percentage: 43 / 57 = 75.4% potentially nullable based on absence of `NOT NULL` constraint
- Deepest Relationship Chain: 2 hops in the star pattern (dimension -> fact -> dimension)
- Many-to-many Relationships: None physically modeled; no bridge tables detected
- Orphan Tables: None

### Per-Domain Metrics
| Domain | Entity Count | Attribute Count | Measure Count | Glossary Coverage % | Domain Status |
|---|---:|---:|---:|---:|---|
| Booking Transactions | 1 | 18 | 6 | 100% | Healthy |
| Date & Fiscal Calendar | 1 | 7 | 0 | 70.0% | Thin |
| Customer Management | 1 | 8 | 0 | 100% | Healthy |
| Product Portfolio | 1 | 7 | 0 | 100% | Healthy |
| Partner Channel | 1 | 6 | 0 | 100% | Healthy |
| Geography | 1 | 4 | 0 | 50.0% | Thin |
| Sales Organization | 1 | 6 | 0 | 100% | Healthy |
| Contract Management | 1 | 5 | 0 | 100% | Healthy |

### Coverage Statistics
#### Explicitly Unmapped Glossary Terms
- `dim_date.day_of_week`
- `dim_date.month_number`
- `dim_date.calendar_quarter`
- `dim_geography.geo_key` / `fact_bookings.geo_key` do not exactly match DDL technical name `geography_key`

#### Ambiguous or Low-Friction Mappings
- `dim_geography.geography_key` inferred from glossary term `geo_key` — confidence 0.84
- `fact_bookings.geography_key` inferred from glossary term `geo_key` — confidence 0.84

## Semantic Quality & Reasoning Report

### Design Observations
- The physical model is a clean star schema centered on `fact_bookings` at the grain of one booked order line.
- Surrogate keys are consistently used across dimensions, supporting dimensional analytics patterns.
- The fact table contains a balanced set of additive measures (`quantity`, `booking_amount_usd`, `acv_usd`, `tcv_usd`) and non-additive/semi-additive rate-style measures (`unit_list_price_usd`, `discount_pct`).
- Two degenerate dimensions (`order_number`, `order_line_number`) are retained in the fact table, which is appropriate for transaction traceability.
- Date modeling includes both calendar and fiscal attributes, which is valuable for financial and operational reporting.
- Geography and date glossary content are richer than the DDL, suggesting either an incomplete physical implementation or future semantic extensions.

### Glossary Health
- Glossary coverage is strong overall.
- Most DDL columns have a direct glossary match or a highly confident semantic match.
- Coverage gaps are concentrated in naming inconsistency rather than meaning inconsistency.
- Domain coverage is complete at the table level across all eight discovered domains.
- Weakest glossary alignment occurs in Geography and Date due to extra glossary attributes and key-name mismatch.

### Strong Semantic Domains
- Booking Transactions
- Customer Management
- Product Portfolio
- Partner Channel
- Sales Organization
- Contract Management

### Weak Semantic Domains
- Geography: key naming mismatch reduces exact-match quality
- Date & Fiscal Calendar: glossary includes expected but physically absent date attributes

### Sparse Modeling
- No bridge tables or additional snowflaked hierarchies are present.
- Geographic hierarchy is minimal (`region`, `theater`, `country`).
- Customer-geography linkage is descriptive only and not normalized into separate relationships.

### Broken Lineage
- No broken physical lineage detected.
- Semantic lineage warning: glossary references `geo_key` while DDL uses `geography_key`.

### Suggested Join Paths
- `fact_bookings.date_key -> dim_date.date_key`
- `fact_bookings.customer_key -> dim_customer.customer_key`
- `fact_bookings.product_key -> dim_product.product_key`
- `fact_bookings.partner_key -> dim_partner.partner_key`
- `fact_bookings.geography_key -> dim_geography.geography_key`
- `fact_bookings.sales_rep_key -> dim_sales_rep.sales_rep_key`
- `fact_bookings.contract_key -> dim_contract.contract_key`
- Analytical join path example: `fact_bookings -> dim_customer -> analyze by segment/industry`
- Analytical join path example: `fact_bookings -> dim_product -> analyze by product_family/technology_domain`
- Analytical join path example: `fact_bookings -> dim_date -> analyze by fiscal_year/fiscal_quarter`

### Data Quality Watch-outs
- High nullable-column ratio may lead to incomplete dimensional slicing and inconsistent aggregations.
- `is_renewal` is stored as integer rather than explicit boolean; semantic interpretation should standardize allowed values.
- `discount_pct` should be validated for allowed ranges.
- `unit_list_price_usd` is a rate and should not be blindly summed.
- `booking_amount_usd`, `acv_usd`, and `tcv_usd` should be reconciled with contract terms and booking type rules.
- Natural business keys such as `customer_id`, `product_id`, `partner_id`, and `rep_id` are not constrained as unique in the DDL.

### Recommendations
- Standardize glossary and DDL naming for geography key (`geo_key` vs `geography_key`).
- Add missing date attributes to the DDL if required by reporting: `day_of_week`, `month_number`, `calendar_quarter`.
- Consider explicit uniqueness constraints on natural identifiers for key dimensions.
- Define semantic aggregation rules for measures: additive, non-additive, and ratio/percentage handling.
- Document valid code lists for `booking_type`, `offer_type`, `partner_type`, `route_to_market`, `sales_role`, and `coverage_level`.
- Add data quality rules for numeric measures and renewal flags.
- Preserve inference metadata for downstream ontology generation, especially where technical and business names differ.
