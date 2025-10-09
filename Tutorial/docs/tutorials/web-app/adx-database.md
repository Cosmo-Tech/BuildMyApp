---
title: ADX Database Overview
summary: Integrate and manage the Azure Data Explorer (ADX) layer for your web application.
tags:
  - adx
  - data
  - web-app
---

# ADX Database Overview

This page introduces how the web-app consumes data from Azure Data Explorer (ADX) and how to structure your data estate for analytics & dashboards.

## 1. Why ADX for the Solution?

- Sub-second exploratory analytics on time-series & events
- Native integration with Power BI (DirectQuery / import) and Azure ecosystem
- Scalable ingestion & retention policies

## 2. High-Level Flow (Project-specific)

```
Sources (onboarding data / storage)
   └─> ETL (Cosmo Tech runner templates)
         - code/run_templates/etl_with_local_file
         - code/run_templates/etl_with_azure_storage
   └─> Backend pipelines (Solution_etl.yaml, Create.kql)
         - Apply ADX tables/mappings/policies
         - Register update policies / materialized views
         - Create serving functions
→ ADX Raw / Prepared Tables
→ Serving Functions
→ Power BI (embedded via backend)
```

Notes:
- The backend is the source of truth for ADX artifacts via `Create.kql` and CI/CD.
- Onboarding data is optional and used only in non-prod to validate end-to-end.

## 3. Core Concepts (in this project)

| Concept | Where it’s defined | Example |
|---------|---------------------|---------|
| Tables & Mappings | Backend `Create.kql` | `BrewerEvents`, JSON/CSV mappings |
| Update Policy / MV | Backend `Create.kql` | Raw -> Cleaned / 15-min aggregates |
| Serving Function | Backend KQL scripts | `Dashboard_Fact(...)` |

Avoid manual table/function creation in shared envs—propose changes via PR to backend scripts.

## 4. Schema Source of Truth & Design Patterns

`Create.kql` dictates the schema. Use these patterns when proposing changes:

| Column Type | Purpose | Naming |
|-------------|---------|--------|
| Timestamp | Query time axis | `Timestamp` first column |
| Entity Keys | Filter dimensions | `Site`, `BatchId`, `Product` |
| Measures | Numeric metrics | `DurationSeconds`, `Temperature` |
| Status / Type | Categorical attributes | `EventType` |

Retention example (applied by backend):

```kusto
.alter table BrewerEvents policy retention softdelete = 30d
```

## 5. Ingestion Tips (Project-specific)

- For dev/onboarding data, use the provided ETL templates to generate the source dataset:
  - Local file template: `Sources/onboarding-brewery-solution/code/run_templates/etl_with_local_file/etl.py`
  - Azure Storage template: `Sources/onboarding-brewery-solution/code/run_templates/etl_with_azure_storage/etl.py`
  - Sample inputs live in `Sources/onboarding-brewery-solution/Simulation/Resource/scenariorun-data/` and, when zipped, under `reference/Nodes/*.csv` and `reference/Edges/*.csv`.
- ADX ingestion is executed by the backend pipeline using `Create.kql` mappings. Do not ingest directly into curated tables. If you need to validate a schema change, ingest into a staging table defined in `Create.kql` and let update policies/materialized views populate the serving tables.
- Keep file formats aligned with the repository (CSV for Nodes/ and Edges/). For larger volumes in higher environments, consider Parquet in storage and update the ingestion mapping in `Create.kql` accordingly.
- For near real-time flows, rely on backend-managed connectors and orchestration defined under `Sources/onboarding-brewery-solution/API/` (e.g., `Solution_etl.yaml`) rather than ad-hoc ingestion; coordinate with backend to register any new sources.

## 6. Schema Provisioning via Backend

In this project, the ADX schema (tables, mappings, policies) is defined in the backend `Create.kql` script and deployed through backend pipelines. Do not recreate tables manually from the UI.

Workflow for changes:
1. Propose updates in `Create.kql` (new columns, policies, mappings)
2. Run local validation against a dev cluster (optional)
3. Open a PR; backend CI applies changes to non-prod
4. After approval, pipeline promotes to production

Keep serving function contracts stable; if breaking, version functions (e.g., `Dashboard_Fact_v2`).

## 7. Serving Functions

All dashboard/API queries should route via serving functions (see: [ADX Functions & Performance](./adx-functions-performance.md)).

Benefits:
- Centralize filter logic
- Enforce parameter contracts
- Reduce duplication across Power BI & services

## 8. Governance & Source Control

- Store Kusto artifacts in backend repo alongside `Create.kql`.
- Use PRs and CI to apply changes to dev first, then promote.

## 9. Observability

Use built-in commands:
```kusto
.show table BrewerEvents details
.show functions
.show queries | where StartedOn > ago(15m)
.show operations | where StartedOn > ago(1h) and Status != 'Completed'
// If using managed ingestion into tables, check ingestion state
.show ingestion failures | where Database == '<database>' and Table == 'BrewerEvents' and Timestamp > ago(1h)
```

## 10. Next

Proceed to implementing reusable functions and performance tuning.

> See: [Functions & Performance](./adx-functions-performance.md)

## 11. Junior Onboarding: ADX Setup (Step-by-step)

This walkthrough assumes:
- You have access to the Azure subscription and target ADX cluster/database
- Backend owns deployment of schema via `Create.kql`
- You have the onboarding repo checked out at `Sources/onboarding-brewery-solution`

### A. Prerequisites (local)

- Azure CLI (logged in): `az login`
- ADX permissions: can view database and run read-only queries
- Coordinates from your team:
  - Cluster URI: `https://<cluster>.<region>.kusto.windows.net`
  - Database name: `<database>`
  - Backend pipeline path for `Create.kql` (read-only)

### B. Provision (done by Backend)

Backend CI will apply `Create.kql` to provision tables, mappings, policies and serving functions. As a junior, you don’t run schema changes yourself.

What you should do: verify that objects exist after deployment.

- In Kusto Explorer or Web UI, run:
```kusto
.show tables
.show functions
```
Confirm presence of domain tables (e.g., `BrewerEvents`) and serving functions (e.g., `Dashboard_Fact` or similar per project naming).

### C. Load Sample Data (optional, non-prod)

If the environment is empty and your team allows sample data loads, use the onboarding dataset.

Location in repo:
- `Sources/onboarding-brewery-solution/Simulation/Resource/scenariorun-data/`
  - `Bar.csv`, `Customer.csv`, `arc_to_Customer.csv` (example graph data)

Typical approaches (confirm which is permitted):
- Use backend ETL runner templates under `code/run_templates/` (e.g., `etl_with_local_file`) — usually interacts with Cosmo Tech API & datasets
- Or a one-off ADX ingestion (dev only) via Web UI upload to a staging table that matches `Create.kql` schemas

Ask your mentor which path to use in your environment.

### D. Sanity Queries (read-only)

Run simple queries to check data landed and functions work:
```kusto
// Replace names with your actual tables/functions
BrewerEvents | take 5
BrewerEvents | summarize count() by Site
Dashboard_Fact(datetime(ago(12h)), datetime(now()), 'Lyon_01', '') | take 10
```

Expect a few rows and reasonable counts. If empty:
- Confirm ingestion happened
- Check RLS/filters (if querying through Power BI)

### E. Power BI Integration Check

This project embeds Power BI via backend. Your tasks:
- Confirm the dataset’s main query references the serving function(s)
- Validate that parameter values (Site, TimeWindowHours) match what the function expects
- Open the embedded dashboard and verify visuals populate for a known site/time window

If visuals are blank, coordinate with backend to check RLS mapping and embed token scopes.

### F. Change Requests

When you need schema or serving function changes:
1. Discuss with backend owner
2. Propose edits in `Create.kql` or function `.kql` files
3. Open a PR → CI applies to dev → validate → promote

Avoid ad-hoc manual changes in shared environments.

### G. What to Document During Onboarding

- Cluster/Database you used
- Tables/functions you validated
- Screenshots of the sanity queries results
- Any discrepancies found & who you contacted

This helps the next newcomer follow the same path.
