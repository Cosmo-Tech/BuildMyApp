# Solution

Manage solutions through the CosmoTech API.

## Solution Elements

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

## Namespace

The elements in this category have configured on previous Babylon uses and are configuered as such

## Metadata

The elements in this category have configured on previous Babylon uses and are configuered as such

## Spec
## Sidecars

In this part, you will be able to enter the different side elements for Solution such as the Azure run templates.

## User-Provided Elements

In Namespace, all the elements were inputed earlier and can be referenced by using the variables in the example

In Metadata, we only need to reference the organizationId used earlier.

In Spec, we can input the azure run templates the workspace will use.

The following elements require user input:

- **`key`** - The name the solution will have in subprocesses such as Azure use
- **`name`** - The name the solution will have displayed in the WebApp
- **`description`** - A description of the solution
- **`repository`** - The container registry where the solution image is stored
- **`alwaysPull`** - Whether or not the run template should always pull the image
- **`csmSimulator`** - The main Cosmo Tech simulator name used in standard Run Templates
- **`version`** - The solution version in `MAJOR.MINOR.PATCH` format; it must be aligned with an existing repository tag
- **`sdkVersion`** - The SDK version in `MAJOR.MINOR` format used to build this solution
- **`url`** - An optional URL link to the solution page
- **`tags`** - The solution tags
- **`parameters`** - The parameters defined on scenario creation
- **`parameterGroups`** - The grouped parameters used in the run templates
- **`runTemplates`** - The run templates for the scenario
- **`security`** - Constituted of a default security role for non-specified users and a list of specified users with their relative security roles. If no security was provided at creation, the default role for the solution will be `none` and the only user in the access control list will be the user who created the solution with the role `admin` 

