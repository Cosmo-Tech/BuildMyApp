# YAML Files

!!! info "Tutorial Duration: 15-20 minutes"

This tutorial covers how to work with **API YAML files** in BuildMyApp.  
You'll learn how to define endpoints, configure requests, and integrate them in your Solution, Workspace, and Dataset workflows.

## Learning Objectives

By the end of this tutorial, you will understand:

- The purpose of API YAML files in BuildMyApp  
- How to define endpoints, methods, and payloads  
- How to test API calls using `curl` or Python  
- Best practices for organizing YAML files in a project  

---

## What are API YAML Files?

API YAML files define how your app communicates with the backend.  
They contain:

- **Endpoint URL**  
- **HTTP Method** (`GET`, `POST`, `PUT`, `DELETE`)  
- **Headers** (authentication, content type)  
- **Request Body** (for POST/PUT)  
- **Expected Response**  

Think of them as a blueprint for all API calls in your BuildMyApp deployment.  

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
```
Using Curl
```bash
curl -X POST "https://api.cosmotech.com/workspaces" \
     -H "Content-Type: application/json" \
     -d '{"name":"MyWorkspace","description":"Example workspace"}'


curl -X GET "https://api.cosmotech.com/workspaces"
```
Using python
``` py
import requests

# Create workspace
response = requests.post(
    "https://api.cosmotech.com/workspaces",
    json={"name": "MyWorkspace", "description": "Example workspace"}
)
print(response.json())

# List workspaces
response = requests.get("https://api.cosmotech.com/workspaces")
print(response.json())
```


## Dataset YAML Example

``` yaml
dataset:
  upload:
    method: POST
    url: /datasets
    headers:
      Content-Type: application/json
    body:
      name: SampleDataset
      description: "Demo dataset for testing"
  get:
    method: GET
    url: /datasets/{id}
```
Using Curl
```bash
curl -X POST "https://api.cosmotech.com/datasets" \
     -H "Content-Type: application/json" \
     -d '{"name":"SampleDataset","description":"Demo dataset"}'

curl -X GET "https://api.cosmotech.com/datasets/123"
```

Using python
```py
import requests

# Upload dataset
response = requests.post(
    "https://api.cosmotech.com/datasets",
    json={"name": "SampleDataset", "description": "Demo dataset"}
)
print(response.json())

# Get dataset by ID
response = requests.get("https://api.cosmotech.com/datasets/123")
print(response.json())
```

Best Practices

Keep each resource (workspace, dataset, solution) in its own YAML file

Use descriptive keys for endpoints (e.g., uploadDataset, listWorkspaces)

Validate YAML files before committing using tools like yamllint

Add comments to your YAML files for better maintainability

Document usage examples alongside each YAML file to make testing easier