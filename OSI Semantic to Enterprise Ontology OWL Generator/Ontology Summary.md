# Ontology Summary

## Ontology Overview

### Primary Business Domain
Cisco bookings and revenue performance analytics within the Quote-to-Booking portion of the Order-to-Cash cycle.

### Ontology Scope
This ontology models the enterprise business semantics required to analyze bookings at booked order-line grain across time, customer, product, partner, geography, sales representative, and contract contexts. It captures explicit dimensional entities from the OSI semantic model and business-process-supported relationships from opportunity qualification through booking recognition, fulfillment context, contract creation, and renewal lifecycle. The ontology is intended for semantic search, governed analytics, metadata governance, knowledge graph construction, Protégé-based ontology management, and AI-assisted business intelligence.

### Concepts Modeled
- **Time domain**: Date
- **Customer & Sales domain**: Customer, Partner, Geography, SalesRepresentative
- **Product & Contract domain**: Product, Contract
- **Bookings Analytics domain**: Booking
- **Process / lifecycle support concepts explicitly grounded in the process document**: OpportunityQualification, QuoteConfiguration, DealRegistration, OrderPlacement, BookingRecognition, FulfillmentProvisioning, ContractEntitlementCreation, RenewalLifecycle, Role
- **Roles from process document**: SalesAccountExecutive, DealDesk, RevenueOperations, ChannelPartnerOrganization, CustomerExperienceRenewals, Finance

### Relationships Modeled
#### Explicit semantic relationships from the OSI model
- Booking occursOn Date
- Booking forCustomer Customer
- Booking includesProduct Product
- Booking soldThroughPartner Partner
- Booking associatedWithGeography Geography
- Booking ownedBySalesRepresentative SalesRepresentative
- Booking governedByContract Contract

#### Explicit process-supported relationships
- OpportunityQualification precedes QuoteConfiguration
- QuoteConfiguration precedes DealRegistration
- DealRegistration precedes OrderPlacement
- OrderPlacement precedes BookingRecognition
- BookingRecognition precedes FulfillmentProvisioning
- FulfillmentProvisioning precedes ContractEntitlementCreation
- ContractEntitlementCreation precedes RenewalLifecycle
- BookingRecognition produces Booking
- ContractEntitlementCreation establishes Contract
- RenewalLifecycle mayGenerate Booking
- SalesAccountExecutive performs OpportunityQualification
- DealDesk performs QuoteConfiguration
- ChannelPartnerOrganization performs DealRegistration
- RevenueOperations governs BookingRecognition
- CustomerExperienceRenewals performs RenewalLifecycle
- Finance consumes BookingRecognition outcome downstream for billings and revenue recognition context

### Business Rules Captured
#### Explicit rules from source material
1. A booking is recorded when Cisco accepts a valid order with committed value.
2. Booking grain is one row per booked order line.
3. Booking amount, ACV, and TCV are measured on bookings.
4. Booking type is classified as New, Renewal, or Upsell/Expansion.
5. Bookings precede billing and revenue recognition.
6. For subscription and SaaS offers, booking amount equals Total Contract Value (TCV).
7. For subscription offers, ACV is the annualized figure.
8. Hardware is perpetual and ACV is typically not applicable.
9. Contracts carry coverage level, term, and renewal context.
10. Renewal bookings flow back into the booking fact population.

#### Inferred OWL-translatable constraints
- Each Booking has exactly 1 Date.
- Each Booking has exactly 1 Customer.
- Each Booking has exactly 1 Product.
- Each Booking has exactly 1 Partner.
- Each Booking has exactly 1 Geography.
- Each Booking has exactly 1 SalesRepresentative.
- Each Booking has exactly 1 Contract.
- bookingId is functional for Booking.
- dateKey, customerKey, productKey, partnerKey, geographyKey, salesRepKey, and contractKey are functional identifiers within their corresponding classes.
- isRenewal is modeled as a boolean-like flag but retained as integer-compatible source semantics in annotation notes.
- Quote-to-Booking process ordering is modeled with a transitive precedesProcess relation.

### Explicit vs Inferred Knowledge
#### Explicit
- Core classes Date, Customer, Product, Partner, Geography, SalesRepresentative, Contract, Booking
- Core attributes and measures from DDL and glossary
- Fact-to-dimension relationships
- Quote-to-Booking process stages and organizational roles stated in the business-process document

#### Inferred
- Process-stage classes formalized as ontology classes
- Transitive process ordering relation
- Inverse properties for key relationships
- Existential and exact-cardinality restrictions derived from foreign-key semantics and stated fact grain
- Property chain for renewal lifecycle producing renewal booking events
- Optional hierarchy support annotations for Region > Theater > Country and Fiscal Year > Fiscal Quarter > Date

### Validation Summary
| Check | Status | Notes |
|---|---|---|
| OSI semantic completeness | Passed with warnings | Complete core star schema and glossary available |
| Business process readability | Passed | DOCX text extracted successfully |
| Missing entity definitions | Warning | No separate entity class for billings or revenue because not present in schema |
| Missing relationships | Warning | No explicit cross-dimension links such as Customer-to-Geography in source model |
| Duplicate entities | Passed | None detected |
| Invalid semantic references | Warning | glossary `geo_key` vs DDL `geography_key` naming mismatch |
| Incomplete business process descriptions | Warning | Renewal capture rate and attach rate mentioned as KPIs but not fully modeled as source entities |
| Missing business rules | Warning | No formal machine-readable rule expressions for renewal capture timing |
| Duplicate classes/properties | Passed in generated ontology | Controlled naming used |
| Circular inheritance | Passed | None introduced |
| Missing domains/ranges | Passed in generated ontology | Declared for generated properties |
| Protégé compatibility | Passed | RDF/XML OWL ontology with standard namespaces |
| Logical consistency | High confidence | No disjointness/assertion conflicts introduced |

### Validation Warnings
- Glossary and SQL use different names for geography key: `geo_key` vs `geography_key`.
- Glossary includes `day_of_week`, `month_number`, and `calendar_quarter`, but these are not present in SQL DDL.
- Process document discusses billings and revenue recognition, but those concepts are not modeled as core enterprise classes because they are not represented in the provided semantic model.
- Renewal capture rate and attach rate are KPI concepts rather than directly modeled transactional entities in the source.
- Hardware ACV non-applicability is a business rule but cannot be fully enforced in OWL DL without more explicit offer-type individualization or closed-world validation.

### Competency Questions
1. Which bookings are renewals for subscription or SaaS offers and what are their ACV and TCV values by fiscal quarter and region?
2. Which customers, partners, products, and sales teams contribute the highest booking amount by route to market and product family?
3. Which contracts marked for auto-renew are associated with renewal lifecycle activities and resulting renewal bookings?

## Ontology Metrics Report

### Overall Metrics
| Metric | Value |
|---|---:|
| Total Classes | 22 |
| Total Object Properties | 22 |
| Total Datatype Properties | 53 |
| Total Individuals | 6 |
| Hierarchy Depth | 2 |
| Total Axioms | 170 |
| Reasoning Rules / Constructs | 12 |

### Domain Metrics
| Domain | Class Count | Property Count | Individual Count | Reasoning Complexity |
|---|---:|---:|---:|---|
| Time | 1 | 7 | 0 | Simple |
| Customer & Sales | 5 | 27 | 4 | Medium |
| Product & Contract | 2 | 12 | 0 | Medium |
| Bookings Analytics | 1 | 18 | 0 | Complex |
| Quote-to-Booking Process | 13 | 11 | 2 | Complex |

### Reasoning Complexity Summary
- **Simple**: Time domain primarily supports classification and filtering by fiscal/calendar attributes.
- **Medium**: Customer & Sales and Product & Contract support contextual classification, inverse navigation, and hierarchy-style semantic grouping.
- **Complex**: Bookings Analytics and process domains support exact-cardinality restrictions, inverse properties, transitive process sequencing, and process-to-booking inference patterns.

## Ontology Quality & Reasoning Insights

### Semantic Coverage
#### Well-covered areas
- Booking star-schema core is strongly represented.
- Fact-to-dimension relationships are explicit and high confidence.
- Process stages from Quote-to-Booking are represented as reusable business activity classes.
- Roles and ownership responsibilities from the process document are captured.

#### Classes without rich outgoing properties
- Role subclasses are intentionally lightweight and mainly participate through `performsActivity` or `governsActivity` semantics.
- Geography currently has descriptive attributes but no explicit object hierarchy individuals for Region/Theater/Country.

#### Missing inverse properties
- Most core relationship inverses were added.
- Descriptive datatype properties do not require inverses.

#### Missing domain/range declarations
- None for generated core properties.

#### Incomplete axioms / sparse areas
- No closed-world validation for mandatory measure presence.
- No explicit ontology classes for Billing or RevenueRecognition because they are outside the provided semantic model.
- No explicit attach-rate event or entitlement asset class beyond process support.

#### Orphan classes
- None. All classes participate in either subclassing, object properties, or annotations.

### Reasoning Capabilities
- **Multi-step inference chains**: A renewal booking can be traced from RenewalLifecycle to Booking through `mayGenerateBooking`, and from Booking to Contract, Product, Customer, and Date.
- **Property chains**: Process ordering via `precedesProcess` supports ordered workflow reasoning; inverse navigation allows tracing from context dimensions back to bookings.
- **Transitive reasoning**: `precedesProcess` is transitive, enabling inference that OpportunityQualification precedes BookingRecognition and RenewalLifecycle.
- **Symmetric reasoning**: Not used, because source relationships are directional.
- **Functional reasoning**: Booking-to-dimension contextual links are modeled as functional from the booking perspective.
- **Rule completeness**: Good for structural analytics, partial for nuanced commercial policy rules.

### Key Reasoning Paths
1. **Booking contextualization path**  
   Booking -> occursOn -> Date -> fiscalQuarter / fiscalYear
2. **Commercial ownership path**  
   Booking -> ownedBySalesRepresentative -> SalesRepresentative -> salesTeam
3. **Channel analysis path**  
   Booking -> soldThroughPartner -> Partner -> routeToMarket
4. **Portfolio analysis path**  
   Booking -> includesProduct -> Product -> productFamily / technologyDomain / offerType
5. **Renewal analysis path**  
   RenewalLifecycle -> mayGenerateBooking -> Booking -> governedByContract -> Contract
6. **Geographic analysis path**  
   Booking -> associatedWithGeography -> Geography -> region / theater / country

### SPARQL Query Patterns Supported
```sparql
PREFIX ex: <http://example.com/cisco/bookings/ontology#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>

# 1. Renewal bookings by fiscal quarter and region
SELECT ?fiscalQuarter ?region (SUM(?amount) AS ?totalBookings)
WHERE {
  ?b rdf:type ex:Booking ;
     ex:isRenewal ?renewal ;
     ex:bookingAmountUsd ?amount ;
     ex:occursOn ?d ;
     ex:associatedWithGeography ?g .
  ?d ex:fiscalQuarter ?fiscalQuarter .
  ?g ex:region ?region .
  FILTER(?renewal = true)
}
GROUP BY ?fiscalQuarter ?region
```

```sparql
PREFIX ex: <http://example.com/cisco/bookings/ontology#>

# 2. Bookings by product family and route to market
SELECT ?productFamily ?routeToMarket (SUM(?amount) AS ?total)
WHERE {
  ?b a ex:Booking ;
     ex:bookingAmountUsd ?amount ;
     ex:includesProduct ?p ;
     ex:soldThroughPartner ?partner .
  ?p ex:productFamily ?productFamily .
  ?partner ex:routeToMarket ?routeToMarket .
}
GROUP BY ?productFamily ?routeToMarket
```

```sparql
PREFIX ex: <http://example.com/cisco/bookings/ontology#>

# 3. Contracts associated with auto-renew context and renewal bookings
SELECT ?contractType ?coverageLevel ?booking
WHERE {
  ?booking a ex:Booking ;
           ex:isRenewal ?renewal ;
           ex:governedByContract ?c .
  ?c ex:autoRenewFlag ?flag ;
     ex:contractType ?contractType ;
     ex:coverageLevel ?coverageLevel .
  FILTER(?renewal = true && ?flag = "Y")
}
```

### Recommendations
1. Standardize `geo_key` and `geography_key` naming across semantic and physical layers.
2. Add missing date attributes `day_of_week`, `month_number`, and `calendar_quarter` to the physical model or revise the glossary.
3. Consider explicit ontology classes for Billing and RevenueRecognition only if future source artifacts formally define them.
4. Add SHACL shapes for closed-world validation of mandatory measures, renewal flags, and offer-type-specific ACV constraints.
5. Introduce optional hierarchy classes or SKOS concept schemes for Region, Theater, ProductFamily, TechnologyDomain, and SalesTeam if richer navigation is required.
6. Add uniqueness constraints at the data-governance layer for natural identifiers such as customerId, productId, partnerId, and representativeId.
7. If sample or production instances become available, populate individuals for products, partners, and fiscal periods to strengthen reasoning demonstrations.

### Watch-outs
- Do not sum `discountPct` or `unitListPriceUsd` as additive measures.
- `is_renewal` originates as integer in SQL but is semantically boolean-like; integration pipelines should normalize carefully.
- OWL operates under open-world semantics, so some business validation rules need SHACL or data-quality controls.
- Process concepts beyond Booking are documented business activities, not transactional entities from the SQL schema.
- Absence of customer-to-geography or partner-to-geography object properties is intentional because those links were not explicitly modeled in the source.

### Confidence Assessment
| Area | Confidence |
|---|---:|
| Core class extraction | 0.99 |
| Core property extraction | 0.99 |
| Process activity mapping | 0.95 |
| Cardinality restrictions from star schema | 0.96 |
| Inferred inverse properties | 0.94 |
| Renewal lifecycle reasoning | 0.88 |
| KPI-to-ontology translation | 0.84 |

### Ontology Validation Outcome
- Duplicate classes: None detected
- Duplicate properties: None detected
- Invalid namespaces: None detected
- Missing domains: None in generated ontology
- Missing ranges: None in generated ontology
- Circular inheritance: None detected
- Invalid restrictions: None detected
- Logical consistency: No contradiction introduced
- Protégé compatibility: Compatible RDF/XML OWL structure generated
