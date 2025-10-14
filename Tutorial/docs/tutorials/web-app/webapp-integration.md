---
title: Web App Integration with workspace.yaml
summary: Configure your web application integration using workspace.yaml settings.
tags:
  - web-app
  - configuration
  - workspace
---

# Web App Integration with workspace.yaml

This guide explains how to configure web application integration using the `workspace.yaml` file. The workspace configuration file is the central point for defining how your web application connects to various services and displays data.

## 1. Overview

The `workspace.yaml` file, located in your project's deployment repository (typically in the `project/` folder), defines:

- Power BI workspace and report configurations
- ADX (Azure Data Explorer) database connections
- Event Hub connectors for data streaming
- Web application display options and filters
- Dynamic filters for automatic context application

## 2. workspace.yaml Structure

### 2.1 Basic Configuration

```yaml
key: <workspace_key>
name: <workspace_name>
description: <workspace_description>
solution:
  solutionId: <solution_id_reference>
```

The workspace key should match the value defined in your `variables.yaml` file and will be used to identify your workspace across the platform.

### 2.2 Power BI Configuration

The Power BI section configures which reports are embedded in your web application:

```yaml
sidecars:
  azure:
    powerbi:
      workspace:
        name: <powerbi_workspace_name>
        reports:
          - name: Example
            displayName: "Example Dashboard"
            file: powerbi/Example.pbix
            parameters:
              ADX_Cluster: <adx_cluster_url>
              ADX_Database: <adx_database_name>
```

**Key fields:**

- `workspace.name`: The Power BI workspace name (must match `powerbi_workspace_name` in `variables.yaml`)
- `reports[].name`: Internal identifier for the report
- `reports[].displayName`: User-friendly name shown in the web app
- `reports[].file`: Path to the .pbix file relative to your project folder
- `reports[].parameters`: Connection parameters passed to the Power BI report
  - `ADX_Cluster`: The Azure Data Explorer cluster URL
  - `ADX_Database`: The database name within the cluster

These parameters are automatically injected into your Power BI report's M queries during deployment, ensuring the report connects to the correct data source.

### 2.3 ADX Configuration

The ADX section defines database initialization and permissions:

```yaml
sidecars:
  azure:
    adx:
      scripts:
        - name: Initialization
          file: adx/scripts/00-Initialization.kql
          order: 0
```

**Key fields:**

- `scripts[].name`: Descriptive name for the script
- `scripts[].file`: Path to the KQL script file
- `scripts[].order`: Execution order (lower numbers run first)

The initialization script (typically `00-Initialization.kql`) should create:

- Tables for storing simulation data
- Materialized views for performance
- Functions for serving data to Power BI (e.g., `GetScenarios`, `GetMeasures`)
- Ingestion mappings for Event Hub data streams

### 2.4 Event Hub Configuration

Event Hubs stream simulation output data into ADX tables:

```yaml
sidecars:
  azure:
    eventhub:
      connectors:
        - name: ScenarioRunConnector
          targetTable: ScenarioRun
          dataFormat: CSV
          mapping: ScenarioRunMapping
        - name: MeasuresConnector
          targetTable: Measures
          dataFormat: CSV
          mapping: MeasuresMapping
```

**Key fields:**

- `connectors[].name`: Unique identifier for the connector
- `connectors[].targetTable`: ADX table name where data will be ingested
- `connectors[].dataFormat`: Data format (CSV, JSON, etc.)
- `connectors[].mapping`: Ingestion mapping name defined in your ADX initialization script

### 2.5 Web App Options

The `webApp.options` section controls how data is displayed in the web application:

```yaml
webApp:
  options:
    charts:
      scenarioView:
        dynamicFilters:
          - id: LastSimulationRun
            filterType: scenario
            field: runId
            value: latest
```

**Dynamic Filters:**

Dynamic filters automatically apply context to your Power BI reports without user intervention:

- `id`: Unique identifier for the filter
- `filterType`: Type of filter (`scenario`, `dataset`, etc.)
- `field`: The field/column to filter on
- `value`: The value or strategy (e.g., `latest` for the most recent run)

Common dynamic filter configurations:

```yaml
# Filter to show only the latest simulation run
- id: LastSimulationRun
  filterType: scenario
  field: runId
  value: latest

# Filter by specific site
- id: CurrentSite
  filterType: parameter
  field: Site
  value: ${SELECTED_SITE}

# Time window filter
- id: TimeWindow
  filterType: parameter
  field: TimeWindowHours
  value: 24
```

## 3. Integration Workflow

### 3.1 Deployment Process

1. **Configure variables.yaml**
   - Set `workspace_key`, `workspace_name`, `workspace_description`
   - Define `powerbi_workspace_name`
   - Configure permissions (`powerbi_permissions`, `adx_permissions`)

2. **Customize workspace.yaml**
   - Reference the correct Power BI reports
   - Set ADX connection parameters
   - Configure Event Hub connectors for your data model
   - Define dynamic filters for your use case

3. **Deploy with Babylon**
   ```bash
   babylon apply project --workspace
   ```

4. **Verify integration**
   - Access the web application
   - Confirm reports are embedded correctly
   - Validate dynamic filters are applied
   - Check ADX queries execute successfully

### 3.2 Configuration Checklist

| Step | Component | File | Verification |
|------|-----------|------|--------------|
| 1 | Workspace identity | `variables.yaml` | Unique keys set |
| 2 | Power BI workspace name | `variables.yaml` | Matches Azure |
| 3 | Report definitions | `workspace.yaml` | All reports listed |
| 4 | ADX parameters | `workspace.yaml` | Cluster & DB correct |
| 5 | ADX initialization | `workspace.yaml` + `.kql` | Script referenced |
| 6 | Event Hub connectors | `workspace.yaml` | Tables & mappings exist |
| 7 | Dynamic filters | `workspace.yaml` | Filter logic defined |
| 8 | Permissions | `variables.yaml` | All principals added |
| 9 | Deployment | Babylon command | No errors |
| 10 | Web app access | Browser | Reports load |

## 4. Common Configuration Patterns

### 4.1 Multi-Site Configuration

For applications serving multiple sites:

```yaml
webApp:
  options:
    charts:
      scenarioView:
        dynamicFilters:
          - id: SiteContext
            filterType: parameter
            field: Site
            value: ${USER_SELECTED_SITE}
          - id: LatestRun
            filterType: scenario
            field: runId
            value: latest
```

### 4.2 Time-Based Analytics

For time-window based analysis:

```yaml
webApp:
  options:
    charts:
      scenarioView:
        parameters:
          - name: TimeWindowHours
            value: 168  # 7 days
          - name: AggregationLevel
            value: hourly
```

### 4.3 Multi-Report Dashboard

For applications with multiple embedded reports:

```yaml
sidecars:
  azure:
    powerbi:
      workspace:
        reports:
          - name: Overview
            displayName: "Overview Dashboard"
            file: powerbi/Overview.pbix
            parameters:
              ADX_Cluster: ${ADX_CLUSTER}
              ADX_Database: ${ADX_DATABASE}
          - name: Detailed
            displayName: "Detailed Analysis"
            file: powerbi/Detailed.pbix
            parameters:
              ADX_Cluster: ${ADX_CLUSTER}
              ADX_Database: ${ADX_DATABASE}
```

## 5. Troubleshooting

### 5.1 Reports Not Appearing in Web App

**Check:**
1. Report is listed in `workspace.yaml` under `powerbi.workspace.reports`
2. The `.pbix` file exists at the specified path
3. Power BI workspace name matches between `variables.yaml` and `workspace.yaml`
4. User has appropriate permissions in `powerbi_permissions`

**Fix:**
```bash
# Re-deploy workspace configuration
babylon apply project --workspace
```

### 5.2 ADX Connection Errors

**Check:**
1. `ADX_Cluster` and `ADX_Database` parameters are correctly set
2. ADX initialization script has been executed
3. User is listed in `adx_permissions` with appropriate role

**Verify ADX connection:**
```kql
.show database <database_name> principals
```

### 5.3 Dynamic Filters Not Applied

**Check:**
1. Filter `id` is unique and properly defined
2. Field names match your ADX table schema
3. Filter type is appropriate for the data

**Test filter logic:**
```kql
// Verify the field exists in your data
ScenarioRun
| summarize by runId
| take 10
```

### 5.4 Event Hub Data Not Flowing

**Check:**
1. Event Hub connectors reference existing tables
2. Ingestion mappings are defined in ADX initialization script
3. Data format matches the connector configuration

**Verify ingestion:**
```kql
.show table <table_name> ingestion mappings
```

## 6. Best Practices

### 6.1 Configuration Management

- **Use variables:** Reference values from `variables.yaml` using `${VARIABLE_NAME}` syntax
- **Version control:** Keep workspace.yaml in git to track configuration changes
- **Documentation:** Add comments in YAML to explain non-obvious settings
- **Validation:** Test configuration changes in a development environment first

### 6.2 Performance Optimization

- **Limit dynamic filters:** Only apply filters that are truly needed
- **Pre-aggregate data:** Use ADX materialized views for frequently accessed queries
- **Connection parameters:** Ensure parameters are set once during deployment, not runtime

### 6.3 Security

- **Least privilege:** Grant only necessary permissions in `powerbi_permissions` and `adx_permissions`
- **No secrets in workspace.yaml:** Use secure parameter substitution
- **Audit access:** Regularly review who has access to workspace resources

## 7. Related Documentation

- [Power BI Overview](./power-bi.md) - Power BI integration details
- [Power BI Parameters](./power-bi-parameters.md) - Parameter configuration
- [ADX Database](./adx-database.md) - Azure Data Explorer setup
- [Frontend Architecture](./frontend.md) - Web app frontend details

## 8. Example workspace.yaml Template

```yaml
key: my-workspace
name: "My Production Workspace"
description: "Production workspace for simulation analytics"

solution:
  solutionId: my-solution

sidecars:
  azure:
    powerbi:
      workspace:
        name: MyPowerBIWorkspace
        reports:
          - name: Example
            displayName: "Analytics Dashboard"
            file: powerbi/Example.pbix
            parameters:
              ADX_Cluster: https://mycluster.region.kusto.windows.net
              ADX_Database: MyDatabase
    
    adx:
      scripts:
        - name: Initialization
          file: adx/scripts/00-Initialization.kql
          order: 0
    
    eventhub:
      connectors:
        - name: ScenarioRunConnector
          targetTable: ScenarioRun
          dataFormat: CSV
          mapping: ScenarioRunMapping
        - name: MeasuresConnector
          targetTable: Measures
          dataFormat: CSV
          mapping: MeasuresMapping

webApp:
  options:
    charts:
      scenarioView:
        dynamicFilters:
          - id: LastSimulationRun
            filterType: scenario
            field: runId
            value: latest
```

## 9. Next Steps

After configuring your workspace.yaml:

1. **Deploy:** Use Babylon to apply your workspace configuration
2. **Verify:** Check that all resources are created correctly
3. **Test:** Access the web application and validate functionality
4. **Monitor:** Observe performance and user experience
5. **Iterate:** Refine configuration based on usage patterns

For deployment instructions, see the [Hands-On Discovery](../../on_hands/02_Discovery.md) guide.
