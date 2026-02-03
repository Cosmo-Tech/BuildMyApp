# Azure Data Explorer

Azure Data Explorer (ADX) is a fast, fully managed data analytics service optimized for real-time analysis of large volumes of data.

ADX serves as the **data warehouse** for Cosmo Tech simulation results, storing:

- **ProbesMeasures**: time-series data from simulation probes.
- **ScenarioRun**: execution details and run metadata.
- **ScenarioMetadata**: scenario configurations and parameters.
- **SimulationTotalFacts**: aggregated metrics and KPIs.

Using ADX offers several key benefits:

- **High Performance**: query billions of records in seconds.
- **Real-time Ingestion**: data flows from Event Hub to ADX automatically.
- **Time-Series Optimized**: built for temporal data analysis.
- **KQL Language**: powerful Kusto Query Language for data exploration.
- **Power BI Integration**: direct connection for visualization.
- **Scalability**: handles massive simulation datasets efficiently.

## Data flow

1. **Simulation runs** and generates output data.
2. **Event Hub** receives and streams the data.
3. **ADX** ingests data in real-time via connectors.
4. **Data is stored** in structured tables with compression.
5. **Users query** the data using KQL or visualize in Power BI.

## Common use cases

ADX can be used in several use cases, most commonly:

1. **Analyze Simulation Results**: query and compare scenario outcomes.
2. **Track Metrics Over Time**: monitor KPIs across multiple runs.
3. **Generate Reports**: create custom analytics dashboards.
4. **Data Exploration**: investigate simulation behavior and patterns.
5. **Performance Analysis**: identify bottlenecks and optimization opportunities.

## Example query

```kql
ProbesMeasures
| where SimulationRun == "run-abc123"
| where Timestamp >= ago(1h)
| summarize avg(Value) by bin(Timestamp, 5m), ProbeName
| render timechart
```

!!! note

    This query retrieves probe measurements from the last hour and displays average values in a time chart.

## Database configuration

ADX databases are automatically created when a workspace is deployed with the following naming convention:

```
<organization_id>-<workspace_key>
```

Configuration includes:

- **Retention policies**: default 365 days.
- **Permissions**: user access controls.
- **Initialization scripts**: table schemas and mappings.
- **Event Hub connectors**: automatic data ingestion.

## Integration with Power BI

ADX connects directly to Power BI, enabling:

- Real-time dashboard updates.
- Interactive data exploration.
- Custom visualizations.
- Scenario comparison reports.

!!! note

    Connect using the Azure Data Explorer connector in Power BI Desktop with your cluster URL and database name.
