# Docker

Docker is a containerization platform that packages your simulation code and dependencies into portable, self-contained units called containers.

Docker enables **consistent and isolated execution** of simulations across different environments by:

- Packaging your solution code with all dependencies.
- Ensuring simulations run identically everywhere.
- Isolating each scenario run in its own container.
- Managing different solution versions through image tags.

Using Docker offers several key benefits:

- **Reproducibility**: same container image always produces the same results.
- **Portability**: run anywhere that supports Docker (local, cloud, Kubernetes).
- **Dependency Management**: all libraries and tools packaged together.
- **Version Control**: different versions coexist using different tags.
- **Scalability**: easily replicate containers for parallel execution.
- **Isolation**: each simulation runs independently without conflicts.

## How it works

1. **Build**: create a Docker image containing your solution code.
2. **Tag**: version your image (e.g., `my-solution:1.0.0`).
3. **Push**: upload to Azure Container Registry.
4. **Register**: reference the image in your Cosmo Tech solution.
5. **Execute**: platform pulls and runs the container for each scenario.

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

## Image registry

Solutions reference Docker images stored in Azure Container Registry:

```yaml
spec:
  payload:
    repository: myregistry.azurecr.io/my-solution
    version: 1.0.0
    alwaysPull: false
```

## Best practices

- **Use semantic versioning**: tag images as `MAJOR.MINOR.PATCH`.
- **Keep images small**: use minimal base images (e.g., `-slim`, `-alpine`).
- **Don't hardcode secrets**: use environment variables.
- **Test locally first**: run containers locally before deploying.
- **Version alignment**: solution version must match image tag.

## Container execution

When a scenario runs:

1. Kubernetes pulls the Docker image from the registry.
2. Container starts with scenario parameters as environment variables.
3. Your simulation code executes inside the container.
4. Results are sent to Event Hub and stored in ADX.
5. Container is cleaned up after completion.

Docker ensures your simulation runs consistently and reliably, regardless of where it is executed.

## References

For more detailed information on Docker, please refer to:

- [Docker documentation](https://docs.docker.com/)
