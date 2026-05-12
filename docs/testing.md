# Testing Strategy — DataPulse BI

## 1. Purpose

This document defines the minimum testing strategy for DataPulse BI.

The goal is not to create a complex testing system in the MVP. The goal is to prove that the most important data and API logic works correctly.

## 2. Testing Objective

Testing must protect the core promise of the project:

```text
raw data becomes reliable business metrics
```

If ingestion, transformation, or metric calculation is wrong, the dashboard becomes misleading. Therefore, backend and pipeline tests are more important than visual tests in the MVP.

## 3. Minimum Testing Scope

The MVP must include tests for:

- ingestion behavior
- data validation
- transformation logic
- revenue calculation
- API healthcheck
- at least one metric endpoint
- empty result behavior

## 4. Testing Types

## 4.1 Unit Tests

Used for pure logic.

Examples:

- parse date
- parse numeric value
- calculate total amount
- normalize category
- normalize region
- detect invalid quantity

## 4.2 Integration Tests

Used to test components together.

Examples:

- CSV ingestion records run metadata correctly
- duplicate raw rows create rejections and data quality issues
- API endpoint reads from database
- metric service returns correct summary
- transformation inserts expected records

## 4.3 Smoke Tests

Used to confirm that the system starts.

Examples:

- backend `/health` returns OK
- frontend builds or lints
- database container runs

## 5. Critical Flows to Test

## 5.1 Ingestion flow

Test that:

- valid rows are inserted into raw table
- duplicate rows are handled
- ingestion run is recorded
- record counts are correct

## 5.2 Transformation flow

Test that:

- valid raw records become staging records
- invalid records are rejected
- dimensions are created or reused
- fact records are created
- total amount is calculated correctly

## 5.3 Metric flow

Test that:

- total revenue is correct
- order count is correct
- average order value is correct
- date filters affect results correctly

## 5.4 API flow

Test that:

- `/health` works
- `/metrics/summary` works
- endpoint returns expected response shape
- invalid query parameters are handled

## 6. Recommended Backend Test Structure

```text
backend/tests/
├── test_foundation.py
├── test_ingestion.py
├── test_api.py
├── test_transformation.py
├── test_metrics.py
├── test_smoke.py
└── conftest.py
```

## 7. Example Test Cases

## 7.1 Revenue calculation

Given:

```text
quantity = 3
unit_price = 20.00
```

Expected:

```text
total_amount = 60.00
```

## 7.2 Invalid quantity

Given:

```text
quantity = -1
```

Expected:

```text
record is rejected
data quality issue is created
```

## 7.3 Date filter

Given orders in January and February.

When filter is:

```text
start_date=2026-01-01
end_date=2026-01-31
```

Expected:

```text
only January orders are included
```

## 8. Test Commands

Backend:

```bash
cd backend
source .venv/bin/activate
pytest
```

Backend smoke command:

```bash
cd backend
source .venv/bin/activate
python scripts/run_smoke_checks.py
```

Frontend lint:

```bash
cd frontend
npm run lint
```

Frontend production build:

```bash
cd frontend
npm run build
```

## 9. Minimum Validation Criteria

The MVP should not be considered ready unless:

- all backend tests pass
- healthcheck works
- transformation tests pass
- one metric endpoint test passes
- no secrets are committed
- dashboard can load data from the backend
- frontend lint succeeds
- frontend production build succeeds

## 10. Test Data Strategy

Use a small deterministic test dataset.

Example:

```text
3 valid orders
1 invalid order
1 duplicate order
```

This allows predictable assertions.

Current implemented tests already cover:

- backend foundation configuration
- metadata registration of the milestone 2 tables
- stable row hash generation for ingestion
- successful raw ingestion into PostgreSQL
- duplicate rejection with `data_quality_issues` creation
- transformation helper normalization and parsing behavior
- successful transformation into staging, dimensions, and fact tables
- invalid raw rows rejected during transformation with `data_quality_issues`
- API endpoint responses for health, ingestion status, metrics, and orders
- metric service summaries, breakdowns, filters, and ordering rules
- end-to-end smoke validation across health, ingestion, transformation, and API reads
- frontend static analysis with `npm run lint`
- frontend compilation with `npm run build`
- `/health` API validation
- `/ingestion/runs/latest` API validation with quality summary
- `/metrics/summary` API validation
- `/orders` API validation with filters

## 11. What Not to Test in MVP

The MVP does not need:

- full browser automation
- load testing
- visual regression testing
- complex mocks for external APIs
- stress testing with millions of rows

## 12. Future Testing Improvements

Future versions may add:

- Playwright end-to-end tests
- data contract tests
- load testing
- CI test matrix
- test database container
- frontend component tests
- visual regression tests

## 13. CI Recommendation

A simple GitHub Actions workflow may run:

- backend tests
- frontend lint
- optional frontend build

Suggested workflow stages:

```text
checkout
setup-python
install-backend-dependencies
run-pytest
setup-node
install-frontend-dependencies
run-lint
```
