---
title: Frontend Architecture & Integration
summary: Learn about the frontend architecture and customization of your web application.
tags:
  - frontend
  - web-app
  - power-bi
  - adx
---

# Frontend Architecture & Integration

The frontend orchestrates context (site, user, time window), passes parameters to embedded analytics, and optimizes perceived performance.

## 1. Layers

| Layer | Responsibility | Tech |
|-------|----------------|------|
| Routing | Page/screen selection | React Router / framework router |
| State | Global context (site, auth, feature flags) | Redux / Zustand / Context API |
| Services | REST / GraphQL / ADX proxy calls | Fetch / Axios |
| Embedding | Power BI iframe + SDK | `powerbi-client` |
| Components | UI widgets / filters / charts | Design system |

## 2. Parameter Propagation Flow

```
User selects Site -> Global State update -> ADX function queries (server) & Embed Filters (client) -> Report visuals refresh
```

Core rule: single source of truth for each parameter (avoid duplicate local state).

## 3. Environment Configuration

Expose environment via `.env` or config endpoint:

| Variable | Purpose |
|----------|---------|
| `PBI_AUTH_MODE` | `service-principal` / `user` |
| `PBI_BACKEND_TOKEN_URL` | Token fetch endpoint |
| `ADX_CLUSTER_URL` | Display / debugging |
| `DEFAULT_SITE` | Initial filter |

## 4. Fetching Embed Token

```ts
async function fetchEmbedToken(reportId: string) {
  const res = await fetch(`/api/powerbi/token?reportId=${reportId}` , { credentials: 'include' });
  if (!res.ok) throw new Error('Token failed');
  return res.json(); // { token, embedUrl, datasetId }
}
```

## 5. Context Synchronization

```ts
const [site, setSite] = useState(initialSite);
useEffect(() => {
  reportRef.current?.setFilters([
    { $schema: 'http://powerbi.com/product/schema#basic', target: { table: 'SiteFilter', column: 'Site' }, operator: 'In', values: [site] }
  ]);
}, [site]);
```

## 6. Performance Optimizations

| Concern | Mitigation |
|---------|------------|
| Slow initial dashboard | Lazy-load Power BI SDK & use skeleton loaders |
| Excess re-renders | Memoize filter components & avoid prop drilling |
| Network chatter | Debounce user input before triggering queries |
| Large bundle | Code-split embedding feature |

## 7. Error Handling Patterns

```ts
try {
  const token = await fetchEmbedToken(reportId);
  embedReport(container, { ...token, site });
} catch (e) {
  logError(e);
  showToast('Unable to load analytics');
}
```

Surface fallback UI with actionable steps (retry / contact support).

## 8. Observability

- Custom performance marks (e.g. `analytics_loaded`)
- Track embed token latency
- Capture front-end errors (Sentry / App Insights)

## 9. Security Hygiene

- Never store secrets in frontend bundle
- Validate all query parameter values server-side
- Sanitize user-entered strings passed to functions

## 10. Next

Proceed to ADX or Power BI deep dives:

- [ADX Overview](./adx-database.md)
- [Power BI Overview](./power-bi.md)
