# Workspace

Workspaces and their management via the CosmoTech API.

!!! info "Tutorial Duration: 15-20 minutes"

This tutorial covers workspace creation, retrieval, updating, and deletion, and best practices for managing workspaces in your deployment.

---

## Learning Objectives

By the end of this tutorial, you will understand:

- How to create, retrieve, update, and delete workspaces via API  
- How to structure workspace YAML files  
- How to test workspace endpoints using curl and Python  
- Best practices for workspace naming and organization  

---

## Understanding Workspaces

Workspaces are containers for your solutions, datasets, and simulations. They help organize your deployment and control access to resources.

Each workspace includes:

- **Name**: Unique identifier for the workspace  
- **Description**: Short description of the workspace  
- **Permissions**: Access control for users and roles  

---

## Workspace YAML Example

```yaml title="workspace.yaml"
workspace:
  create:
    method: POST
    url: /workspaces
    headers:
      Content-Type: application/json
    body:
      name: MyWorkspace
      description: "Example workspace for deployment"
  list:
    method: GET
    url: /workspaces
  update:
    method: PUT
    url: /workspaces/{id}
    headers:
      Content-Type: application/json
    body:
      name: UpdatedWorkspace
      description: "Updated workspace description"
  delete:
    method: DELETE
    url: /workspaces/{id}
```

### Create Workspace

Using Curl
```bash
curl -X POST "https://api.cosmotech.com/workspaces" \
     -H "Content-Type: application/json" \
     -d '{"name":"MyWorkspace","description":"Example workspace for deployment"}'
```
Using python
```py
import requests

response = requests.post(
    "https://api.cosmotech.com/workspaces",
    json={"name": "MyWorkspace", "description": "Example workspace for deployment"}
)
print(response.json())
```
### List Workspaces

Using curl
```bash
curl -X GET "https://api.cosmotech.com/workspaces"
```
Using python
```py
response = requests.get("https://api.cosmotech.com/workspaces")
print(response.json())
```
### Update Workspace
Using curl
```bash
curl -X PUT "https://api.cosmotech.com/workspaces/123" \
     -H "Content-Type: application/json" \
     -d '{"name":"UpdatedWorkspace","description":"Updated workspace description"}'
```
Using python
```py
response = requests.put(
    "https://api.cosmotech.com/workspaces/123",
    json={"name": "UpdatedWorkspace", "description": "Updated workspace description"}
)
print(response.json())
```
### Delete Workspace
Using curl
```bash
curl -X DELETE "https://api.cosmotech.com/workspaces/123"
```
Using python
```py
response = requests.delete("https://api.cosmotech.com/workspaces/123")
print(response.status_code)  # 204 means deleted successfully
```
## Best Practices
Use descriptive names for workspaces (e.g., Marketing2025, not just Workspace1)

Keep YAML files version-controlled for reproducibility

Validate YAML files with yamllint before deploying

Avoid deleting workspaces unless necessary; prefer archiving or renaming

Organize workspaces by team, project, or environment for clarity