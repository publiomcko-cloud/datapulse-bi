# Database Modeling — DataPulse BI

## 1. Purpose

This document defines the recommended relational model for DataPulse BI.

The database must support:

- raw data storage
- transformation history
- analytical queries
- dashboard metrics
- ingestion logs
- future expansion

PostgreSQL is the recommended database.

## 2. Modeling Principles

The model should follow these principles:

- keep raw imported data separate from clean analytical data
- use explicit primary keys
- preserve source identifiers when possible
- include timestamps for traceability
- avoid storing secrets
- index columns commonly used in filters
- use clear naming conventions

## 3. Suggested Schemas

For clarity, the project may use PostgreSQL schemas:

```text
raw
staging
analytics
ops
```

If schema separation is too much for the MVP, use table prefixes instead:

```text
raw_orders
stg_orders
fact_orders
dim_products
ingestion_runs
```

Current implementation choice:

- milestone 2 uses table prefixes in the default `public` schema
- the initial schema is managed by Alembic revision `20260429_0001`
- milestone 4 uses `transform_orders` runs in `ingestion_runs` to track staging and analytics loads

## 4. Main Entities

## 4.1 ingestion_runs

Stores execution logs for ingestion and transformation jobs.

### Purpose

To track pipeline execution and show operational status in the dashboard.

### Suggested fields

| Field | Type | Notes |
|---|---:|---|
| id | UUID | Primary key |
| job_name | VARCHAR(100) | Example: ingest_orders, transform_orders |
| source_name | VARCHAR(100) | CSV, API, synthetic_generator |
| status | VARCHAR(30) | success, failed, partial |
| started_at | TIMESTAMP | Required |
| finished_at | TIMESTAMP | Nullable |
| records_read | INTEGER | Default 0 |
| records_inserted | INTEGER | Default 0 |
| records_rejected | INTEGER | Default 0 |
| error_message | TEXT | Nullable |
| created_at | TIMESTAMP | Default now |

### Business rules

- each pipeline run must create one record
- failed runs must store an error message
- status must be controlled by enum or check constraint

## 4.2 raw_orders

Stores imported source records before transformation.

### Purpose

To preserve original data for auditing and reprocessing.

### Suggested fields

| Field | Type | Notes |
|---|---:|---|
| id | UUID | Primary key |
| source_record_id | VARCHAR(100) | Unique if available |
| source_name | VARCHAR(100) | Source label |
| order_date_raw | TEXT | Original date value |
| customer_id_raw | TEXT | Original customer ID |
| product_id_raw | TEXT | Original product ID |
| product_name_raw | TEXT | Original product name |
| category_raw | TEXT | Original category |
| region_raw | TEXT | Original region |
| channel_raw | TEXT | Original sales channel |
| quantity_raw | TEXT | Original quantity |
| unit_price_raw | TEXT | Original unit price |
| total_amount_raw | TEXT | Original amount if available |
| ingestion_run_id | UUID | FK to ingestion_runs |
| row_hash | VARCHAR(128) | For duplicate detection |
| created_at | TIMESTAMP | Default now |

### Business rules

- raw values may be text to preserve source format
- row_hash should help prevent duplicate imports
- raw records should not be manually edited

## 4.3 stg_orders

Stores cleaned order records.

### Purpose

To normalize raw data before loading analytical tables.

### Suggested fields

| Field | Type | Notes |
|---|---:|---|
| id | UUID | Primary key |
| source_record_id | VARCHAR(100) | Original source ID |
| order_date | DATE | Normalized date |
| customer_external_id | VARCHAR(100) | Source customer ID |
| product_external_id | VARCHAR(100) | Source product ID |
| product_name | VARCHAR(255) | Clean product name |
| category | VARCHAR(100) | Clean category |
| region | VARCHAR(100) | Clean region |
| channel | VARCHAR(100) | Clean sales channel |
| quantity | INTEGER | Must be positive |
| unit_price | NUMERIC(12,2) | Must be non-negative |
| total_amount | NUMERIC(12,2) | quantity * unit_price if absent |
| ingestion_run_id | UUID | FK |
| created_at | TIMESTAMP | Default now |

### Business rules

- quantity must be greater than zero
- total_amount must be greater than or equal to zero
- invalid dates must be rejected or sent to a rejection log
- category, region, and channel should be normalized

## 4.4 dim_products

Stores product information.

### Purpose

To support product and category analysis.

### Suggested fields

| Field | Type | Notes |
|---|---:|---|
| id | UUID | Primary key |
| product_external_id | VARCHAR(100) | Unique source ID |
| product_name | VARCHAR(255) | Required |
| category | VARCHAR(100) | Required |
| created_at | TIMESTAMP | Default now |
| updated_at | TIMESTAMP | Default now |

### Business rules

- product_external_id should be unique
- category should be normalized
- product name changes should update updated_at

## 4.5 dim_customers

Stores customer or customer-segment information.

### Purpose

To support future customer segmentation.

### Suggested fields

| Field | Type | Notes |
|---|---:|---|
| id | UUID | Primary key |
| customer_external_id | VARCHAR(100) | Unique source ID |
| region | VARCHAR(100) | Nullable |
| customer_segment | VARCHAR(100) | Nullable |
| created_at | TIMESTAMP | Default now |
| updated_at | TIMESTAMP | Default now |

### Business rules

- customer details may remain synthetic or anonymized
- no sensitive personal data should be used in public demo datasets

## 4.6 dim_regions

Stores normalized region values.

### Purpose

To avoid inconsistent region names.

### Suggested fields

| Field | Type | Notes |
|---|---:|---|
| id | UUID | Primary key |
| region_name | VARCHAR(100) | Unique |
| country | VARCHAR(100) | Optional |
| created_at | TIMESTAMP | Default now |

## 4.7 dim_channels

Stores normalized sales channels.

### Purpose

To support channel comparison.

### Suggested fields

| Field | Type | Notes |
|---|---:|---|
| id | UUID | Primary key |
| channel_name | VARCHAR(100) | Unique |
| created_at | TIMESTAMP | Default now |

Examples:

- online
- marketplace
- physical_store
- phone_sales
- partner

## 4.8 fact_orders

Stores analytics-ready order facts.

### Purpose

To support dashboard queries.

### Suggested fields

| Field | Type | Notes |
|---|---:|---|
| id | UUID | Primary key |
| order_date | DATE | Required |
| product_id | UUID | FK to dim_products |
| customer_id | UUID | FK to dim_customers |
| region_id | UUID | FK to dim_regions |
| channel_id | UUID | FK to dim_channels |
| quantity | INTEGER | Required |
| unit_price | NUMERIC(12,2) | Required |
| total_amount | NUMERIC(12,2) | Required |
| source_record_id | VARCHAR(100) | Traceability |
| created_at | TIMESTAMP | Default now |

### Business rules

- total_amount should equal quantity * unit_price unless source data explicitly provides a verified amount
- facts should reference dimensions
- duplicate source records should not create duplicate facts

## 5. data_quality_issues

Stores validation problems.

### Purpose

To support dashboard data quality summaries and preserve row-level validation evidence for rejected records.

### Suggested fields

| Field | Type | Notes |
|---|---:|---|
| id | UUID | Primary key |
| ingestion_run_id | UUID | FK |
| source_record_id | VARCHAR(100) | Nullable |
| issue_type | VARCHAR(100) | missing_field, invalid_type, duplicate |
| field_name | VARCHAR(100) | Nullable |
| original_value | TEXT | Nullable |
| message | TEXT | Required |
| created_at | TIMESTAMP | Default now |

### Business rules

- the MVP should create a quality issue record when a row is rejected during validation or transformation
- issue records should be attributable to an ingestion or transformation run
- the latest ingestion status can aggregate these records into a compact dashboard summary

## 6. Enums

Recommended enums or controlled values:

### ingestion_status

- success
- failed
- partial
- running

### sales_channel

- online
- marketplace
- physical_store
- phone_sales
- partner
- other

### issue_type

- missing_field
- invalid_date
- invalid_number
- duplicate_record
- negative_value
- unknown_category
- transformation_error

## 7. Relationships

```text
ingestion_runs 1---N raw_orders
ingestion_runs 1---N stg_orders
ingestion_runs 1---N data_quality_issues

dim_products 1---N fact_orders
dim_customers 1---N fact_orders
dim_regions 1---N fact_orders
dim_channels 1---N fact_orders
```

## 8. Recommended Indexes

### raw_orders

- row_hash
- ingestion_run_id
- source_record_id

### stg_orders

- order_date
- category
- region
- channel
- ingestion_run_id

### fact_orders

- order_date
- product_id
- customer_id
- region_id
- channel_id
- order_date + region_id
- order_date + channel_id
- order_date + product_id

### ingestion_runs

- job_name
- status
- started_at

## 9. Suggested Initial Seed

The seed should include:

- 100 to 1,000 synthetic orders for quick local testing
- 10 products
- 4 categories
- 5 regions
- 4 channels
- at least 12 months of dates
- intentional minor data quality issues in raw records

Example categories:

- electronics
- home
- food
- services

Example regions:

- south
- southeast
- midwest
- northeast
- north

## 10. Conceptual Schema

```text
raw_orders
    |
    v
stg_orders
    |
    +--> dim_products
    +--> dim_customers
    +--> dim_regions
    +--> dim_channels
    |
    v
fact_orders
    |
    v
dashboard metrics
```

## 11. MVP Database Boundaries

The MVP should avoid:

- multi-tenant modeling
- personally identifiable customer data
- complex slowly changing dimensions
- real payment information
- unnecessary warehouse complexity

## 12. Future Database Improvements

Future versions may add:

- dbt models
- materialized views
- incremental transformations
- partitioning by date
- row-level security
- organization-based access
- data lineage metadata
