# Docker

Docker is a containerization platform that packages your simulation code and dependencies into portable, self-contained units called containers.

## Purpose in CosmoTech Platform

Docker enables **consistent and isolated execution** of simulations across different environments by:

- Packaging your solution code with all dependencies
- Ensuring simulations run identically everywhere
- Isolating each scenario run in its own container
- Managing different solution versions through image tags

## Key Benefits

- **Reproducibility** - Same container image always produces the same results
- **Portability** - Run anywhere that supports Docker (local, cloud, Kubernetes)
- **Dependency Management** - All libraries and tools packaged together
- **Version Control** - Different versions coexist using different tags
- **Scalability** - Easily replicate containers for parallel execution
- **Isolation** - Each simulation runs independently without conflicts

## How It Works

1. **Build** - Create a Docker image containing your solution code
2. **Tag** - Version your image (e.g., `my-solution:1.0.0`)
3. **Push** - Upload to Azure Container Registry
4. **Register** - Reference the image in your CosmoTech solution
5. **Execute** - Platform pulls and runs the container for each scenario

## Example Dockerfile

```dockerfile
FROM python:3.11-slim

WORKDIR /app

# Install dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy solution code
COPY src/ ./src/

# Run simulation
ENTRYPOINT ["python", "src/main.py"]
```

## Image Registry

Solutions reference Docker images stored in Azure Container Registry:

```yaml
spec:
  payload:
    repository: myregistry.azurecr.io/my-solution
    version: 1.0.0
    alwaysPull: false
```

## Best Practices

- **Use semantic versioning** - Tag images as `MAJOR.MINOR.PATCH`
- **Keep images small** - Use minimal base images (e.g., `-slim`, `-alpine`)
- **Don't hardcode secrets** - Use environment variables
- **Test locally first** - Run containers locally before deploying
- **Version alignment** - Solution version must match image tag

## Container Execution

When a scenario runs:
1. Kubernetes pulls the Docker image from the registry
2. Container starts with scenario parameters as environment variables
3. Your simulation code executes inside the container
4. Results are sent to Event Hub and stored in ADX
5. Container is cleaned up after completion

Docker ensures your simulation runs consistently and reliably, regardless of where it's executed.