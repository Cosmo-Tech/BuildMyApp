# Run templates

**Run templates** are JSON files that define and configure the workflow of your Cosmo Tech solution. More precisely, it can define which input parameters are available to the user and which steps in the run cycle are executed, and how. Each run template consists of a set of steps that can be activated or not, depending on the desired behavior. They orchestrate data processing, simulation execution, and result handling by defining a sequence of steps with dependencies.

## Basic structure

Run templates define:
- Execution steps: individual operations with commands and arguments (data fetch, validation, simulation, etc.).
- Step order: defines which steps mut be completed before others start in the execution sequence.

!!! note

    Steps can be either:

    - Sequential: steps run one after another using precedents.
    - Parallel: steps with the same precedents are run simultaneoulsy.
    - No cycles: circular dependencies are not allowed.

- Environment: variables and parameters used for the configuration (data paths and scenario information, API connection details, etc.)
- Commands: what gets executed during each step (direct commands, templates, etc.)

!!! example

    ```
    csm-orc run run.json
    ```
    ``` json title="run.json"
    {
     "steps": [
      {
        "id": "Run",
        "description": "Simple Run of a Cosmo Tech Simulator",
        "command": "main",
        "arguments": ["-i","$CSM_SIMULATION"],
          "environment": {
            "CSM_SIMULATION": {
              "description": "The simulation file that will be run"
            }
          }
        }
      ]
    }
    ```

## Types of run templates

Different types of run templates are available:

- Simulation templates: to execute digital twin simulations.
- ETL templates: to extract, transform and load data.
- Validation templates: to perform data quality checks.
- Processing templates: to create custom data workflows.

## Best practices

- Use clear, descriptive step names.
- Minimize dependencies for better parallelization.
- Use command templates for repeated operations.
- Test locally before deploying.
- Implement proper error handling with exit handlers.

## References

For more detailed information on run templates, please refer to:

- [Run Template Orchestrator documentation](https://cosmo-tech.github.io/run-orchestrator/latest/)
