# ETL

ETL (**E**xtract, **T**ransform, **L**oad) run templates handle data processing workflows. They differ from simulation templates by focusing on data ingestion, transformation and output to various storage systems rather than running simulations.

The Cosmo Tech Platform can store multiple ETL scripts, developed by system integrators, per solution and run them to manipulate the twin data layer (dataset, results, graph, etc.) and to perform any data processing needed by system integrators such as follows:

- Create and populate datasets with customer format input data
- Refresh/update a full dataset with customer format input data
- Modify/update a part of a dataset with customer format input data
- Validate a complete dataset
- Create business KPIs that are based on a scenario run results (probes, etc.) and an existing dataset,
- Create a new dataset based on an existing dataset or external data.

## 	Workflow

The typical workflow of an ETL is the following:

1. **Download data**: using `csm-data api run-load-data` or a custom code.
2. **Transform**: converting Excel/CSV formats to a standardized format.
3. **Validate**: checking the data quality and its structure.
4. **Load**: sending data to different storages such as Twin Data Layer (to create flowcharts, etc.), PostgreSQL (if you want an SQL database), workspaces files, ADX, S3, etc.

!!! note 

    [CoAL](https://cosmo-tech.github.io/CosmoTech-Acceleration-Library/latest/) can be used for common operations, for intermediate processing, etc.

    ``` json Example of an ETL in a solution.json file
    "runTemplates": [
        {
            "id": "what_if",
            "name": "Basic what-if run type",
            "description": "Basic what-if run type with a default Run step.",
            "tags": [
                "basic_simulation",
                "CoMETS"
            ],
            "parameterGroups": [
                "bar_parameters"
            ],
            "fetchDatasets": true,
            "fetchScenarioParameters": true,
            "applyParameters": true,
            "parametersJson": true,
            "parametersHandlerSource": "local",
            "csmSimulation": "BusinessApp_Simulation"
        },
        {
            "id": "etl_with_local_file",
            "tags": [
                "datasource"
            ],
            "parameterGroups": [
                "bar_parameters",
                "local_file"
            ]
        },
        {
            "id": "etl_with_azure_storage",
            "tags": [
                "datasource"
            ],
            "parameterGroups": [
                "bar_parameters",
                "azure_storage"
            ]
        }
    ]
    ```

## Integration in the web application

ETLs can be integrated into the web application in two different ways:

- If an ETL is tagged `["datasource"]`, it appears in the Dataset Manager view.
- If an ETL is not tagged, it appears in the Scenario view.

## Best pratices

- Test your ETL templates locally (use the same environment variables as the production, test with sample data files, validate the output formats, etc.).
- Test with various data sizes.
- Design for data validation at each step.
- Use intermediate storage (data store) for complex transformations.

## References

For more detailed information about CSM-ORC, please refer to the official documentation:

- [Chapter "ETL", in the Platform Doc](https://portal.cosmotech.com/docs//documentation/platform_help/3.3/Content/Platform%20Help/Data%20Integration/ETL/ETL.htm)
