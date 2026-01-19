# Workspace

Workspaces and their management via the CosmoTech API.

## Workspace Elements

```yaml
kind: Workspace
namespace: 
  remote: {{remote}}
  state_id: "{{state_id}}"
  context: "{{context_id}}"
  platform: {{platform}}
metadata:
  selector:
    organization_id: {{organization_id}}
    solution_id: {{services['api.solution_id']}}
spec:
    key: {{workspace_key}}
    name: {{workspace_name}}
    description: {{workspace_description}}
    version: string
    tags: {}
    solution:
      solutionId: {{services['api.solution_id']}}
      defaultRunTemplateDataset: null
    webapp:
      url: string
      iframes: {}
      options: {}
    sendInputToDataWarehouse: boolean
    useDedicatedEventHubNamespace: boolean
    dedicatedEventHubSasKeyName: string
    sendScenarioRunToEventHub: boolean
    sendScenarioMetadataToEventHub: boolean
    datasetCopy: boolean
    webApp:
      url: string
      options: {}
    security: {{security}}
```

## Namespace

The elements in this category have configured on previous Babylon uses and are configuered as such

## Metadata

The elements in this category have configured on previous Babylon uses and are configuered as such

## Spec

In this part, you will be able to enter the different side elements for workspace, such as EventHub or PowerBI.

## Payload

The following elements require user input:

- **`key`** - The name the workspace will have in subprocesses such as Azure use
- **`name`** - The name the workspace will have displayed in the WebApp
- **`description`** - A description of the workspace
- **`version`** - The workspace version `MAJOR.MINOR.PATCH`
- **`tags`** - The list of tags
- **`solution`** - The solution has three parts: `solutionId` is the ID of the linked solution; `runTemplateFilter` is the list of Solution Run Template IDs to filter; `defaultRunTemplateDataset` is a map of RunTemplate/DatasetID to set a default dataset for a Run Template
- **`webapp`** - The webapp has three parts: `url` is the WebApp URL; `iframes` is a map of iframeKey/iframeURL; `options` are free-form options for the WebApp
- **`sendInputToDataWarehouse`** - Whether or not to send the dataset values and the input parameter values to the DataWarehouse prior to the ScenarioRun
- **`useDedicatedEventHubNamespace`** - Whether or not to use a dedicated Azure Event Hub Namespace. The name must be `<organization_id>-<workspace_id>` (in lowercase). The Namespace must contain two Event Hubs named `probesmeasures` and `scenariorun`
- **`dedicatedEventHubSasKeyName`** - The dedicated Event Hub SAS key name, defaults to `RootManageSharedAccessKey`
- **`dedicatedEventHubAuthenticationStrategy`** - The Event Hub authentication strategy: `SHARED_ACCESS_POLICY` or `TENANT_CLIENT_CREDENTIALS`
- **`sendScenarioRunToEventHub`** - Whether or not the ScenarioRun is sent to the Event Hub
- **`sendScenarioMetadataToEventHub`** - Whether or not to send scenario metadata to Azure Event Hub Namespace for this workspace. The Event Hub Namespace must be named `<organization_id>-<workspace_id>` (in lowercase). This Namespace must also contain two Event Hubs named `scenariometadata` and `scenariorunmetadata`
- **`datasetCopy`** - Activate the copy of dataset on scenario creation, meaning that each scenario created in this workspace will create this copy
- **`security`** - Constituted of a default security role for non-specified users and a list of specified users with their relative security roles. If no security was provided at creation, the default role for the workspace will be `none` and the only user in the access control list will be the user who created the workspace with the role `admin` 

