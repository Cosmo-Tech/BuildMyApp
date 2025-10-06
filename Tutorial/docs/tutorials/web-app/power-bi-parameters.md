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

## 1. Parameter Strategy

| Parameter | Purpose | Source | Example |
|-----------|---------|--------|---------|
| Site | Scope data to a factory/site | Web-app context / user claims | `Lyon_01` |
| TimeWindowHours | Rolling horizon | UI control (slider) | `12` |
| Product | Optional product filter | Slicer | `Pilsner` |
| RefreshMode | Switch raw vs aggregated | Hidden toggle | `"auto"` / `"historical"` |

Keep parameters minimal; prefer enumerations (domains) when possible for caching efficiency.

## 2. Creating Parameters in Power BI Desktop

1. Transform Data > Manage Parameters > New
2. Name: `Site`; Type: Text; Suggested Values: List (optional)
3. Default Value: `Lyon_01`; Current Value: `Lyon_01`
4. Repeat for others.

> Tip: Use Text/Number/TrueFalse types—avoid DateTime parameters if you can compute datetimes inside the query from `now()` plus duration.

## 3. Using Parameters in Kusto M Query

```m
let
    Site = Text.From(Parameter_Site),
    Hours = Number.ToText(Parameter_TimeWindowHours),
    Product = Text.From(Parameter_Product),
    Cluster = Parameter_ADXCluster,
    Database = Parameter_ADXDatabase,
    Query =
        "Dashboard_Fact(datetime(" & DateTimeZone.ToText(DateTimeZone.UtcNow() - #duration(0, Number.From(Hours),0,0), "yyyy-MM-dd HH:mm:ss") & "), datetime(" & DateTimeZone.ToText(DateTimeZone.UtcNow(), "yyyy-MM-dd HH:mm:ss") & "), '" & Site & "', '" & Product & "')"
,
    Source = Kusto.Contents(Cluster, Database, Query)
in
    Source
```

Abstract cluster & database into parameters too for full reuse.

## 4. Dynamic Binding from Web-App

When embedding, you can set report-level filters or parameter values via the JavaScript SDK.

### Option A: Embedding + Slicers
Populate hidden slicer tables with a single value coming from the web-app.

```ts
const site = appContext.site; // e.g. from JWT
report.setFilters([
  {
    $schema: "http://powerbi.com/product/schema#basic",
    target: { table: "SiteFilter", column: "Site" },
    operator: "In",
    values: [site]
  }
]);
```

### Option B: Query Parameters Through DirectQuery (Preview-dependent)
If using DirectQuery, you can rewrite queries to reference a single-row parameter table that the app updates via Push API.

## 5. Dataset Slimming

| Technique | Effect | Notes |
|-----------|--------|-------|
| Parameter pushdown | Smaller scans | Express all filters as function parameters |
| Summarize in ADX | Less row transfer | Provide aggregated KPIs, not raw events |
| Remove unused columns | Better compression | Audit visuals & fields list |
| Incremental refresh | Faster partition processing | Combine with time parameters |

## 6. Testing Reusability

Create a matrix of environment x site values and script automated validation:

```bash
for site in Lyon_01 Berlin_02; do
  pbicli reports export --report "BrewDashboard" --workspace $WS --file export-$site.pbix \
    --parameter Site=$site || exit 1
  echo "Validated $site"
done
```

## 7. Checklist

- [ ] All Power BI queries reference a single root ADX serving function
- [ ] Parameters documented & minimal
- [ ] No visual applies redundant filters already in function
- [ ] Dataset size < target (e.g. 100 MB) / row count limited
- [ ] Test matrix executed for each release

## 8. Next

Proceed to embedding details & security considerations.

> See: [Embedding & Security](./power-bi-embedding.md)
