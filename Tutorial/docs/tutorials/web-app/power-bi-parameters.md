---
title: Power BI Parameters & Reuse
summary: Design parameterized datasets and M queries to reuse dashboards across environments and web-app deployments.
tags:
  - power-bi
  - parameters
  - reuse
  - performance
---

# Power BI Parameters & Reuse

Parametrization lets you deploy the same `.pbix` to multiple tenants, sites, or time scopes without cloning logic. It also reduces dataset size by pushing filters down into ADX.

## 1. Parameter Strategy (Project Context)

In this training setup, parameters align with serving functions created in `00-Initialization.kql` (e.g., `GetScenarios`, `GetMeasures`). Rather than many separate filters, we focus on a small stable set:

| Parameter | Purpose | Backing Function Usage | Source |
|-----------|---------|------------------------|--------|
| Site (or Workspace scope) | Narrow scenario / probe data | Filters Scenario/Probe tables (if multi-site) | Derived from workspace / user claim |
| TimeWindowHours | Limit recency for probe facts | Compute datetime range in Kusto (`ago(TimeWindowHours * 1h)`) | Frontend control |
| ProbeType | Focus a specific measure set | Passed to `GetMeasures(Probe)` | Report slicer |
| ScenarioState (optional) | Filter scenarios by validation state | Filter in `GetScenarios()` pipeline | Report slicer |

Keep parameters minimal; they must map cleanly to function parameters or filters without post-processing.

### Mapping to Configuration Files

| Concept | Declared In | How It Appears In Report |
|---------|-------------|--------------------------|
| Run template parameters (e.g., CustomersCount) | `project/solution.yaml` (`parameters` & `parameterGroups`) | May influence simulation outputs surfaced via `GetScenarios()` |
| Dynamic filter (last run) | `project/workspace.yaml` (`dynamicFilters`) | Automatically filters report to latest simulation run (no manual parameter) |
| ADX connection parameters | `workspace.yaml` powerbi.workspace.reports.parameters (`ADX_Cluster`, `ADX_Database`) | M parameters bound during deployment |
| Probe / scenario functions | `00-Initialization.kql` | Data sources in Power BI queries |

## 2. Creating Parameters in Power BI Desktop (Applied)

Use only parameters required for query shaping. Example minimal set: `TimeWindowHours`.
If Site is handled via RLS or embedding context, skip exposing it as a manual parameter.

Steps:
1. Transform Data > Manage Parameters > New > `TimeWindowHours` (Number, default 12)
2. (Optional) Add `ProbeType` as Text if you want a configurable default instead of a slicer.
3. If cluster/database differ per environment, add `ADXCluster` & `ADXDatabase` parameters; populate from deployment script or Power BI deployment pipeline rules.

## 3. Using Parameters in Kusto M Query (GetMeasures)

```m
let
    Cluster = Parameter_ADXCluster,
    Database = Parameter_ADXDatabase,
    Hours = Number.ToText(Parameter_TimeWindowHours),
    // Example: restrict to last N hours then expand probes
    Query =
        "GetMeasures('LatencyProbe') | where ProbeDate > ago(" & Hours & "h)"
,
    Source = Kusto.Contents(Cluster, Database, Query)
in
    Source
```

For scenarios list:
```m
let
    Cluster = Parameter_ADXCluster,
    Database = Parameter_ADXDatabase,
    Query = "GetScenarios()"
,
    Source = Kusto.Contents(Cluster, Database, Query)
in
    Source
```

## 4. Dynamic Binding (Embedding Context)

In this platform the backend embedding already injects user/workspace context. Frontend may only adjust slicers (e.g., ProbeType). Avoid duplicating Site/Workspace filters if RLS or function logic already enforces them.

## 5. Dataset Slimming (Project-Specific)

| Technique | Application Here | Notes |
|-----------|------------------|-------|
| Function parameters | Limit probe window | Use `ago()` in function or query |
| Pre-aggregation | Add MV on `ProbesMeasures` if performance degrades | Requires backend PR |
| Column pruning | Remove unused dynamic fields | Avoid expanding entire `FactsRaw` unless needed |
| Incremental refresh | If converting to Import mode for large history | Partition by `ProbeDate` |

## 6. Testing Reusability

Instead of exporting PBIX per site, validate across workspace contexts (if multi-workspace deployment) and time windows:

```bash
for hours in 6 12 24; do
  echo "Testing TimeWindowHours=$hours"
  # Use pbicli or REST to rebind parameter (pseudo-example)
  pbicli datasets update-params --dataset-id $DATASET --parameters TimeWindowHours=$hours || exit 1
  sleep 5
  # Optionally trigger a refresh if Import mode
  echo "OK"
done
```

## 7. Checklist (Adapted)

- [ ] Queries call only approved serving functions (GetMeasures/GetScenarios)
- [ ] Parameter list minimal & documented
- [ ] No duplicated filter logic in visuals and functions
- [ ] Dataset refresh / DirectQuery latency within targets
- [ ] Performance test across TimeWindowHours values

## 8. Next

Proceed to backend embedding & security validation.

> See: [Embedding & Security](./power-bi-embedding.md)
