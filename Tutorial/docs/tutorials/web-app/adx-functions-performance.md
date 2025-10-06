---
title: ADX Functions & Performance
summary: Create reusable functions and optimize Kusto queries powering the web-app dashboards.
tags:
  - adx
  - performance
  - web-app
  - optimization
---

# ADX Functions & Performance

This guide shows how to encapsulate logic into Azure Data Explorer (ADX) functions and optimize query performance for responsive dashboards.

> Focus areas:
> 1. Reuse logic across dashboards & web-app deployments
> 2. Reduce data scanned & transferred to Power BI
> 3. Improve freshness vs cost balance
> 4. Make parameters first-class citizens

## 1. Organize Your ADX Layer

| Layer | Purpose | Artifacts |
|-------|---------|-----------|
| Raw ingestion | Land data as-is | Ingestion mappings, staging tables |
| Harmonized | Cleaned, typed, conformed | Update policies, materialized views |
| Serving | Query-ready, thin, parameterizable | Functions, export tables |

Keep functions in a dedicated folder in source control (e.g. `infrastructure/adx/functions/`). Version them and deploy declaratively (ARM/Bicep/Terraform / Kusto scripts).

## 2. Defining Functions

Two main kinds:
- Inline (lightweight) functions
- Stored functions created once in the database

Example: `GetBrewEvents(startTime:datetime, endTime:datetime, site:string)`

```kusto
.create-or-alter function with (docstring = 'Brew events filtered by time & site', folder='serving/brew')
GetBrewEvents(startTime:datetime, endTime:datetime, site:string)
{
    BrewerEvents
    | where Timestamp between (startTime .. endTime)
    | where Site == site
    | project Timestamp, Site, BatchId, EventType, DurationSeconds
}
```

### Best Practices
- Use PascalCase for function names, camelCase for parameters.
- Provide `docstring` and `folder` for discoverability.
- Avoid hard-coded constants; expose as parameters.

## 3. Composing Functions

Create small focused functions and compose:

```kusto
// Base filter
.create-or-alter function GetBatches(startTime:datetime, endTime:datetime){
  Batches | where StartTime between (startTime .. endTime)
          | project BatchId, Product, StartTime, EndTime
}

// Duration enrichment
.create-or-alter function GetBatchDurations(startTime:datetime, endTime:datetime){
  GetBatches(startTime, endTime)
  | extend DurationMinutes = datetime_diff('minute', EndTime, StartTime)
}

// Aggregated KPI
.create-or-alter function GetOutputKPI(startTime:datetime, endTime:datetime){
  GetBatchDurations(startTime, endTime)
  | summarize AvgDuration = avg(DurationMinutes), TotalBatches = count()
}
```

## 4. Parameter Patterns for Power BI

When embedding, the web-app can compute a time window & site, then pass them as query parameters.

Pattern: a single root function that accepts all Power BI-relevant parameters and outputs a narrow dataset.

```kusto
.create-or-alter function Dashboard_Fact(startTime:datetime, endTime:datetime, site:string, product:string){
  GetBrewEvents(startTime, endTime, site)
  | where product == '' or Product == product
  | summarize Events=count(), TotalDuration=sum(DurationSeconds)
}
```

In Power BI M query (parameterized):

```m
let
    StartTime = DateTimeZone.UtcNow() - #duration(0,12,0,0),
    EndTime = DateTimeZone.UtcNow(),
    Site = Text.From(EnvSiteParameter),
    Product = "" ,
    Source = Kusto.Contents("https://<cluster>.<region>.kusto.windows.net", "<database>", 
        "Dashboard_Fact(datetime({" & DateTimeZone.ToText(StartTime, "yyyy-MM-dd HH:mm:ss") & "}), datetime({" & DateTimeZone.ToText(EndTime, "yyyy-MM-dd HH:mm:ss") & "}), '" & Site & "', '" & Product & "')")
in
    Source
```

(Replace the dynamic site & product with actual PBI parameters linked to slicers.)

## 5. Performance Tactics

| Goal | Tactic | Notes |
|------|--------|-------|
| Min scan | Use narrower projection early | Always `project` right after filters |
| Fast filter | Use ingestion time partitioning & `between` | Align queries to partition keys |
| Reuse | Materialized views | For heavy joins/expensive lookups |
| Lower transfer | Summarize before export to PBI | Return aggregated rows, not raw events |
| Adaptive freshness | Tiered functions (raw vs MV) | Switch via a boolean parameter |

### Example: Hybrid Live + Historical

```kusto
.create-or-alter function Dashboard_Fact_Live(startAgo:timespan, site:string){
  GetBrewEvents(now()-startAgo, now(), site)
}

.create-or-alter function Dashboard_Fact_History(startTime:datetime, endTime:datetime, site:string){
  Materialized_BrewAgg
  | where WindowStart between (startTime .. endTime)
  | where Site == site
}

.create-or-alter function Dashboard_Fact_Union(startTime:datetime, endTime:datetime, liveAgo:timespan, site:string){
  union isfuzzy=true
    (Dashboard_Fact_History(startTime, endTime, site))
    (Dashboard_Fact_Live(liveAgo, site))
  | summarize Events=sum(Events), TotalDuration=sum(TotalDuration)
}
```

## 6. Monitoring & Diagnostics

```kusto
.show functions
.show commands-and-queries | where StartedOn > ago(1h)
```

Enable Query Store & alerts for:
- Long-running queries > 15s
- High data scanned vs returned ratio

## 7. Deployment Automation

Store function definitions as `.kql` files and apply idempotently:

```bash
#!/usr/bin/env bash
for f in infrastructure/adx/functions/*.kql; do
  echo "Applying $f";
  az kusto script create --cluster-name $CLUSTER --database-name $DB --name $(basename $f .kql) --script-content "$(cat $f)" --force-update-tag $(date +%s);
done
```

## 8. Checklist

- [ ] All dashboard queries go through a root serving function
- [ ] Parameters documented (name, type, default)
- [ ] Functions have docstrings & folder metadata
- [ ] Query completion p50 < 3s
- [ ] No function returns > 100k rows to Power BI
- [ ] Materialized views used for joins > 10M rows

## 9. Next Steps

Continue with Power BI parameterization to bind these functions to dynamic dashboards.

> See: [Power BI Parameters & Reuse](./power-bi-parameters.md)
