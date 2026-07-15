# OSI Semantic Model

## Model Scope
- Source DDL: `sql.txt`
- Source Glossary: `Glossary.txt`
- Modeling basis: explicit DDL structures enriched with glossary semantics
- Inference policy: glossary-preferred business naming, with inferred semantics clearly marked

## Validation Warnings
- Glossary/DDL naming mismatch: `dim_geography` glossary uses `geo_key` while DDL uses `geography_key`.
- Missing glossary mappings for DDL column `geography_key` are resolved by inferred mapping to `geo_key` with medium confidence.
- Glossary terms not represented in DDL: `day_of_week`, `month_number`, `calendar_quarter`.
- No duplicate entities, duplicate measures, circular relationships, missing keys, invalid semantic domains, or orphan entities detected.

## Domain: Time
**Description:** Calendar and fiscal time semantics used to analyze bookings across reporting periods.
**Business Purpose:** Enable period-based analytics across day, month, quarter, and fiscal cycles.
**Inference Status:** Explicit domain from glossary semantics and dimension naming.

### Entity: Date
- **Technical Table:** `dim_date`
- **Description:** Stores calendar and fiscal date information used for analyzing bookings across different time periods.
- **Entity Type:** Dimension
- **Business Keys:** None explicitly declared; `date_key` is a surrogate key representing YYYYMMDD.
- **Primary Key:** `date_key`
- **Hierarchies:**
  - Inferred: Calendar Year -> Month Name -> Full Date
  - Inferred: Fiscal Year -> Fiscal Quarter -> Full Date
- **Reference Data:** Month and fiscal period descriptors

#### Attributes
| Business Name | Technical Name | Definition | Data Type | Role | Glossary Mapping | Confidence |
|---|---|---|---|---|---|---:|
| Date Key | date_key | Unique surrogate key representing a calendar date. Used to join the Date dimension with fact tables. | INTEGER | Key | Explicit | 1.00 |
| Full Date | full_date | Complete calendar date associated with a transaction or booking. | DATE | Attribute | Explicit | 1.00 |
| Month Name | month_name | Name of the calendar month. | VARCHAR(12) | Attribute | Explicit | 1.00 |
| Calendar Year | calendar_year | Calendar year associated with the date. | INTEGER | Attribute | Explicit | 1.00 |
| Fiscal Year | fiscal_year | Fiscal year according to the organization's accounting calendar. | VARCHAR(6) | Attribute | Explicit | 1.00 |
| Fiscal Quarter | fiscal_quarter | Fiscal quarter according to the organization's fiscal calendar. | VARCHAR(10) | Attribute | Explicit | 1.00 |
| Fiscal Period Sequence | fiscal_period_seq | Sequential fiscal accounting period used for financial reporting. | INTEGER | Attribute | Explicit | 1.00 |

#### Missing Glossary Attributes Not Present in DDL
| Glossary Term | Definition | Status |
|---|---|---|
| day_of_week | Name of the weekday on which the date falls. | Glossary-only |
| month_number | Numeric representation of the calendar month (1–12). | Glossary-only |
| calendar_quarter | Calendar quarter in which the date falls. | Glossary-only |

## Domain: Customer & Sales
**Description:** Customer, partner, geography, and sales workforce semantics used to analyze commercial performance.
**Business Purpose:** Support customer segmentation, channel analysis, territory reporting, and sales accountability.
**Inference Status:** Inferred from table names, glossary domains, and fact-table foreign key clustering.

### Entity: Customer
- **Technical Table:** `dim_customer`
- **Description:** Contains descriptive information about customers who purchase products or services.
- **Entity Type:** Dimension
- **Business Keys:** `customer_id` (business identifier; inferred natural key)
- **Primary Key:** `customer_key`
- **Hierarchies:**
  - Inferred: HQ Region -> HQ Country -> Customer
  - Inferred: Segment -> Account Tier -> Customer

#### Attributes
| Business Name | Technical Name | Definition | Data Type | Role | Glossary Mapping | Confidence |
|---|---|---|---|---|---|---:|
| Customer Key | customer_key | Surrogate key uniquely identifying a customer within the data warehouse. | INTEGER | Key | Explicit | 1.00 |
| Customer ID | customer_id | Business identifier assigned to a customer. | VARCHAR(20) | Business Key | Explicit | 1.00 |
| Customer Name | customer_name | Official name of the customer organization. | VARCHAR(80) | Attribute | Explicit | 1.00 |
| Segment | segment | Business segment to which the customer belongs. | VARCHAR(30) | Dimension Attribute | Explicit | 1.00 |
| Industry | industry | Industry classification of the customer. | VARCHAR(40) | Dimension Attribute | Explicit | 1.00 |
| Account Tier | account_tier | Strategic classification of the customer based on business importance. | VARCHAR(20) | Dimension Attribute | Explicit | 1.00 |
| Headquarters Country | hq_country | Country where the customer's headquarters is located. | VARCHAR(40) | Dimension Attribute | Explicit | 1.00 |
| Headquarters Region | hq_region | Geographic region where the customer's headquarters is located. | VARCHAR(20) | Dimension Attribute | Explicit | 1.00 |

### Entity: Partner
- **Technical Table:** `dim_partner`
- **Description:** Stores information about channel partners involved in selling products.
- **Entity Type:** Dimension
- **Business Keys:** `partner_id` (inferred natural key)
- **Primary Key:** `partner_key`
- **Hierarchies:**
  - Inferred: Route to Market -> Partner Type -> Partner Tier -> Partner

#### Attributes
| Business Name | Technical Name | Definition | Data Type | Role | Glossary Mapping | Confidence |
|---|---|---|---|---|---|---:|
| Partner Key | partner_key | Surrogate key uniquely identifying a partner. | INTEGER | Key | Explicit | 1.00 |
| Partner ID | partner_id | Business identifier assigned to the partner organization. | VARCHAR(20) | Business Key | Explicit | 1.00 |
| Partner Name | partner_name | Name of the partner organization. | VARCHAR(60) | Attribute | Explicit | 1.00 |
| Partner Type | partner_type | Classification of the partner such as Distributor, Reseller, or Service Provider. | VARCHAR(30) | Dimension Attribute | Explicit | 1.00 |
| Partner Tier | partner_tier | Partner program level indicating certification or status. | VARCHAR(30) | Dimension Attribute | Explicit | 1.00 |
| Route to Market | route_to_market | Sales channel through which products are sold to customers. | VARCHAR(20) | Dimension Attribute | Explicit | 1.00 |

### Entity: Geography
- **Technical Table:** `dim_geography`
- **Description:** Provides hierarchical geographic information for analyzing bookings across global regions.
- **Entity Type:** Dimension
- **Business Keys:** None explicitly declared
- **Primary Key:** `geography_key`
- **Hierarchies:**
  - Explicit/Inferred: Region -> Theater -> Country

#### Attributes
| Business Name | Technical Name | Definition | Data Type | Role | Glossary Mapping | Confidence |
|---|---|---|---|---|---|---:|
| Geography Key | geography_key | Surrogate key identifying a geographic location. Inferred mapping to glossary term `geo_key`. | INTEGER | Key | Inferred to `geo_key` | 0.72 |
| Region | region | Highest-level geographic reporting region. | VARCHAR(20) | Dimension Attribute | Explicit | 1.00 |
| Theater | theater | Sales theater within a region. | VARCHAR(30) | Dimension Attribute | Explicit | 1.00 |
| Country | country | Country associated with the booking or customer. | VARCHAR(40) | Dimension Attribute | Explicit | 1.00 |

### Entity: Sales Representative
- **Technical Table:** `dim_sales_rep`
- **Description:** Contains information about sales representatives responsible for customer bookings.
- **Entity Type:** Dimension
- **Business Keys:** `rep_id` (inferred natural key)
- **Primary Key:** `sales_rep_key`
- **Hierarchies:**
  - Inferred: Sales Team -> Sales Role -> Sales Representative
  - Inferred: Segment Covered -> Sales Team -> Sales Representative

#### Attributes
| Business Name | Technical Name | Definition | Data Type | Role | Glossary Mapping | Confidence |
|---|---|---|---|---|---|---:|
| Sales Representative Key | sales_rep_key | Surrogate key identifying a sales representative. | INTEGER | Key | Explicit | 1.00 |
| Representative ID | rep_id | Business identifier assigned to the sales representative. | VARCHAR(20) | Business Key | Explicit | 1.00 |
| Representative Name | rep_name | Full name of the sales representative. | VARCHAR(60) | Attribute | Explicit | 1.00 |
| Sales Role | sales_role | Functional role performed by the sales representative. | VARCHAR(40) | Dimension Attribute | Explicit | 1.00 |
| Sales Team | sales_team | Sales organization or team to which the representative belongs. | VARCHAR(40) | Dimension Attribute | Explicit | 1.00 |
| Segment Covered | segment_covered | Customer segment managed by the sales representative. | VARCHAR(30) | Dimension Attribute | Explicit | 1.00 |

## Domain: Product & Contract
**Description:** Product portfolio and contractual semantics associated with booked transactions.
**Business Purpose:** Support product mix analysis, technology-domain reporting, offer analysis, and contract valuation context.
**Inference Status:** Inferred from glossary content and table clustering.

### Entity: Product
- **Technical Table:** `dim_product`
- **Description:** Contains descriptive information about products and services sold through bookings.
- **Entity Type:** Dimension
- **Business Keys:** `product_id` (business identifier / SKU; inferred natural key)
- **Primary Key:** `product_key`
- **Hierarchies:**
  - Inferred: Business Entity -> Technology Domain -> Product Family -> Product
  - Inferred: Offer Type -> Product Family -> Product

#### Attributes
| Business Name | Technical Name | Definition | Data Type | Role | Glossary Mapping | Confidence |
|---|---|---|---|---|---|---:|
| Product Key | product_key | Surrogate key uniquely identifying a product record. | INTEGER | Key | Explicit | 1.00 |
| Product ID | product_id | Business identifier or SKU assigned to a product. | VARCHAR(30) | Business Key | Explicit | 1.00 |
| Product Name | product_name | Commercial name of the product or service. | VARCHAR(80) | Attribute | Explicit | 1.00 |
| Product Family | product_family | Group of related products sharing common characteristics. | VARCHAR(30) | Dimension Attribute | Explicit | 1.00 |
| Technology Domain | technology_domain | Technology category to which the product belongs. | VARCHAR(40) | Dimension Attribute | Explicit | 1.00 |
| Offer Type | offer_type | Commercial offering category such as Hardware, Software, Subscription, or Services. | VARCHAR(30) | Dimension Attribute | Explicit | 1.00 |
| Business Entity | business_entity | Business unit responsible for the product portfolio. | VARCHAR(30) | Dimension Attribute | Explicit | 1.00 |

### Entity: Contract
- **Technical Table:** `dim_contract`
- **Description:** Stores information describing customer contracts associated with bookings.
- **Entity Type:** Dimension
- **Business Keys:** None explicitly declared
- **Primary Key:** `contract_key`
- **Hierarchies:**
  - Inferred: Contract Type -> Coverage Level -> Contract

#### Attributes
| Business Name | Technical Name | Definition | Data Type | Role | Glossary Mapping | Confidence |
|---|---|---|---|---|---|---:|
| Contract Key | contract_key | Surrogate key identifying a contract. | INTEGER | Key | Explicit | 1.00 |
| Contract Type | contract_type | Category of customer contract such as Subscription, Support, or Service Agreement. | VARCHAR(40) | Dimension Attribute | Explicit | 1.00 |
| Term Months | term_months | Duration of the contract expressed in months. | INTEGER | Attribute | Explicit | 1.00 |
| Auto Renew Flag | auto_renew_flag | Indicates whether the contract automatically renews upon expiration. | CHAR(1) | Attribute | Explicit | 1.00 |
| Coverage Level | coverage_level | Level of service or support included in the contract. | VARCHAR(20) | Dimension Attribute | Explicit | 1.00 |

## Domain: Bookings Analytics
**Description:** Central booking transaction semantics and booking performance measures.
**Business Purpose:** Measure booked revenue, unit volume, pricing, discounting, annualized value, and total contract value.
**Inference Status:** Explicit from fact grain statement and glossary definitions.

### Entity: Booking
- **Technical Table:** `fact_bookings`
- **Description:** Stores measurable business transactions representing customer bookings. Each record corresponds to a booked order line and contains revenue, quantity, pricing, and references to related dimensions.
- **Entity Type:** Fact
- **Grain:** One row per booked order line
- **Primary Key:** `booking_id`
- **Business Keys:** None explicitly declared; `order_number` + `order_line_number` behaves as an inferred degenerate transaction identifier.
- **Degenerate Dimensions:** `order_number`, `order_line_number`

#### Attributes
| Business Name | Technical Name | Definition | Data Type | Role | Glossary Mapping | Confidence |
|---|---|---|---|---|---|---:|
| Booking ID | booking_id | Unique identifier for a booking transaction. | INTEGER | Key | Explicit | 1.00 |
| Order Number | order_number | Sales order number associated with the booking. | VARCHAR(20) | Degenerate Dimension | Explicit | 1.00 |
| Order Line Number | order_line_number | Line number within the sales order. | INTEGER | Degenerate Dimension | Explicit | 1.00 |
| Date Key | date_key | Foreign key referencing the booking date in the Date dimension. | INTEGER | Foreign Key | Explicit | 1.00 |
| Customer Key | customer_key | Foreign key referencing the customer associated with the booking. | INTEGER | Foreign Key | Explicit | 1.00 |
| Product Key | product_key | Foreign key referencing the booked product. | INTEGER | Foreign Key | Explicit | 1.00 |
| Partner Key | partner_key | Foreign key referencing the selling partner. | INTEGER | Foreign Key | Explicit | 1.00 |
| Geography Key | geography_key | Foreign key referencing the geographic location associated with the booking. Inferred alignment to glossary `geo_key`. | INTEGER | Foreign Key | Inferred to `geo_key` | 0.72 |
| Sales Representative Key | sales_rep_key | Foreign key referencing the responsible sales representative. | INTEGER | Foreign Key | Explicit | 1.00 |
| Contract Key | contract_key | Foreign key referencing the associated customer contract. | INTEGER | Foreign Key | Explicit | 1.00 |
| Booking Type | booking_type | Indicates the type of booking transaction, such as New, Renewal, or Expansion. | VARCHAR(15) | Attribute | Explicit | 1.00 |
| Is Renewal | is_renewal | Indicates whether the booking represents a contract renewal. | INTEGER | Attribute / Flag | Explicit | 1.00 |

#### Measures
| Measure Name | Technical Name | Definition | Data Type | Aggregation Type | Semantic Type | Glossary Mapping | Confidence |
|---|---|---|---|---|---|---|---:|
| Quantity | quantity | Number of product units included in the booking. | INTEGER | SUM | Additive | Explicit | 1.00 |
| Unit List Price USD | unit_list_price_usd | Standard list price per product unit before discounts. | DECIMAL(12,2) | AVG / MIN / MAX | Non-additive rate | Explicit | 1.00 |
| Discount Percentage | discount_pct | Percentage discount applied to the standard list price. | DECIMAL(5,2) | AVG or weighted logic | Non-additive percentage | Explicit | 1.00 |
| Booking Amount USD | booking_amount_usd | Net booking revenue after applying discounts. | DECIMAL(14,2) | SUM | Additive | Explicit | 1.00 |
| Annual Contract Value USD | acv_usd | Annual Contract Value associated with the booking. | DECIMAL(14,2) | SUM | Additive | Explicit | 1.00 |
| Total Contract Value USD | tcv_usd | Total Contract Value over the full duration of the contract. | DECIMAL(14,2) | SUM | Additive | Explicit | 1.00 |

## Semantic Relationships
| Relationship Name | Parent Entity | Child Entity | Technical Join | Cardinality | Relationship Type | Basis | Confidence |
|---|---|---|---|---|---|---|---:|
| Booking occurs on Date | Date | Booking | fact_bookings.date_key = dim_date.date_key | Many-to-One from Booking to Date | Fact-to-Dimension | Foreign Key | 1.00 |
| Booking is for Customer | Customer | Booking | fact_bookings.customer_key = dim_customer.customer_key | Many-to-One from Booking to Customer | Fact-to-Dimension | Foreign Key | 1.00 |
| Booking includes Product | Product | Booking | fact_bookings.product_key = dim_product.product_key | Many-to-One from Booking to Product | Fact-to-Dimension | Foreign Key | 1.00 |
| Booking is sold through Partner | Partner | Booking | fact_bookings.partner_key = dim_partner.partner_key | Many-to-One from Booking to Partner | Fact-to-Dimension | Foreign Key | 1.00 |
| Booking is associated with Geography | Geography | Booking | fact_bookings.geography_key = dim_geography.geography_key | Many-to-One from Booking to Geography | Fact-to-Dimension | Foreign Key with inferred glossary alignment | 0.95 |
| Booking is owned by Sales Representative | Sales Representative | Booking | fact_bookings.sales_rep_key = dim_sales_rep.sales_rep_key | Many-to-One from Booking to Sales Representative | Fact-to-Dimension | Foreign Key | 1.00 |
| Booking is governed by Contract | Contract | Booking | fact_bookings.contract_key = dim_contract.contract_key | Many-to-One from Booking to Contract | Fact-to-Dimension | Foreign Key | 1.00 |

## Semantic Mapping Summary
### Explicit mappings
Most table and column mappings are explicit because the glossary mirrors the DDL structure.

### Inferred mappings
| Technical Name | Preferred Business Term | Reason | Confidence |
|---|---|---|---:|
| geography_key | Geography Key / geo_key | Naming mismatch between DDL and glossary; semantics clearly equivalent | 0.72 |
| order_number + order_line_number | Booking line transaction identifier | Inferred degenerate business transaction grain from fact-table comments | 0.88 |
| customer_id | Customer business key | Comment and glossary indicate business identifier | 0.96 |
| product_id | Product business key | Comment and glossary indicate business identifier / SKU | 0.96 |
| partner_id | Partner business key | Glossary indicates business identifier | 0.95 |
| rep_id | Sales representative business key | Glossary indicates business identifier | 0.95 |

## Semantic Validation Result
- Missing glossary mappings: limited to naming mismatch and glossary-only date fields
- Duplicate entities: None
- Duplicate measures: None
- Circular relationships: None
- Missing keys: None
- Invalid semantic domains: None
- Orphan entities: None

## Reusable Downstream Metadata
### Preferred Domain Names
- Time
- Customer & Sales
- Product & Contract
- Bookings Analytics

### Preferred Entity Names
- Date
- Customer
- Product
- Partner
- Geography
- Sales Representative
- Contract
- Booking

### Preferred Measure Names
- Quantity
- Unit List Price USD
- Discount Percentage
- Booking Amount USD
- Annual Contract Value USD
- Total Contract Value USD

### Key Modeling Notes
- No entities were invented beyond the DDL.
- Domain assignments for grouped dimensions are inferred but grounded in glossary semantics and star-schema structure.
- Business names prefer glossary terminology over technical names.
- All confidence scores below 1.00 indicate inference rather than explicit source agreement.
