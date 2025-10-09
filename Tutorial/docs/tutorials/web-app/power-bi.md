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

## 3. Key Building Blocks (Extended)

| Component | Source File | Purpose |
|-----------|------------|---------|
| Solution metadata | `project/solution.yaml` | Declares run templates (Example, ETL) & parameters feeding scenarios |
| Workspace definition | `project/workspace.yaml` | Defines Power BI workspace, ADX scripts, Event Hub connectors, webApp embedding options |
| ADX Init Script | `project/adx/scripts/00-Initialization.kql` | Creates tables & functions consumed by report |
| Power BI Report | `project/powerbi/Example.pbix` | Visualization layer referencing serving functions |
| Permissions | `variables.yaml` (`powerbi_permissions`, `adx_permissions`) | Grants principals access |
| Dynamic Filters | `workspace.yaml` (`webApp.options.charts.scenarioView.dynamicFilters`) | Auto-apply context filters (e.g., last simulation run) |

## 4. Quick Start Checklist (Onboarding Flow)

Follow these in order when onboarding:

1. Clone repos:
   - `onboarding-brewery-solution` (simulation + ETL templates)
   - `build-my-app-training-workspaces` (workspace/Power BI + ADX bootstrap scripts)
2. Review `build-my-app-training-workspaces/variables.yaml` and note:
   - `organization_id`, `workspace_key`, `powerbi_workspace_name`
   - Power BI & ADX principal entries (`powerbi_permissions`, `adx_permissions`)
3. Ensure your user (or service principal) has been added under `powerbi_permissions` and `adx_permissions` (if not, submit a PR or request an admin update and re-run provisioning scripts).
4. Run ADX initialization (if dev environment is fresh):
   - Execute `project/adx/scripts/00-Initialization.kql` via the approved backend pipeline or Kusto Script deployment job (never hand-edit prod).
5. Import / verify `Example.pbix` into the Power BI workspace named in `variables.yaml` if not already published. (Admins only; otherwise just confirm it's present.)
6. Open the report and validate required dataset parameters (Site, TimeWindowHours, etc.) exist or are represented via filters / serving functions (e.g., `GetMeasures`, `GetScenarios`).
7. Confirm backend embedding endpoint returns an embed token for the report (use internal API doc / curl if available).
8. Load the web-app; verify visuals render for default site and switch site/time context successfully.
9. Capture performance: measure first visual load (<5s target) & check ADX query durations for `GetMeasures` and `GetScenarios` functions.
10. Log findings in onboarding notes (successes, missing permissions, performance outliers).

## 5. Key Building Blocks

| Component | Repository / File | Purpose |
|-----------|------------------|---------|
| Workspace Variables | `build-my-app-training-workspaces/variables.yaml` | IDs, permissions, workspace naming |
| ADX Bootstrap | `project/adx/scripts/00-Initialization.kql` | Tables, mappings, baseline functions |
| Example Report | `project/powerbi/Example.pbix` | Starter visuals tied to functions |
| Serving Functions | Defined in KQL bootstrap | `GetScenarios`, `GetMeasures`, etc. |
| ETL Templates | `onboarding-brewery-solution/code/run_templates/*` | Populate dataset / scenario data |

## 6. Performance Tips (Contextual)

| Issue | Mitigation |
|-------|------------|
| Slow `GetMeasures` | Pre-aggregate or reduce `mv-expand` usage | 
| Large dataset refresh | Limit time window in parameters (TimeWindowHours) |
| Permission errors | Re-check `variables.yaml` and re-run permission scripts |

## 7. Next Steps

Continue with parameter configuration: see [Parameters & Reuse](./power-bi-parameters.md)

> After parameters and embedding validation, proceed to RLS & operational governance in [Embedding & Security](./power-bi-embedding.md)

## 8. Unified Onboarding Checklist

| Step | Action | File / Command | Done |
|------|--------|----------------|------|
| 1 | Clone repos | git clone both repos | [ ] |
| 2 | Inspect variables | Open `variables.yaml` | [ ] |
| 3 | Confirm PB workspace name | `powerbi_workspace_name` in `variables.yaml` | [ ] |
| 4 | Check solution run templates | `solution.yaml` runTemplates section | [ ] |
| 5 | Review workspace report binding | `workspace.yaml` powerbi.workspace.reports | [ ] |
| 6 | Verify ADX scripts referenced | `workspace.yaml` adx.scripts + `00-Initialization.kql` | [ ] |
| 7 | Ensure principals present | `powerbi_permissions` / `adx_permissions` | [ ] |
| 8 | Provision / sync backend | Run pipeline / deployment process | [ ] |
| 9 | Publish or confirm `Example.pbix` | Power BI Service | [ ] |
| 10 | Test embed endpoint | API call (embed token) | [ ] |
| 11 | Open web-app & validate visuals | Browser | [ ] |
| 12 | Run ADX sanity queries | `.show tables`, `GetScenarios()` | [ ] |
| 13 | Check dynamic filter (last run) | `workspace.yaml` dynamicFilters config | [ ] |
| 14 | Performance sanity | Measure load & ADX query p50 | [ ] |
| 15 | Document findings | Onboarding notes | [ ] |

## 9. Next Steps

Proceed to parameter specifics: [Parameters & Reuse](./power-bi-parameters.md) and embedding governance: [Embedding & Security](./power-bi-embedding.md)
