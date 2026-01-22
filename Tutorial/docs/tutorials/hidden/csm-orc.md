# CSM-ORC
The **Cosmo Tech Orchestrator** (called **CSM-ORC** or **Run Orchestrator**) is a command line interface (CLI) used to execute run templates defined in JSON format. It enables the user to define commands and how they interact with each other (in which order they are executed, how they interact with the environment, etc.) in order to orchestrate the different steps in your solution workflow, from data fetching to simulation execution and result processing.
Thus, with CSM-ORC, you can:
- Define workflows through JSON-based run templates,
- Execute steps in a controlled order with dependencies,
- Use environment variables for configuration,
- Create reusable command templates,
- Run the same workflow locally and in production.

## CLI usage
### Basic commands
Two commands exist:
- `run`: used to run the given template file by reading said file, interpreting it and executing the associated contents. It is the main usage of CSM-ORC.

!!! note

    In case you are in a Python venv, the venv is activated before any command is run.

- `list-templates`: used to show a list of pre-available command templates.

Hereinbelow is an table of the most commonly used commands:
| Command                                                                | Description                          |
| ---------------------------------------------------------------------- | ------------------------------------ |
| `csm-orc run <JSON file>`                                              | To execute a run template            |
| `csm-orc run --dry-run <JSON file>`                                    | To validate without executing        |
| `csm-orc run --display-env --gen-env-target <env file> <JSON file>`    | To generate an environment file      |
| `csm-orc list-templates`                                               | To show available command templates  |

## Basic run template structure
The main elements of a JSON run template file are:
- commandTemplates
- object
- steps
- identifier
- argument
- environment variables

!!! example

    ```
    csm-orc run run.json
    ```
    ``` title="run.json"
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

## References
For more detailed information about CSM-ORC, please refer to the official documentation :
- [Run Template Orchestrator](https://cosmo-tech.github.io/run-orchestrator/latest/)
