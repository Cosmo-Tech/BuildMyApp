---
title: Power BI Embedding & Security
summary: Back-end managed embedding — configure workspaces, datasets, and RLS; validate filters and performance.
tags:
  - power-bi
  - embedding
  - security
  - web-app
---

# Power BI Embedding & Security (Backend-managed)

In this solution, embedding is implemented in the backend. You do not write frontend embedding code. Instead, you configure the embedding context and validate security and performance.

## 0. Onboarding Flow Snapshot

```
Clone repos → Verify permissions (variables.yaml / gen_adx_permissions.sh) → ADX bootstrap (00-Initialization.kql via backend) → Publish Example.pbix → Backend embed endpoint test → Open web-app → Validate RLS & filters
```

## 1. What’s handled by the backend (Recap)

- Acquire AAD tokens (service principal or user)
- Generate Power BI embed tokens for the selected report/dataset
- Apply identity mappings (optional) for RLS
- Expose a simplified API endpoint to the web-app

Your frontend only consumes the embed configuration and applies high-level report filters (e.g., site) when needed.

## 2. Required Configuration (From `variables.yaml`, `workspace.yaml`, `solution.yaml`)

| Setting / Element | Source File | YAML Path | Notes |
|-------------------|-------------|-----------|-------|
| Power BI Workspace Name | `variables.yaml` | `powerbi_workspace_name` | Target workspace for Example report |
| Report binding | `workspace.yaml` | `sidecars.azure.powerbi.workspace.reports` | Ensures Example.pbix deployed with parameters |
| Report parameters (ADX cluster/db) | `workspace.yaml` | `reports[].parameters` | Provide `ADX_Cluster`, `ADX_Database` values |
| ADX init script | `workspace.yaml` | `sidecars.azure.adx.scripts` | Executes `00-Initialization.kql` |
| Event Hub connectors | `workspace.yaml` | `sidecars.azure.eventhub.connectors` | Stream simulation outputs into ADX tables |
| Dynamic Filters | `workspace.yaml` | `webApp.options.charts.scenarioView.dynamicFilters` | Auto apply `LastSimulationRun` |
| Run Templates | `solution.yaml` | `spec.runTemplates` | Provide simulation & ETL context for data feeding report |

If a principal is missing, update `variables.yaml` in a PR and re-run provisioning scripts.

## 3. Permission Bootstrap

Use (if provided) `gen_adx_permissions.sh` and backend automation to ensure ADX and Power BI roles are applied. Confirm in ADX:
```kusto
.show database principals
```
And in Power BI workspace (Admin portal) that the service principal has Admin rights.

## 4. RLS & Identity Mapping (Dataset)

If the Example PBIX includes RLS roles (e.g., SiteIsolation), verify them in Desktop: Modeling > Manage Roles. If not present yet, coordinate with analytics engineer to add roles BEFORE wide distribution.

Recommended role pattern:
- `TenantIsolation` (filters organization/workspace id if multi-tenant)
- `ScenarioScope` (limits to allowed scenario set)

Backend maps user/app context → role(s) when generating embed token (or injects effective identity).

## 5. Validation Steps

1. Publish `Example.pbix` to workspace (`powerbi_workspace_name`).
2. Call embed endpoint (e.g., `GET /api/analytics/embed/report/{id}`) and capture JSON (id, embedUrl, token expiry).
3. Open web-app dashboard; verify:
   - Data loads without manual sign-in (service principal path)
   - Only allowed scenarios/probes appear
   - Switching TimeWindowHours (if parameterized) re-queries ADX
4. In ADX, list recent queries:
```kusto
.show queries | where StartedOn > ago(10m) | where Text has "GetMeasures" or Text has "GetScenarios"
```
Confirm calls originate with expected principal.
5. Measure render time (<5s for main visuals) using browser dev tools.

## 6. Troubleshooting (Extended)

| Symptom | Likely Cause | Diagnostic | Fix |
|---------|--------------|-----------|-----|
| Empty visuals | RLS over-filtering | View as role in Desktop | Adjust role filter / identity mapping |
| 403 embed call | Missing workspace permission | Power BI workspace access panel | Add service principal / user |
| Slow queries | Function expanding large dynamic fields | Inspect ADX query text | Add selective projection / MV |
| Token expires too soon | Short TTL | Backend token config | Increase within security guidelines |
| Wrong dataset | Misconfigured IDs | Compare variables.yaml vs embed response | Correct backend config |

## 7. Performance (Applied)

- Monitor `GetMeasures` query duration distribution (p50 < 2s target in dev) using `.show queries`.
- If dynamic expansion (bag_unpack) dominates cost, pre-flatten heavy fields in a materialized view and reference that in functions.
- Schedule dataset refresh only if Import mode; for DirectQuery ensure aggregations are defined before scaling.

## 8. Operational Runbook (Daily / Release)

| Task | Frequency | Owner |
|------|-----------|-------|
| Check failed embed calls (5xx / 4xx) | Daily | Backend Ops |
| Review ADX heavy queries (>10s) | Daily | Data Eng |
| Validate RLS after adding new users | On change | Platform Admin |
| Refresh Example PBIX (if Import) | Release / Data change | BI Dev |

## 9. Related

- [Parameters & Reuse](./power-bi-parameters.md)
- [ADX Functions & Performance](./adx-functions-performance.md)
