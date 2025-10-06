---
title: Power BI Embedding & Security
summary: Embed interactive Power BI reports securely in the web-app with governance, caching, and performance best practices.
tags:
  - power-bi
  - embedding
  - security
  - web-app
---

# Power BI Embedding & Security

This guide covers embedding models, authentication flows, Row Level Security (RLS), and performance tuning.

## 1. Embedding Models

| Scenario | Model | Notes |
|----------|-------|-------|
| Internal users (AAD) | User owns data | Uses user AAD token; per-user RLS naturally applied |
| External / multi-tenant | Service principal | App gets embed token; enforce tenant filter via RLS |
| Public showcase | Publish-to-web (avoid) | Not secure; never use for production |

Prefer service principal + embed token generation backend-side for multi-tenant.

## 2. Architecture

```
Browser -> Web-App (frontend) -> Backend (token endpoint) -> AAD -> Power BI Service
                                           |
                                           +--> ADX (DirectQuery via PBI service)
```

## 3. Backend: Generate Embed Token

Pseudo-code:

```python
from msal import ConfidentialClientApplication
import requests

def get_embed_token(report_id, workspace_id, dataset_id):
    app = ConfidentialClientApplication(
        client_id=CLIENT_ID,
        authority=f"https://login.microsoftonline.com/{TENANT_ID}",
        client_credential=CLIENT_SECRET
    )
    scope = ["https://analysis.windows.net/powerbi/api/.default"]
    result = app.acquire_token_silent(scope, account=None)
    if not result:
        result = app.acquire_token_for_client(scopes=scope)
    access_token = result['access_token']

    body = {
        "datasets": [{"id": dataset_id}],
        "reports": [{"id": report_id}],
        "targetWorkspaces": [{"id": workspace_id}]
    }
    r = requests.post(
        "https://api.powerbi.com/v1.0/myorg/GenerateToken",
        headers={"Authorization": f"Bearer {access_token}"},
        json=body
    )
    return r.json()['token']
```

## 4. Frontend: Embed

```ts
import * as powerbi from 'powerbi-client';

const models = powerbi.models;

function embedReport(container: HTMLElement, embedConfig: { id: string; embedUrl: string; token: string; site: string; }) {
  const config: powerbi.IEmbedConfiguration = {
    type: 'report',
    id: embedConfig.id,
    embedUrl: embedConfig.embedUrl,
    accessToken: embedConfig.token,
    tokenType: models.TokenType.Embed,
    settings: { panes: { filters: { visible: false } } }
  };
  const report = powerbi.embed(container, config) as powerbi.Report;
  // Apply site filter from contextual data
  report.on('loaded', async () => {
    await report.setFilters([
      {
        $schema: 'http://powerbi.com/product/schema#basic',
        target: { table: 'SiteFilter', column: 'Site' },
        operator: 'In',
        values: [embedConfig.site]
      }
    ]);
  });
}
```

## 5. Security Layers

| Layer | Control | Purpose |
|-------|---------|---------|
| AAD App | App roles / scopes | Restrict who can call token endpoint |
| Backend | Tenant/site validation | Prevent arbitrary site injection |
| Power BI | RLS roles | Enforce data slice per user/tenant |
| Dataset | Parameterization | Ensure minimal data exposure |

### Example RLS Role (Site Isolation)

In Power BI Desktop > Modeling > Manage Roles:
```
[Site] = USERPRINCIPALNAME()\"s site mapping OR LOOKUPVALUE(SiteMap[Site], SiteMap[UserPrincipalName], USERPRINCIPALNAME())
```
(Alternatively pass site via embed token identities.)

## 6. Caching & Performance

| Layer | Strategy | Notes |
|-------|----------|-------|
| Power BI | Aggregations | Combine DirectQuery (hot) + Import (warm) |
| Power BI | Incremental refresh | Partition large historical tables |
| ADX | Materialized views | Pre-aggregate heavy metrics |
| Web-App | Embed lifecycle | Reuse report instance across tabs, avoid reloading |

### Measuring
Use Performance Analyzer in Power BI Desktop and `QueryTrace` in ADX.

## 7. Observability

- Log token requests (timestamp, workspace, dataset, site, user/tenant)
- Alert on unusual volume or failed token generation
- Track report load times (frontend performance marks)

## 8. Checklist

- [ ] Service principal least-privilege
- [ ] Embed token TTL minimal (e.g. 1h)
- [ ] RLS validated per tenant
- [ ] Site filter applied programmatically
- [ ] No hard-coded secrets in frontend

## 9. Related

- [Parameters & Reuse](./power-bi-parameters.md)
- [ADX Functions & Performance](./adx-functions-performance.md)
