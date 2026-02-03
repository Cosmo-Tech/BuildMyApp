# Workspace

The `workspace.yaml` file declares the elements used to deploy a Cosmo Tech workspace and manages it through the Cosmo Tech API.

## Workspace elements

Several elements and parameters are to be declared in the `workspace.yaml` file, in order to configure the solution properly:

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
  sidecars:
  payload:
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

!!! note

    The elements in the `namespace` and `metadata` categories are configured in previous Babylon uses and are configured as such.

### Sidecars

`sidecars` enable the configuration of external services for the workspace, such as Event Hub, Power BI, etc.

### Payload

The following elements require user input:

- **`key`**: the name the workspace will have in subprocesses such as Azure use.
- **`name`**: the name the workspace will have displayed in the web application.
- **`description`**: a description of the workspace.
- **`version`**: the workspace version `MAJOR.MINOR.PATCH`.
- **`tags`**: the list of tags.
- **`solution`**: the solution has three parts:
  * `solutionId`: the ID of the linked solution,
  * `runTemplateFilter`: the list of solution's run template IDs to filter,
  * `defaultRunTemplateDataset`: a map of RunTemplate/DatasetID to set a default dataset for a run template.

- **`webapp`**: the web application has three parts:
  * `url`: the URL of the web application,
  * `iframes`: a map of iframeKey/iframeURL,
  * `options`: free-form options for the web application.

- **`sendInputToDataWarehouse`**: whether or not the dataset values and the input parameter values are sent to the DataWarehouse prior to the ScenarioRun.
- **`useDedicatedEventHubNamespace`**: whether or not a dedicated Azure Event Hub Namespace is used. The name must be `<organization_id>-<workspace_id>` (in lowercase). The Namespace must contain two Event Hubs named `probesmeasures` and `scenariorun`.
- **`dedicatedEventHubSasKeyName`**: the dedicated Event Hub SAS key name, defaults to `RootManageSharedAccessKey`.
- **`dedicatedEventHubAuthenticationStrategy`**: the Event Hub authentication strategy: `SHARED_ACCESS_POLICY` or `TENANT_CLIENT_CREDENTIALS`.
- **`sendScenarioRunToEventHub`**: whether or not the ScenarioRun is sent to the Event Hub.
- **`sendScenarioMetadataToEventHub`**: whether or not the scenario metadata are sent to Azure Event Hub Namespace for this workspace. The Event Hub Namespace must be named `<organization_id>-<workspace_id>` (in lowercase). This Namespace must also contain two Event Hubs named `scenariometadata` and `scenariorunmetadata`.
- **`datasetCopy`**: activate the copy of dataset on scenario creation, meaning that each scenario created in this workspace will create this copy.
- **`security`**: Constituted of a default security role for non-specified users and a list of specified users with their relative security roles. If no security was provided at creation, the default role for the workspace will be `none` and the only user in the access control list (ACL) will be the user who created the workspace with the role `admin`.

## References

For more detailed information about the `workspace.yaml` file, please refer to:

- [Cosmo Tech API 3.X](https://chocolatine.api.cosmotech.com/cosmotech-api/supplychain-demo/v3-2/swagger-ui/index.html)
