# ETL

ETL processes for data transformation and loading in your solution.

## Overview

ETL (Extract, Transform, Load) run templates handle data processing workflows. They differ from simulation templates by focusing on data ingestion, transformation, and output to various storage systems rather than running simulations.

## What are ETL Templates?

ETL templates are run templates specifically designed for:
- **Extract**: Getting data from external sources
- **Transform**: Converting data formats and structures
- **Load**: Storing processed data in target systems

## Key Differences from Simulation Templates

Based on the meetings, ETL templates:
- Use the **Runner API** (newer) instead of Scenario API (legacy)
- Are marked with `"tags": ["data-source"]` for web app integration
- Focus on data processing rather than simulation execution
- Often create datasets for later use in simulations

## API Versions and Compatibility

### Scenario API vs Runner API
- **Scenario API**: Legacy approach (API v3.x, WebApp v6.x)
- **Runner API**: Modern approach (API v4.x+, WebApp v7.x+)
- **Compatibility Code**: Some solutions support both for transition

*[Placeholder: Code example showing API version switching]*

### Environment Variables
Different variables are provided:
- **Legacy**: `CSM_SCENARIO_ID`, scenario-based variables
- **Modern**: `CSM_RUNNER_ID`, runner-based variables

## ETL Workflow Pattern

Typical ETL workflow:
1. **Download Data**: Use `csm-data api run-load-data` or custom fetch
2. **Transform**: Convert Excel/CSV to standardized format
3. **Validate**: Check data quality and structure
4. **Load**: Send to Twin Data Layer, PostgreSQL, or other storage

*[Placeholder: JSON showing ETL step sequence]*

## Data Storage Options

Based on the meetings, options include:
- **Twin Data Layer (TDL)**: Graph-based storage (being deprecated)
- **Workspace Files**: Direct file upload to platform
- **PostgreSQL**: Relational database storage
- **Azure Data Explorer (ADX)**: Analytics database
- **Custom Storage**: S3, Azure Blob, etc.

## Using CoAL for ETL

The CosmoTech Acceleration Library provides:
- `csm-data` commands for common operations
- Data store (SQLite) for intermediate processing
- Standard connectors for various services

*[Placeholder: Example using csm data commands]*

## Web App Integration

ETL templates appear in the web app:
- **Data Source Templates**: Tagged for dataset creation
- **Create Dataset**: Runs ETL to generate new datasets
- **Dataset Selection**: Results can be used in simulations

## Common ETL Patterns

### Excel to CSV Transformation
1. Upload Excel file via web app
2. ETL reads and processes Excel data
3. Output standardized CSV files
4. Create dataset for simulation use

### API Data Integration
1. Fetch data from external APIs
2. Transform to required format
3. Store in platform-compatible format
4. Make available for scenarios

## Local Development

Test ETL templates locally:
- Use same environment variables as production
- Test with sample data files
- Validate output formats

## Best Practices

- Design for data validation at each step
- Handle errors gracefully
- Use intermediate storage (data store) for complex transformations
- Test with various data sizes
- Implement proper logging for debugging

## Migration Considerations

When moving from Scenario to Runner API:
- Update environment variable references
- Change API endpoints in custom code
- Test compatibility with both web app versions

## Further Reading

