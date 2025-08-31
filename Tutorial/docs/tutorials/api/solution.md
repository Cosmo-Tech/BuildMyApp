# Solution

Manage solutions through the CosmoTech API.

!!! info "Tutorial Duration: 15-20 minutes"

This tutorial covers solution creation, retrieval, updating, and deletion, as well as best practices for managing solutions in your deployment.

---

## Learning Objectives

By the end of this tutorial, you will understand:

- How to create, retrieve, update, and delete solutions via API  
- How to structure solution YAML files  
- How to test solution endpoints using curl and Python  
- Best practices for solution naming and organization  

---

## Understanding Solutions

Solutions are the core deployments in BuildMyApp. They combine datasets, workflows, and configurations to perform simulations or analyses.

Each solution includes:

- **Name**: Unique identifier for the solution  
- **Description**: Short description of what the solution does  
- **Workspace**: Workspace to which the solution belongs  
- **Configuration**: Optional metadata or simulation parameters  

---

## Solution YAML Example

```yaml title="solution.yaml"
solution:
  create:
    method: POST
    url: /solutions
    headers:
      Content-Type: application/json
    body:
      name: MySolution
      description: "Example solution for deployment"
      workspaceId: 123
  list:
    method: GET
    url: /solutions
  update:
    method: PUT
    url: /solutions/{id}
    headers:
      Content-Type: application/json
    body:
      name: UpdatedSolution
      description: "Updated solution description"
  delete:
    method: DELETE
    url: /solutions/{id}
```
### Create Solution

Using curl
```bash
curl -X POST "https://api.cosmotech.com/solutions" \
     -H "Content-Type: application/json" \
     -d '{"name":"MySolution","description":"Example solution for deployment","workspaceId":123}'
```
Using python
```py
import requests

response = requests.post(
    "https://api.cosmotech.com/solutions",
    json={"name": "MySolution", "description": "Example solution for deployment", "workspaceId": 123}
)
print(response.json())
```

### List Solutions

Using curl
```bash
curl -X GET "https://api.cosmotech.com/solutions"
```
Using python
```py
response = requests.get("https://api.cosmotech.com/solutions")
print(response.json())
```

### Update Solution

Using curl
```bash
curl -X PUT "https://api.cosmotech.com/solutions/456" \
     -H "Content-Type: application/json" \
     -d '{"name":"UpdatedSolution","description":"Updated solution description"}'
```
Using python
```py
response = requests.put(
    "https://api.cosmotech.com/solutions/456",
    json={"name": "UpdatedSolution", "description": "Updated solution description"}
)
print(response.json())
```
### Delete Soultions

Using curl
```bash
curl -X DELETE "https://api.cosmotech.com/solutions/456"
```
Using python
```py
response = requests.delete("https://api.cosmotech.com/solutions/456")
print(response.status_code)  # 204 means deleted successfully
```

## Best Practices
Use descriptive names for solutions (e.g., CustomerAnalysis2025)

Keep YAML files version-controlled for reproducibility

Validate YAML files with yamllint before deploying

Organize solutions within workspaces for clarity

Test solutions with small datasets before running full simulations