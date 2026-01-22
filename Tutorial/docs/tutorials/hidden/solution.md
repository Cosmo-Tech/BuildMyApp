# Solution

The `solution.yaml` file is the core of a Cosmo Tech solution, as it declares the elements to deploy a solution and manages it through the Cosmo Tech API. It describes:
- Where the simulator is found,
- Which parameters are accessible for modification by the user,
- The run templates for all the run types associated with the solution.

## Solution elements

Several elements and parameters are to be declared in the `solution.yaml` file, in order to configure the solution properly:

```yaml
kind: Solution
namespace: 
  remote: {{remote}}
  state_id: "{{state_id}}"
  context: "{{context_id}}"
  platform: {{platform}}
metadata:
  selector:
    organization_id: {{organization_id}}
spec:
  sidecars:
  payload:
    key: {{solution_key}}
    name: {{solution_name}}
    description: {{solution_description}}
    repository: {{solution_repository}}
    alwaysPull: boolean
    csmSimulator: string
    version: {{solution_version}}
    sdkVersion: string
    url: string
    tags: {}
    parameters: {}
    parameterGroups: {}
    runTemplates: {}
    security: {{security}}
```

!!! note

    The elements in the `namespace` and `metadata` categories are configured in previous Babylon uses and are configured as such.

### Sidecars

`sidecars` enable the configuration of external services for the solution, such as the Azure run templates, etc.

### User-provided elements

- In `namespace`, all the elements were inputed previously and can be referenced by using the variables in the example.
- In `metadata`, only the `organizationId` previously used must be referenced.
- In `spec`, the Azure run templates that the workspace will use can be inputed.

The following elements require user input:

- **`key`**: the name the solution will have in subprocesses such as Azure use.
- **`name`**: the name the solution will have displayed in the web application.
- **`description`**: a description of the solution.
- **`repository`**: the container registry where the solution image is stored.
- **`alwaysPull`**: whether or not the run template should always pull the image.
- **`csmSimulator`**: the main Cosmo Tech simulator name used in standard run templates.
- **`version`**: the solution version in `MAJOR.MINOR.PATCH` format; it must be aligned with an existing repository tag.
- **`sdkVersion`**: the SDK version in `MAJOR.MINOR` format used to build this solution.
- **`url`**: an optional URL link to the solution page.
- **`tags`**: the solution tags.
- **`parameters`**: the parameters defined for scenario creation.
- **`parameterGroups`**: the grouped parameters used in the run templates.
- **`runTemplates`**: the run templates for the scenario.
- **`security`**: constituted of a default security role for non-specified users and a list of specified users with their relative security roles. If no security was provided at creation, the default role for the solution will be `none` and the only user in the access control list (ACL) will be the user who created the solution with the role `admin`.
