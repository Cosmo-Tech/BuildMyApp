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

## 1. What’s handled by the backend

- Acquire AAD tokens (service principal or user)
- Generate Power BI embed tokens for the selected report/dataset
- Apply identity mappings (optional) for RLS
- Expose a simplified API endpoint to the web-app

Your frontend only consumes the embed configuration and applies high-level report filters (e.g., site) when needed.

## 2. Required Configuration

| Setting | Where | Example |
|--------|-------|---------|
| Workspace Id | Backend config/secret store | `00000000-0000-0000-0000-000000000000` |
| Report Id | Backend config | `11111111-1111-1111-1111-111111111111` |
| Dataset Id | Backend config | `22222222-2222-2222-2222-222222222222` |
| AAD App (Client Id/Secret) | Secret store | Azure Key Vault |
| Allowed Sites / Tenants | App settings | `LYON_01, BERLIN_02` |

## 3. RLS and Identity Mapping

- Define roles in the Power BI dataset (e.g., TenantIsolation, SiteIsolation)
- Backend maps the calling user/tenant/site to a role or passes identities in the embed token
- Keep the mapping source-of-truth server-side (e.g., DB or configuration)

Checklist:
- [ ] RLS roles exist and are tested in Desktop (View as)
- [ ] Backend mapping enforces least privilege
- [ ] No ability to request arbitrary site via client-only params

## 4. Frontend Responsibilities

- Request the embed configuration from the backend endpoint
- Render the report with the provided embed URL/token (SDK usage is abstracted by the platform)
- Apply contextual filters (e.g., default site) via backend-approved mechanisms
- Do NOT store secrets nor generate tokens client-side

## 5. Operational Runbook

- Rotate client secrets regularly (Key Vault)
- Monitor token issuance failures and latency
- Track report load times and error rates

## 6. Troubleshooting

| Symptom | Likely cause | Action |
|--------|--------------|--------|
| Report loads but shows empty data | RLS filtering out data | Validate role mapping and site passed |
| 403 on embed token | AAD app permission or workspace access | Re-check service principal access |
| Slow visuals | Query heavy / dataset design | See performance section below |

## 7. Performance

- Prefer aggregated tables or ADX serving functions returning summarized data
- Validate DirectQuery vs Import mix; consider aggregations and incremental refresh
- Prewarm via scheduled refresh if suitable

## 8. Related

- [Parameters & Reuse](./power-bi-parameters.md)
- [ADX Functions & Performance](./adx-functions-performance.md)
