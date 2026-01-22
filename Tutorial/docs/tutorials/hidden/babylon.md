# Babylon

Babylon is CosmoTech's proprietary command-line tool for deploying, configuring, and managing platform resources.

## Overview

Babylon is a deployment orchestration tool developed by CosmoTech that simplifies the process of creating and managing solutions, workspaces, datasets, and other platform resources. It uses declarative YAML manifests to define infrastructure and configurations, similar to Kubernetes.

## Key Features

- **Declarative Configuration** - Define resources in YAML manifests
- **State Management** - Track and manage resource states across environments
- **Idempotent Operations** - Apply configurations multiple times safely
- **Multi-Environment Support** - Manage dev, staging, and production environments
- **Version Control Friendly** - Store configurations in Git repositories
- **Template Support** - Use variables and templates for reusable configurations
- **Dependency Management** - Handle relationships between resources automatically

## Why Babylon?

### Before Babylon

Without Babylon, deploying CosmoTech resources required:
- Manual API calls for each resource
- Complex scripts to coordinate deployments
- Difficult tracking of resource states
- Error-prone configuration management
- No easy way to replicate environments

### With Babylon

Babylon provides:
- Single command deployment of entire environments
- Automatic handling of resource dependencies
- Consistent configuration across environments
- Easy rollback and version management
- Template-based configuration for reusability

## Core Concepts

### Namespaces

Namespaces organize and isolate resources:

- **`remote`** - Environment identifier (dev, staging, production)
- **`state_id`** - Unique identifier for this deployment state
- **`context`** - Logical grouping of related resources
- **`platform`** - Target CosmoTech platform URL

### Payload

The payload contains the actual resource configuration that will be sent to the CosmoTech API.

## Resource Types

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

## Using Templates and Variables

### Variable Substitution

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

### Variable Files

Define variables in separate files:

**variables.yaml:**
```yaml
workspace_key: production-workspace
workspace_name: Production Workspace
workspace_description: Main production environment
organization_id: o-a1b2c3d4e5f6g7
powerbi_workspace_name: Analytics Workspace
```

### Environment-Specific Variables

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
## State Management

### State Files

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

### State Commands

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