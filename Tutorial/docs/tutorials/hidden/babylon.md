# Babylon

Babylon is Cosmo Tech's proprietary command-line tool for deploying, configuring, and managing platform resources.

It is a deployment orchestration tool developed by Cosmo Tech that simplifies the process of creating and managing solutions, workspaces, datasets, and other platform resources. It uses declarative YAML manifests to define infrastructure and configurations, similar to Kubernetes.

Its key features are:

- **Declarative configuration**: define resources in YAML manifests.
- **State management**: track and manage resource states across environments.
- **Idempotent operations**: apply configurations multiple times safely.
- **Multi-environment support**: manage dev, staging, and production environments.
- **Version control friendly**: store configurations in Git repositories.
- **Template support**: use variables and templates for reusable configurations.
- **Dependency management**: handle relationships between resources automatically.

## Why Babylon?

### Before Babylon

Without Babylon, deploying Cosmo Tech resources required:

- Manual API calls for each resource,
- Complex scripts to coordinate deployments,
- Difficult tracking of resource states,
- Error-prone configuration management,
- No easy way to replicate environments.

### With Babylon

Babylon provides:

- Single command deployment of entire environments,
- Automatic handling of resource dependencies,
- Consistent configuration across environments,
- Easy rollback and version management,
- Template-based configuration for reusability.

## Core concepts

### Namespaces

Namespaces organize and isolate resources:

- **`remote`**: environment identifier (dev, staging, production).
- **`state_id`**: unique identifier for this deployment state.
- **`context`**: logical grouping of related resources.
- **`platform`**: target CosmoTech platform URL.

### Payload

The payload contains the actual resource configuration that will be sent to the Cosmo Tech API.

## Resource types

### Solution

Define and deploy solutions:

```yaml
kind: Solution
namespace:
  remote: production
  state_id: "solution-v1"
  context: "manufacturing"
  platform: https://api.cosmotech.com
metadata:
  selector:
    organization_id: o-a1b2c3d4e5f6g7
spec:
  payload:
    key: manufacturing-sim
    name: Manufacturing Simulation
    repository: myregistry.azurecr.io/manufacturing-sim
    version: 2.1.0
    sdkVersion: "11.3"
    tags:
      - manufacturing
      - optimization
    parameters:
      - id: ProductionRate
        varType: number
    runTemplates:
      - id: Simulation
        name: Run Simulation
        computeSize: basic
```

### Workspace

Define workspaces with all configurations:

```yaml
kind: Workspace
namespace:
  remote: production
  state_id: "workspace-main"
  context: "manufacturing"
  platform: https://api.cosmotech.com
metadata:
  selector:
    organization_id: o-a1b2c3d4e5f6g7
    solution_id: s-a1b2c3d4e5f6g7
spec:
  sidecars:
    azure:
      adx:
        database:
          create: true
          retention: 365
      eventhub:
        create: true
      powerbi:
        workspace:
          name: Manufacturing Analytics
  payload:
    key: main-workspace
    name: Main Workspace
    description: Production workspace for manufacturing
    useDedicatedEventHubNamespace: true
    datasetCopy: false
```

### Dataset

Manage datasets:

```yaml
kind: Dataset
namespace:
  remote: production
  state_id: "dataset-reference"
  context: "manufacturing"
  platform: https://api.cosmotech.com
metadata:
  selector:
    organization_id: o-a1b2c3d4e5f6g7
spec:
  payload:
    name: Reference Dataset
    description: Base data for simulations
    connector:
      id: c-ADTConnector
      parametersValues:
        AZURE_DIGITAL_TWINS_URL: https://myadt.api.weu.digitaltwins.azure.net
```

## Using templates and variables

### Variable substitution

Babylon supports Jinja2-style variable substitution:

```yaml
spec:
  payload:
    key: {{workspace_key}}
    name: {{workspace_name}}
    description: {{workspace_description}}
    solution:
      solutionId: {{services['api.solution_id']}}
```

### Variable files

Define variables in separate files:

**variables.yaml:**
```yaml
workspace_key: production-workspace
workspace_name: Production Workspace
workspace_description: Main production environment
organization_id: o-a1b2c3d4e5f6g7
powerbi_workspace_name: Analytics Workspace
```

### Environment-specific variables

Organize variables by environment:

```
config/
├── common.yaml
├── dev/
│   └── variables.yaml
├── staging/
│   └── variables.yaml
└── production/
    └── variables.yaml
```
## State management

### State files

Babylon maintains state files to track deployed resources:

```
.babylon/
├── states/
│   ├── production/
│   │   ├── solution-v1.state
│   │   └── workspace-main.state
│   └── dev/
│       ├── solution-v1.state
│       └── workspace-dev.state
```

### State commands

```bash
# List states
babylon state list

# Show state details
babylon state show production/solution-v1

# Remove state (without deleting resource)
babylon state rm production/solution-v1

# Import existing resource into state
babylon state import solution my-solution --organization o-a1b2c3d4e5f6g7
```

## Sidecars

Sidecars are Azure resource configurations managed alongside platform resources:

### Azure Data Explorer (ADX)

```yaml
sidecars:
  azure:
    adx:
      database:
        create: true
        retention: 365
        permissions:
          - email: user@company.com
            role: Admin
        scripts:
          - id: init
            name: 00-Initialization.kql
            path: "adx/scripts"
```

### Event Hub

```yaml
sidecars:
  azure:
    eventhub:
      create: true
      consumers:
        - displayName: adx
          entity: ProbesMeasures
      connectors:
        - table_name: ProbesMeasures
          consumer_group: adx
          connection_name: ProbesMeasures
          database_target: "{{organization_id}}-{{workspace_key}}"
          format: JSON
          compression: Gzip
```

### Power BI

```yaml
sidecars:
  azure:
    powerbi:
      workspace:
        name: Analytics Workspace
        reports:
          - name: Dashboard
            type: scenario
            path: "powerbi/Dashboard.pbix"
            tag: main
            parameters:
              - id: ADX_Cluster
                value: "https://mycluster.westeurope.kusto.windows.net"
        permissions:
          - email: user@company.com
            role: Admin
```

## References

For more detailed information on Docker, please refer to:

-[Babylon](https://cosmo-tech.github.io/Babylon-End-User-Doc/latest/)
