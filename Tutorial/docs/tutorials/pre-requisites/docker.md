# Docker

Docker is required for containerized deployments and running many development environments.

## Install Docker on Debian

The recommended way is to use the official Docker repository and install via apt:

```bash title="Install Docker"
cd ~/Tooling

# Remove old versions if any
sudo apt-get remove docker docker-engine docker.io containerd runc

# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

# Install Docker Engine
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

## Add Docker to ToolingBins

Docker is installed system-wide, so no symlink is needed. You can add a symlink for convenience:

```bash title="Symlink docker"
cd ~/ToolingBins
ln -s /usr/bin/docker docker
```

## Update Docker

To update Docker, run:

```bash title="Update Docker"
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
