---
title: Power BI Overview
summary: Embed and use Power BI dashboards in your web application.
tags:
  - power-bi
  - web-app
---

# Power BI Overview

This section introduces how Power BI integrates with the solution for interactive analytics.

## 1. Objectives

- Reuse the same report across environments & sites
- Keep dashboards performant (< 5s load for key visuals)
- Enforce data security & isolation

## 2. Architecture Snapshot

```
ADX (Functions) -> Power BI Dataset -> Report -> Embedded in Web-App
```

## 3. Key Building Blocks

| Component | Description | Reference |
|-----------|-------------|-----------|
| Parameterized Functions | Source for DirectQuery / Import | ADX Functions & Performance |
| Power BI Parameters | Filter scoping & reuse | Parameters & Reuse |
| Embed Token Service | Secure report embedding | Embedding & Security |
| RLS Roles | Tenant / site isolation | Embedding & Security |

## 4. Quick Start Checklist

- [ ] Create ADX serving function `Dashboard_Fact` with parameters
- [ ] Define Power BI parameters (Site, TimeWindowHours, Product)
- [ ] Build visuals from parameterized query
- [ ] Implement embed token backend endpoint
- [ ] Add site filter application on load
- [ ] Validate RLS & parameter variations

## 5. Subpages

- [Parameters & Reuse](./power-bi-parameters.md)
- [Embedding & Security](./power-bi-embedding.md)

## 6. Performance Tips (Preview)

| Issue | Mitigation |
|-------|------------|
| Slow initial load | Pre-warm dataset via REST refresh | 
| Heavy visuals | Aggregate in ADX function | 
| Data bloat | Remove unused columns & tables |

## 7. Next Steps

Proceed to parameters for full reuse across sites.

> See: [Parameters & Reuse](./power-bi-parameters.md)
