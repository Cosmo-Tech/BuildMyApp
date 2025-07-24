# Azure CLI

Azure CLI is required for managing Azure resources from the command line.

## Install Azure CLI on Debian

Install using the official Microsoft repository:

```bash title="Install Azure CLI"
cd ~/Tooling

# Install dependencies
sudo apt-get update
sudo apt-get install -y ca-certificates curl apt-transport-https lsb-release gnupg

# Add Microsoft signing key
curl -sL https://packages.microsoft.com/keys/microsoft.asc | \
  gpg --dearmor | \
  sudo tee /etc/apt/keyrings/microsoft.gpg > /dev/null

# Add Azure CLI repository
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/microsoft.gpg] https://packages.microsoft.com/repos/azure-cli/ $(lsb_release -cs) main" | \
  sudo tee /etc/apt/sources.list.d/azure-cli.list

sudo apt-get update
sudo apt-get install -y azure-cli
```

## Add Azure CLI to ToolingBins

Azure CLI is installed system-wide, but you can add a symlink for convenience:

```bash title="Symlink az"
cd ~/ToolingBins
ln -s /usr/bin/az az
```

## Update Azure CLI

To update Azure CLI, run:

```bash title="Update Azure CLI"
sudo apt-get update
sudo apt-get install -y azure-cli
```
