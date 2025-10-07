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

## 2. High-Level Flow

```
Sources -> Ingestion (EventHub / Blob / ADX LightIngest) -> Raw Tables -> Update Policies / Materialized Views -> Serving Functions -> Power BI / API
```

## 3. Core Concepts

| Concept | Description | Example |
|---------|-------------|---------|
| Table | Physically stored records | `BrewerEvents` |
| Ingestion Mapping | Column mapping from source payload | JSON mapping for brewer events |
| Update Policy | Auto-populate derived table | Raw -> Cleaned table |
| Materialized View | Pre-computed aggregation | 15-min brew KPIs |
| Function | Reusable query logic | `GetBrewEvents()` |

## 4. Recommended Table Design

| Column Type | Purpose | Naming |
|-------------|---------|--------|
| Timestamp | Query time axis | `Timestamp` first column |
| Entity Keys | Filter dimensions | `Site`, `BatchId`, `Product` |
| Measures | Numeric metrics | `DurationSeconds`, `Temperature` |
| Status / Type | Categorical attributes | `EventType` |

Partitioning automatically by ingestion time—optimize retention with policies:

```kusto
.alter table BrewerEvents policy retention softdelete = 30d");
```

## 5. Ingestion Tips

- Batch ingestion for historical loads; streaming/EventHub for near real-time
- Validate schema with a staging table before merging
- Compress JSON payloads or prefer CSV/Parquet for large volume

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

Store Kusto artifacts (`tables.kql`, `functions/*.kql`, `policies.kql`) under version control. Deploy via CI using Azure CLI or ARM scripts.

## 9. Observability

Use built-in commands:
```kusto
.show table BrewerEvents details
.show queries | where StartedOn > ago(15m)
.show operations | where StartedOn > ago(1h) and Status != 'Completed'
```

## 10. Next

Proceed to implementing reusable functions and performance tuning.

> See: [Functions & Performance](./adx-functions-performance.md)
