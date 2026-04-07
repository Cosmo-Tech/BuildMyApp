---
tags:
  - Bash
  - Packaging
  - Solution
  - Requirement
---
# Docker

Docker is required for containerized deployments and running many development environments.

## Install Docker on Debian

Docker can be installed using Debian's standard packages.

```bash title="Install Docker (Debian standard packages)"
sudo apt-get update

sudo apt-get install docker.io docker-cli -y

# Add your user to the docker group
sudo usermod -aG docker $USER
```

After installation, log out and back in for group changes to take effect.

Verify the installation:

```bash title="Check the Docker version"
docker version
```

## Add Docker to ToolingBins

Docker is installed system-wide, so no symlink is needed. You can add a symlink for convenience:

```bash title="Symlink docker"
cd ~/ToolingBins
ln -s /usr/bin/docker docker
```

## Update Docker

To update Docker, run:

```bash title="Update Docker (Debian standard packages)"
sudo apt-get update

sudo apt-get install --only-upgrade docker.io docker-cli
```

## References

- [Docker Official Documentation](https://docs.docker.com/)
- [Docker Getting Started Guide](https://docs.docker.com/get-started/)
- [Docker CLI Reference](https://docs.docker.com/engine/reference/commandline/docker/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)
