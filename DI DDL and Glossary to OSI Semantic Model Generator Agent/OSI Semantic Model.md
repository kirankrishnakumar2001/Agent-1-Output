# OSI Semantic Model

## Model Scope
- Source Type: Relational star schema DDL + business glossary text
- Grain: One row per booked order line in `fact_bookings`
- Inference Policy: Prefer glossary terminology over technical names; inferred mappings are explicitly marked with confidence

## Validation Warnings
- Warning: Glossary-to-DDL key mismatch detected for Geography (`geo_key` in glossary, `geography_key` in DDL).
- Warning: Glossary contains attributes not physically implemented in `dim_date`: `day_of_week`, `month_number`, `calendar_quarter`.
- Warning: No explicit unique constraints on natural keys such as `customer_id`, `product_id`, `partner_id`, `rep_id`.
- Warning: No duplicate entities, duplicate measures, circular relationships, missing keys, or orphan entities detected.

---

## Domain: Booking Transactions
**Description:** Core commercial transaction domain containing booked order-line level facts, monetary values, quantities, and booking classification.
**Business Purpose:** Supports revenue, contract value, renewal, pricing, and order-level booking analysis.

### Entity: Booking
- **Technical Table:** `fact_bookings`
- **Entity Type:** Fact Entity
- **Description:** Stores measurable business transactions representing customer bookings. Each record corresponds to a booked order line and contains revenue, quantity, pricing, and references to related dimensions.
- **Grain:** One row per booked order line
- **Business Keys:**
  - Inferred composite operational identifier: `order_number` + `order_line_number` (confidence: 0.82, inferred)
- **Technical Keys:**
  - Primary Key: `booking_id`
  - Foreign Keys: `date_key`, `customer_key`, `product_key`, `partner_key`, `geography_key`, `sales_rep_key`, `contract_key`
- **Attributes:**
  - `booking_id`
    - Business Name: Booking Identifier
    - Definition: Unique identifier for a booking transaction.
    - Glossary Mapping: Explicit
    - Domain: Booking Transactions
    - Preferred Name: Booking Identifier
    - Synonyms: Booking ID
    - Data Type: INTEGER
    - Semantic Role: Identifier
    - Mapping Confidence: 1.00
  - `order_number`
    - Business Name: Sales Order Number
    - Definition: Sales order number associated with the booking.
    - Glossary Mapping: Explicit
    - Domain: Booking Transactions
    - Preferred Name: Sales Order Number
    - Synonyms: Order Number
    - Data Type: VARCHAR(20)
    - Semantic Role: Degenerate Dimension
    - Mapping Confidence: 1.00
  - `order_line_number`
    - Business Name: Sales Order Line Number
    - Definition: Line number within the sales order.
    - Glossary Mapping: Explicit
    - Domain: Booking Transactions
    - Preferred Name: Sales Order Line Number
    - Synonyms: Order Line Number
    - Data Type: INTEGER
    - Semantic Role: Degenerate Dimension
    - Mapping Confidence: 1.00
  - `date_key`
    - Business Name: Booking Date Key
    - Definition: Foreign key referencing the booking date in the Date dimension.
    - Glossary Mapping: Explicit
    - Domain: Date & Fiscal Calendar
    - Preferred Name: Booking Date Key
    - Synonyms: Date Key
    - Data Type: INTEGER
    - Semantic Role: Foreign Key
    - Mapping Confidence: 1.00
  - `customer_key`
    - Business Name: Customer Key
    - Definition: Foreign key referencing the customer associated with the booking.
    - Glossary Mapping: Explicit
    - Domain: Customer Management
    - Preferred Name: Customer Key
    - Synonyms: Customer Surrogate Key
    - Data Type: INTEGER
    - Semantic Role: Foreign Key
    - Mapping Confidence: 1.00
  - `product_key`
    - Business Name: Product Key
    - Definition: Foreign key referencing the booked product.
    - Glossary Mapping: Explicit
    - Domain: Product Portfolio
    - Preferred Name: Product Key
    - Synonyms: Product Surrogate Key
    - Data Type: INTEGER
    - Semantic Role: Foreign Key
    - Mapping Confidence: 1.00
  - `partner_key`
    - Business Name: Partner Key
    - Definition: Foreign key referencing the selling partner.
    - Glossary Mapping: Explicit
    - Domain: Partner Channel
    - Preferred Name: Partner Key
    - Synonyms: Partner Surrogate Key
    - Data Type: INTEGER
    - Semantic Role: Foreign Key
    - Mapping Confidence: 1.00
  - `geography_key`
    - Business Name: Geography Key
    - Definition: Foreign key referencing the geographic location associated with the booking.
    - Glossary Mapping: Inferred from glossary term `geo_key`
    - Domain: Geography
    - Preferred Name: Geography Key
    - Synonyms: Geo Key
    - Data Type: INTEGER
    - Semantic Role: Foreign Key
    - Mapping Confidence: 0.84
  - `sales_rep_key`
    - Business Name: Sales Representative Key
    - Definition: Foreign key referencing the responsible sales representative.
    - Glossary Mapping: Explicit
    - Domain: Sales Organization
    - Preferred Name: Sales Representative Key
    - Synonyms: Sales Rep Key
    - Data Type: INTEGER
    - Semantic Role: Foreign Key
    - Mapping Confidence: 1.00
  - `contract_key`
    - Business Name: Contract Key
    - Definition: Foreign key referencing the associated customer contract.
    - Glossary Mapping: Explicit
    - Domain: Contract Management
    - Preferred Name: Contract Key
    - Synonyms: Contract Surrogate Key
    - Data Type: INTEGER
    - Semantic Role: Foreign Key
    - Mapping Confidence: 1.00
  - `booking_type`
    - Business Name: Booking Type
    - Definition: Indicates the type of booking transaction, such as New, Renewal, or Expansion.
    - Glossary Mapping: Explicit
    - Domain: Booking Transactions
    - Preferred Name: Booking Type
    - Synonyms: Transaction Type
    - Data Type: VARCHAR(15)
    - Semantic Role: Classification
    - Mapping Confidence: 1.00
  - `is_renewal`
    - Business Name: Renewal Indicator
    - Definition: Indicates whether the booking represents a contract renewal.
    - Glossary Mapping: Explicit
    - Domain: Booking Transactions
    - Preferred Name: Renewal Indicator
    - Synonyms: Is Renewal, Renewal Flag
    - Data Type: INTEGER
    - Semantic Role: Indicator
    - Mapping Confidence: 1.00
- **Measures:**
  - `quantity`
    - Business Name: Booking Quantity
    - Definition: Number of product units included in the booking.
    - Aggregation Type: Sum
    - Additivity: Additive
    - Mapping Confidence: 1.00
  - `unit_list_price_usd`
    - Business Name: Unit List Price USD
    - Definition: Standard list price per product unit before discounts.
    - Aggregation Type: Average / Do Not Sum
    - Additivity: Non-additive
    - Mapping Confidence: 1.00
  - `discount_pct`
    - Business Name: Discount Percent
    - Definition: Percentage discount applied to the standard list price.
    - Aggregation Type: Average / Weighted Average preferred
    - Additivity: Non-additive
    - Mapping Confidence: 1.00
  - `booking_amount_usd`
    - Business Name: Booking Amount USD
    - Definition: Net booking revenue after applying discounts.
    - Aggregation Type: Sum
    - Additivity: Additive
    - Mapping Confidence: 1.00
  - `acv_usd`
    - Business Name: Annual Contract Value USD
    - Definition: Annual Contract Value associated with the booking.
    - Aggregation Type: Sum
    - Additivity: Additive
    - Mapping Confidence: 1.00
  - `tcv_usd`
    - Business Name: Total Contract Value USD
    - Definition: Total Contract Value over the full duration of the contract.
    - Aggregation Type: Sum
    - Additivity: Additive
    - Mapping Confidence: 1.00
- **Relationships:**
  - Booking occurs on Date
  - Booking is for Customer
  - Booking includes Product
  - Booking is sold via Partner
  - Booking is associated with Geography
  - Booking is owned by Sales Representative
  - Booking is governed by Contract

---

## Domain: Date & Fiscal Calendar
**Description:** Time intelligence domain supporting both calendar and fiscal analysis.
**Business Purpose:** Enables trending, period-based reporting, and fiscal performance analysis.

### Entity: Date
- **Technical Table:** `dim_date`
- **Entity Type:** Dimension Entity
- **Description:** Stores calendar and fiscal date information used for analyzing bookings across different time periods.
- **Keys:**
  - Primary Key: `date_key`
- **Attributes:**
  - `date_key`
    - Business Name: Date Key
    - Definition: Unique surrogate key representing a calendar date. Used to join the Date dimension with fact tables.
    - Glossary Mapping: Explicit
    - Domain: Date & Fiscal Calendar
    - Preferred Name: Date Key
    - Synonyms: Calendar Date Key
    - Data Type: INTEGER
    - Semantic Role: Identifier
    - Mapping Confidence: 1.00
  - `full_date`
    - Business Name: Full Date
    - Definition: Complete calendar date associated with a transaction or booking.
    - Glossary Mapping: Explicit
    - Domain: Date & Fiscal Calendar
    - Preferred Name: Full Date
    - Synonyms: Calendar Date
    - Data Type: DATE
    - Semantic Role: Descriptive Attribute
    - Mapping Confidence: 1.00
  - `month_name`
    - Business Name: Month Name
    - Definition: Name of the calendar month.
    - Glossary Mapping: Explicit
    - Domain: Date & Fiscal Calendar
    - Preferred Name: Month Name
    - Synonyms: Calendar Month Name
    - Data Type: VARCHAR(12)
    - Semantic Role: Hierarchy Attribute
    - Mapping Confidence: 1.00
  - `calendar_year`
    - Business Name: Calendar Year
    - Definition: Calendar year associated with the date.
    - Glossary Mapping: Explicit
    - Domain: Date & Fiscal Calendar
    - Preferred Name: Calendar Year
    - Synonyms: Year
    - Data Type: INTEGER
    - Semantic Role: Hierarchy Attribute
    - Mapping Confidence: 1.00
  - `fiscal_year`
    - Business Name: Fiscal Year
    - Definition: Fiscal year according to the organization's accounting calendar.
    - Glossary Mapping: Explicit
    - Domain: Date & Fiscal Calendar
    - Preferred Name: Fiscal Year
    - Synonyms: FY
    - Data Type: VARCHAR(6)
    - Semantic Role: Hierarchy Attribute
    - Mapping Confidence: 1.00
  - `fiscal_quarter`
    - Business Name: Fiscal Quarter
    - Definition: Fiscal quarter according to the organization's fiscal calendar.
    - Glossary Mapping: Explicit
    - Domain: Date & Fiscal Calendar
    - Preferred Name: Fiscal Quarter
    - Synonyms: FQ
    - Data Type: VARCHAR(10)
    - Semantic Role: Hierarchy Attribute
    - Mapping Confidence: 1.00
  - `fiscal_period_seq`
    - Business Name: Fiscal Period Sequence
    - Definition: Sequential fiscal accounting period used for financial reporting.
    - Glossary Mapping: Explicit
    - Domain: Date & Fiscal Calendar
    - Preferred Name: Fiscal Period Sequence
    - Synonyms: Fiscal Period Seq
    - Data Type: INTEGER
    - Semantic Role: Hierarchy Attribute
    - Mapping Confidence: 1.00
- **Hierarchies:**
  - Calendar Hierarchy: `full_date` -> `month_name` -> `calendar_year` (partially explicit; inferred due to missing month number and quarter attributes, confidence 0.78)
  - Fiscal Hierarchy: `full_date` -> `fiscal_period_seq` -> `fiscal_quarter` -> `fiscal_year` (inferred from glossary and naming, confidence 0.90)
- **Reference Data:** None explicit

---

## Domain: Customer Management
**Description:** Customer master and segmentation domain.
**Business Purpose:** Supports booking analysis by customer, segment, industry, and strategic tier.

### Entity: Customer
- **Technical Table:** `dim_customer`
- **Entity Type:** Dimension Entity
- **Description:** Contains descriptive information about customers who purchase products or services.
- **Keys:**
  - Primary Key: `customer_key`
  - Business Key: `customer_id` (inferred as natural key from glossary and comment, confidence 0.95)
- **Attributes:**
  - `customer_key`
    - Business Name: Customer Key
    - Definition: Surrogate key uniquely identifying a customer within the data warehouse.
    - Glossary Mapping: Explicit
    - Domain: Customer Management
    - Preferred Name: Customer Key
    - Synonyms: Customer Surrogate Key
    - Data Type: INTEGER
    - Semantic Role: Identifier
    - Mapping Confidence: 1.00
  - `customer_id`
    - Business Name: Customer Identifier
    - Definition: Business identifier assigned to a customer.
    - Glossary Mapping: Explicit
    - Domain: Customer Management
    - Preferred Name: Customer Identifier
    - Synonyms: Customer ID
    - Data Type: VARCHAR(20)
    - Semantic Role: Business Key
    - Mapping Confidence: 1.00
  - `customer_name`
    - Business Name: Customer Name
    - Definition: Official name of the customer organization.
    - Glossary Mapping: Explicit
    - Domain: Customer Management
    - Preferred Name: Customer Name
    - Synonyms: Customer Organization Name
    - Data Type: VARCHAR(80)
    - Semantic Role: Descriptive Attribute
    - Mapping Confidence: 1.00
  - `segment`
    - Business Name: Customer Segment
    - Definition: Business segment to which the customer belongs.
    - Glossary Mapping: Explicit
    - Domain: Customer Management
    - Preferred Name: Customer Segment
    - Synonyms: Segment
    - Data Type: VARCHAR(30)
    - Semantic Role: Dimension Attribute
    - Mapping Confidence: 1.00
  - `industry`
    - Business Name: Industry
    - Definition: Industry classification of the customer.
    - Glossary Mapping: Explicit
    - Domain: Customer Management
    - Preferred Name: Industry
    - Synonyms: Industry Classification
    - Data Type: VARCHAR(40)
    - Semantic Role: Dimension Attribute
    - Mapping Confidence: 1.00
  - `account_tier`
    - Business Name: Account Tier
    - Definition: Strategic classification of the customer based on business importance.
    - Glossary Mapping: Explicit
    - Domain: Customer Management
    - Preferred Name: Account Tier
    - Synonyms: Strategic Tier
    - Data Type: VARCHAR(20)
    - Semantic Role: Classification
    - Mapping Confidence: 1.00
  - `hq_country`
    - Business Name: Headquarters Country
    - Definition: Country where the customer's headquarters is located.
    - Glossary Mapping: Explicit
    - Domain: Customer Management
    - Preferred Name: Headquarters Country
    - Synonyms: HQ Country
    - Data Type: VARCHAR(40)
    - Semantic Role: Geography Attribute
    - Mapping Confidence: 1.00
  - `hq_region`
    - Business Name: Headquarters Region
    - Definition: Geographic region where the customer's headquarters is located.
    - Glossary Mapping: Explicit
    - Domain: Customer Management
    - Preferred Name: Headquarters Region
    - Synonyms: HQ Region
    - Data Type: VARCHAR(20)
    - Semantic Role: Geography Attribute
    - Mapping Confidence: 1.00

---

## Domain: Product Portfolio
**Description:** Product and offer classification domain.
**Business Purpose:** Supports analysis by product, family, technology, offering type, and owning business unit.

### Entity: Product
- **Technical Table:** `dim_product`
- **Entity Type:** Dimension Entity
- **Description:** Contains descriptive information about products and services sold through bookings.
- **Keys:**
  - Primary Key: `product_key`
  - Business Key: `product_id` (inferred natural key, confidence 0.95)
- **Attributes:**
  - `product_key`
    - Business Name: Product Key
    - Definition: Surrogate key uniquely identifying a product record.
    - Glossary Mapping: Explicit
    - Domain: Product Portfolio
    - Preferred Name: Product Key
    - Synonyms: Product Surrogate Key
    - Data Type: INTEGER
    - Semantic Role: Identifier
    - Mapping Confidence: 1.00
  - `product_id`
    - Business Name: Product Identifier
    - Definition: Business identifier or SKU assigned to a product.
    - Glossary Mapping: Explicit
    - Domain: Product Portfolio
    - Preferred Name: Product Identifier
    - Synonyms: Product ID, SKU, PID
    - Data Type: VARCHAR(30)
    - Semantic Role: Business Key
    - Mapping Confidence: 1.00
  - `product_name`
    - Business Name: Product Name
    - Definition: Commercial name of the product or service.
    - Glossary Mapping: Explicit
    - Domain: Product Portfolio
    - Preferred Name: Product Name
    - Synonyms: Commercial Product Name
    - Data Type: VARCHAR(80)
    - Semantic Role: Descriptive Attribute
    - Mapping Confidence: 1.00
  - `product_family`
    - Business Name: Product Family
    - Definition: Group of related products sharing common characteristics.
    - Glossary Mapping: Explicit
    - Domain: Product Portfolio
    - Preferred Name: Product Family
    - Synonyms: Family
    - Data Type: VARCHAR(30)
    - Semantic Role: Hierarchy Attribute
    - Mapping Confidence: 1.00
  - `technology_domain`
    - Business Name: Technology Domain
    - Definition: Technology category to which the product belongs.
    - Glossary Mapping: Explicit
    - Domain: Product Portfolio
    - Preferred Name: Technology Domain
    - Synonyms: Technology Category
    - Data Type: VARCHAR(40)
    - Semantic Role: Classification
    - Mapping Confidence: 1.00
  - `offer_type`
    - Business Name: Offer Type
    - Definition: Commercial offering category such as Hardware, Software, Subscription, or Services.
    - Glossary Mapping: Explicit
    - Domain: Product Portfolio
    - Preferred Name: Offer Type
    - Synonyms: Offering Type
    - Data Type: VARCHAR(30)
    - Semantic Role: Classification
    - Mapping Confidence: 1.00
  - `business_entity`
    - Business Name: Business Entity
    - Definition: Business unit responsible for the product portfolio.
    - Glossary Mapping: Explicit
    - Domain: Product Portfolio
    - Preferred Name: Business Entity
    - Synonyms: Business Unit
    - Data Type: VARCHAR(30)
    - Semantic Role: Organizational Attribute
    - Mapping Confidence: 1.00

---

## Domain: Partner Channel
**Description:** Channel partner domain for indirect sales analysis.
**Business Purpose:** Supports understanding of partner contribution, channel routes, and partner program segmentation.

### Entity: Partner
- **Technical Table:** `dim_partner`
- **Entity Type:** Dimension Entity
- **Description:** Stores information about channel partners involved in selling products.
- **Keys:**
  - Primary Key: `partner_key`
  - Business Key: `partner_id` (inferred natural key, confidence 0.95)
- **Attributes:**
  - `partner_key`
    - Business Name: Partner Key
    - Definition: Surrogate key uniquely identifying a partner.
    - Glossary Mapping: Explicit
    - Domain: Partner Channel
    - Preferred Name: Partner Key
    - Synonyms: Partner Surrogate Key
    - Data Type: INTEGER
    - Semantic Role: Identifier
    - Mapping Confidence: 1.00
  - `partner_id`
    - Business Name: Partner Identifier
    - Definition: Business identifier assigned to the partner organization.
    - Glossary Mapping: Explicit
    - Domain: Partner Channel
    - Preferred Name: Partner Identifier
    - Synonyms: Partner ID
    - Data Type: VARCHAR(20)
    - Semantic Role: Business Key
    - Mapping Confidence: 1.00
  - `partner_name`
    - Business Name: Partner Name
    - Definition: Name of the partner organization.
    - Glossary Mapping: Explicit
    - Domain: Partner Channel
    - Preferred Name: Partner Name
    - Synonyms: Channel Partner Name
    - Data Type: VARCHAR(60)
    - Semantic Role: Descriptive Attribute
    - Mapping Confidence: 1.00
  - `partner_type`
    - Business Name: Partner Type
    - Definition: Classification of the partner such as Distributor, Reseller, or Service Provider.
    - Glossary Mapping: Explicit
    - Domain: Partner Channel
    - Preferred Name: Partner Type
    - Synonyms: Partner Classification
    - Data Type: VARCHAR(30)
    - Semantic Role: Classification
    - Mapping Confidence: 1.00
  - `partner_tier`
    - Business Name: Partner Tier
    - Definition: Partner program level indicating certification or status.
    - Glossary Mapping: Explicit
    - Domain: Partner Channel
    - Preferred Name: Partner Tier
    - Synonyms: Program Tier
    - Data Type: VARCHAR(30)
    - Semantic Role: Classification
    - Mapping Confidence: 1.00
  - `route_to_market`
    - Business Name: Route to Market
    - Definition: Sales channel through which products are sold to customers.
    - Glossary Mapping: Explicit
    - Domain: Partner Channel
    - Preferred Name: Route to Market
    - Synonyms: Sales Channel
    - Data Type: VARCHAR(20)
    - Semantic Role: Classification
    - Mapping Confidence: 1.00

---

## Domain: Geography
**Description:** Geographic reporting and territorial analysis domain.
**Business Purpose:** Supports regional, theater, and country-based booking analysis.

### Entity: Geography
- **Technical Table:** `dim_geography`
- **Entity Type:** Dimension Entity
- **Description:** Provides hierarchical geographic information for analyzing bookings across global regions.
- **Keys:**
  - Primary Key: `geography_key`
- **Attributes:**
  - `geography_key`
    - Business Name: Geography Key
    - Definition: Surrogate key identifying a geographic location.
    - Glossary Mapping: Inferred from glossary term `geo_key`
    - Domain: Geography
    - Preferred Name: Geography Key
    - Synonyms: Geo Key
    - Data Type: INTEGER
    - Semantic Role: Identifier
    - Mapping Confidence: 0.84
  - `region`
    - Business Name: Region
    - Definition: Highest-level geographic reporting region.
    - Glossary Mapping: Explicit
    - Domain: Geography
    - Preferred Name: Region
    - Synonyms: Reporting Region
    - Data Type: VARCHAR(20)
    - Semantic Role: Hierarchy Attribute
    - Mapping Confidence: 1.00
  - `theater`
    - Business Name: Theater
    - Definition: Sales theater within a region.
    - Glossary Mapping: Explicit
    - Domain: Geography
    - Preferred Name: Theater
    - Synonyms: Sales Theater
    - Data Type: VARCHAR(30)
    - Semantic Role: Hierarchy Attribute
    - Mapping Confidence: 1.00
  - `country`
    - Business Name: Country
    - Definition: Country associated with the booking or customer.
    - Glossary Mapping: Explicit
    - Domain: Geography
    - Preferred Name: Country
    - Synonyms: Country Name
    - Data Type: VARCHAR(40)
    - Semantic Role: Hierarchy Attribute
    - Mapping Confidence: 1.00
- **Hierarchies:**
  - Geography Hierarchy: `country` -> `theater` -> `region` (inferred reporting rollup, confidence 0.88)

---

## Domain: Sales Organization
**Description:** Sales workforce and organizational coverage domain.
**Business Purpose:** Supports analysis of bookings by representative, role, team, and customer segment coverage.

### Entity: Sales Representative
- **Technical Table:** `dim_sales_rep`
- **Entity Type:** Dimension Entity
- **Description:** Contains information about sales representatives responsible for customer bookings.
- **Keys:**
  - Primary Key: `sales_rep_key`
  - Business Key: `rep_id` (inferred natural key, confidence 0.95)
- **Attributes:**
  - `sales_rep_key`
    - Business Name: Sales Representative Key
    - Definition: Surrogate key identifying a sales representative.
    - Glossary Mapping: Explicit
    - Domain: Sales Organization
    - Preferred Name: Sales Representative Key
    - Synonyms: Sales Rep Key
    - Data Type: INTEGER
    - Semantic Role: Identifier
    - Mapping Confidence: 1.00
  - `rep_id`
    - Business Name: Sales Representative Identifier
    - Definition: Business identifier assigned to the sales representative.
    - Glossary Mapping: Explicit
    - Domain: Sales Organization
    - Preferred Name: Sales Representative Identifier
    - Synonyms: Rep ID
    - Data Type: VARCHAR(20)
    - Semantic Role: Business Key
    - Mapping Confidence: 1.00
  - `rep_name`
    - Business Name: Sales Representative Name
    - Definition: Full name of the sales representative.
    - Glossary Mapping: Explicit
    - Domain: Sales Organization
    - Preferred Name: Sales Representative Name
    - Synonyms: Rep Name
    - Data Type: VARCHAR(60)
    - Semantic Role: Descriptive Attribute
    - Mapping Confidence: 1.00
  - `sales_role`
    - Business Name: Sales Role
    - Definition: Functional role performed by the sales representative.
    - Glossary Mapping: Explicit
    - Domain: Sales Organization
    - Preferred Name: Sales Role
    - Synonyms: Role
    - Data Type: VARCHAR(40)
    - Semantic Role: Classification
    - Mapping Confidence: 1.00
  - `sales_team`
    - Business Name: Sales Team
    - Definition: Sales organization or team to which the representative belongs.
    - Glossary Mapping: Explicit
    - Domain: Sales Organization
    - Preferred Name: Sales Team
    - Synonyms: Team
    - Data Type: VARCHAR(40)
    - Semantic Role: Organizational Attribute
    - Mapping Confidence: 1.00
  - `segment_covered`
    - Business Name: Segment Covered
    - Definition: Customer segment managed by the sales representative.
    - Glossary Mapping: Explicit
    - Domain: Sales Organization
    - Preferred Name: Segment Covered
    - Synonyms: Covered Segment
    - Data Type: VARCHAR(30)
    - Semantic Role: Coverage Attribute
    - Mapping Confidence: 1.00

---

## Domain: Contract Management
**Description:** Contract characteristics and service entitlement domain.
**Business Purpose:** Supports booking analysis by contract type, term, renewal behavior, and support coverage.

### Entity: Contract
- **Technical Table:** `dim_contract`
- **Entity Type:** Dimension Entity
- **Description:** Stores information describing customer contracts associated with bookings.
- **Keys:**
  - Primary Key: `contract_key`
- **Attributes:**
  - `contract_key`
    - Business Name: Contract Key
    - Definition: Surrogate key identifying a contract.
    - Glossary Mapping: Explicit
    - Domain: Contract Management
    - Preferred Name: Contract Key
    - Synonyms: Contract Surrogate Key
    - Data Type: INTEGER
    - Semantic Role: Identifier
    - Mapping Confidence: 1.00
  - `contract_type`
    - Business Name: Contract Type
    - Definition: Category of customer contract such as Subscription, Support, or Service Agreement.
    - Glossary Mapping: Explicit
    - Domain: Contract Management
    - Preferred Name: Contract Type
    - Synonyms: Agreement Type
    - Data Type: VARCHAR(40)
    - Semantic Role: Classification
    - Mapping Confidence: 1.00
  - `term_months`
    - Business Name: Contract Term Months
    - Definition: Duration of the contract expressed in months.
    - Glossary Mapping: Explicit
    - Domain: Contract Management
    - Preferred Name: Contract Term Months
    - Synonyms: Term in Months
    - Data Type: INTEGER
    - Semantic Role: Duration Attribute
    - Mapping Confidence: 1.00
  - `auto_renew_flag`
    - Business Name: Auto Renew Flag
    - Definition: Indicates whether the contract automatically renews upon expiration.
    - Glossary Mapping: Explicit
    - Domain: Contract Management
    - Preferred Name: Auto Renew Flag
    - Synonyms: Auto Renewal Indicator
    - Data Type: CHAR(1)
    - Semantic Role: Indicator
    - Mapping Confidence: 1.00
  - `coverage_level`
    - Business Name: Coverage Level
    - Definition: Level of service or support included in the contract.
    - Glossary Mapping: Explicit
    - Domain: Contract Management
    - Preferred Name: Coverage Level
    - Synonyms: Support Coverage Level
    - Data Type: VARCHAR(20)
    - Semantic Role: Classification
    - Mapping Confidence: 1.00

---

## Domain: Cross-Domain Relationship Model

### Relationship: Booking occurs on Date
- **Parent Entity:** Date
- **Child Entity:** Booking
- **Technical Join:** `fact_bookings.date_key = dim_date.date_key`
- **Relationship Type:** Dimensional Lookup
- **Cardinality:** Many Bookings to One Date
- **Confidence:** 1.00
- **Basis:** Explicit foreign key

### Relationship: Booking is for Customer
- **Parent Entity:** Customer
- **Child Entity:** Booking
- **Technical Join:** `fact_bookings.customer_key = dim_customer.customer_key`
- **Relationship Type:** Dimensional Lookup
- **Cardinality:** Many Bookings to One Customer
- **Confidence:** 1.00
- **Basis:** Explicit foreign key

### Relationship: Booking includes Product
- **Parent Entity:** Product
- **Child Entity:** Booking
- **Technical Join:** `fact_bookings.product_key = dim_product.product_key`
- **Relationship Type:** Dimensional Lookup
- **Cardinality:** Many Bookings to One Product
- **Confidence:** 1.00
- **Basis:** Explicit foreign key

### Relationship: Booking is sold via Partner
- **Parent Entity:** Partner
- **Child Entity:** Booking
- **Technical Join:** `fact_bookings.partner_key = dim_partner.partner_key`
- **Relationship Type:** Dimensional Lookup
- **Cardinality:** Many Bookings to One Partner
- **Confidence:** 1.00
- **Basis:** Explicit foreign key

### Relationship: Booking is associated with Geography
- **Parent Entity:** Geography
- **Child Entity:** Booking
- **Technical Join:** `fact_bookings.geography_key = dim_geography.geography_key`
- **Relationship Type:** Dimensional Lookup
- **Cardinality:** Many Bookings to One Geography
- **Confidence:** 1.00
- **Basis:** Explicit foreign key plus inferred glossary alignment

### Relationship: Booking is owned by Sales Representative
- **Parent Entity:** Sales Representative
- **Child Entity:** Booking
- **Technical Join:** `fact_bookings.sales_rep_key = dim_sales_rep.sales_rep_key`
- **Relationship Type:** Dimensional Lookup
- **Cardinality:** Many Bookings to One Sales Representative
- **Confidence:** 1.00
- **Basis:** Explicit foreign key

### Relationship: Booking is governed by Contract
- **Parent Entity:** Contract
- **Child Entity:** Booking
- **Technical Join:** `fact_bookings.contract_key = dim_contract.contract_key`
- **Relationship Type:** Dimensional Lookup
- **Cardinality:** Many Bookings to One Contract
- **Confidence:** 1.00
- **Basis:** Explicit foreign key

---

## Semantic Quality Annotations
- **Explicit Metadata Used:** Table names, columns, primary keys, foreign keys, comments in DDL, glossary definitions
- **Inferred Metadata Used:** Domain names, business-friendly entity names, business key inference, hierarchies, geography key synonym alignment, operational composite identifier for order line
- **No Invented Entities:** All entities correspond directly to physical tables in the DDL
- **No Bridge Tables Detected:** No many-to-many semantic structure identified
- **Circular Relationships:** None detected
- **Orphan Entities:** None detected

## Measure Catalog
| Measure | Entity | Definition | Aggregation Type | Additivity |
|---|---|---|---|---|
| Booking Quantity | Booking | Number of product units included in the booking. | Sum | Additive |
| Unit List Price USD | Booking | Standard list price per product unit before discounts. | Average / Do Not Sum | Non-additive |
| Discount Percent | Booking | Percentage discount applied to the standard list price. | Average / Weighted Average preferred | Non-additive |
| Booking Amount USD | Booking | Net booking revenue after applying discounts. | Sum | Additive |
| Annual Contract Value USD | Booking | Annual Contract Value associated with the booking. | Sum | Additive |
| Total Contract Value USD | Booking | Total Contract Value over the full duration of the contract. | Sum | Additive |

## Unmapped Glossary Terms
- `day_of_week` in `dim_date`
- `month_number` in `dim_date`
- `calendar_quarter` in `dim_date`
- Exact glossary term `geo_key` not physically present; semantically mapped to `geography_key`

## Reuse Notes for Downstream Ontology Generation
- Use domains as top-level ontology subject areas.
- Materialize dimension entities as classes and fact entity as transactional/event class.
- Represent foreign-key relationships as object properties with explicit cardinality.
- Carry mapping confidence metadata for inferred terms, especially Geography key synonym handling.
- Preserve aggregation semantics on measures to prevent invalid analytical behavior.
