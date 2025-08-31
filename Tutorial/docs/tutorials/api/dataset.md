# Dataset Management

Learn how to manage datasets in BuildMyApp using the API and YAML files.

!!! info "Tutorial Duration: 15-25 minutes"

This tutorial covers dataset creation, retrieval, updating, and best practices for managing datasets in your deployment.

---

## Learning Objectives

By the end of this tutorial, you will understand:

- How to upload, retrieve, update, and delete datasets via API  
- How to structure dataset YAML files  
- How to test dataset endpoints using curl and Python  
- Best practices for dataset naming and organization  

---

## Understanding Datasets

Datasets are structured collections of data used in your BuildMyApp solution.  

Each dataset includes:

- **Name**: Unique identifier for the dataset  
- **Description**: Short description of the dataset content  
- **Data files**: CSV, JSON, or other supported formats  
- **Metadata**: Optional fields for additional information  

Datasets allow solutions and simulations to process data efficiently and reproducibly.

---

## Dataset YAML Example

```yaml title="dataset.yaml"
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
  update:
    method: PUT
    url: /datasets/{id}
    headers:
      Content-Type: application/json
    body:
      name: UpdatedDataset
      description: "Updated description"
  delete:
    method: DELETE
    url: /datasets/{id}
 ```

### Upload Dataset

Using curl
```bash
    curl -X POST "https://api.cosmotech.com/datasets" \
     -H "Content-Type: application/json" \
     -d '{"name":"SampleDataset","description":"Demo dataset"}'
```
Using python
```py
import requests

response = requests.post(
    "https://api.cosmotech.com/datasets",
    json={"name": "SampleDataset", "description": "Demo dataset"}
)
print(response.json())

```
### Retrieve dataset

Using curl
```bash
curl -X GET "https://api.cosmotech.com/datasets/123"
```
Using python
```py
response = requests.get("https://api.cosmotech.com/datasets/123")
print(response.json())
```
### Update dataset

Using curl
```bash
curl -X PUT "https://api.cosmotech.com/datasets/123" \
     -H "Content-Type: application/json" \
     -d '{"name":"UpdatedDataset","description":"Updated description"}'
```
Using python
```py
response = requests.put(
    "https://api.cosmotech.com/datasets/123",
    json={"name": "UpdatedDataset", "description": "Updated description"}
)
print(response.json())
```
### Delete dataset

Using curl 
```bash
curl -X DELETE "https://api.cosmotech.com/datasets/123"
```
Using python
```py
response = requests.delete("https://api.cosmotech.com/datasets/123")
print(response.status_code)  # 204 means deleted successfully
```
## Best practices

Use descriptive names for datasets (e.g., CustomerData2025, not just Dataset1)

Keep YAML files version-controlled for reproducibility

Validate your YAML files with yamllint before deploying

Avoid overwriting datasets unnecessarily — prefer creating new versions

Always test with small datasets before uploading large production data
