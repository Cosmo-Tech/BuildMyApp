# Setting up your environment

Before you can begin working with the Build My App hands-on session, you need to set up your development environment with the required tools and dependencies. This guide will walk you through installing all necessary components for both Windows and Debian 13/WSL environments.

## System Requirements

The Build My App tutorial requires specific tools depending on your operating system:

<div class="annotate" markdown>
- **Windows users only**: 
    - WSL (Windows Subsystem for Linux) with Debian 13
    - Docker Desktop
    - Power BI Desktop
- **All users**: 
    - Git
    - Docker
    - CosmoTech SDK
    - Babylon
    - Azure CLI
    - jq
    - *CoAL and Dotenv* (1)
    - any IDE of your choice
</div>
1. Nice to have
## WSL Setup (Windows Only)

If you're working on Windows, you'll need to set up Windows Subsystem for Linux with Debian 13 for the best development experience.

### Installing WSL with Debian 13

First, enable WSL and install Debian 13:

```bash title="Install WSL with Debian 13"
# Open PowerShell as Administrator and run:
wsl --install -d debian

# After installation, restart your computer
# Open Debian from the Start menu and complete the initial setup
```

### Configure your WSL environment

Once Debian is installed, set up your development directories:

```bash title="Set up tooling directories"
cd ~

mkdir Tooling
mkdir ToolingBins

echo 'export PATH=~/ToolingBins:$PATH' >> ~/.bashrc
source ~/.bashrc
```

!!! info "WSL Configuration"
    The tooling directories will help organize your development tools. Most installation commands in this guide assume you're using a bash-based terminal (either native Linux or WSL).

## Git Installation

Git is essential for version control and accessing the project repositories.

### On Debian 13/WSL

```bash title="Install Git on Debian"
sudo apt update
sudo apt install -y git

# Verify installation
git --version

# Configure Git with your information
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

### Create an SSH Key for Git Services

SSH keys allow secure authentication with Git services for pulling and pushing code.

To generate a new SSH key pair:

```bash title="Generate SSH key"
ssh-keygen -t ed25519 -C "your_email@example.com"
```

After running the command:
- You will be prompted for a file location (press Enter to accept the default: `~/.ssh/id_ed25519`).
- You can set a passphrase for additional security (optional).

The public key will be saved as `~/.ssh/id_ed25519.pub`.  
Add this public key to your Git service (GitHub, GitLab, etc.) under SSH keys to enable SSH-based authentication.

Then to allow your system to use that ssh key to connect to your services you need to add it to your `ssh-agent`:

```bash title="Add SSH key to ssh-agent"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

### On Windows (if not using WSL)

Download and install Git from [git-scm.com](https://git-scm.com/download/win) and follow the installation wizard.

!!! warning "Checkpoint"
    Verify Git is properly installed by running `git --version` in your terminal.

## Docker Installation

Docker is required for containerized deployments and building solution images.

### Docker Desktop (Windows Only)

Download and install Docker Desktop from [docker.com](https://www.docker.com/products/docker-desktop/).

After installation:

1. Ensure WSL 2 integration is enabled in Docker Desktop settings
2. Start Docker Desktop and verify it's running

### On Debian 13/WSL

Docker can be installed using Debian's standard packages. For Debian 12 and earlier, install `docker.io`. For Debian 13 and onward, install both `docker.io` and `docker-cli`:

```bash title="Install Docker (Debian standard packages)"
sudo apt update

# For Debian 12 and earlier:
# sudo apt install docker.io

# For Debian 13 and onward:
sudo apt install docker.io docker-cli -y

# Add your user to the docker group
sudo usermod -aG docker $USER
```

After installation, log out and back in for group changes to take effect.

!!! warning "Checkpoint"
    Verify Docker is working by running `docker --version` and `docker run hello-world`.

## CosmoTech SDK Installation

The CosmoTech SDK is the main tool for building simulators and includes the `csm` CLI.

### Installation

As this is the main tool of the Cosmo Tech IP, the installation guide won't be made public here, you can follow the references for the full installation information.

But the main elements are:

- Install the dependencies
- Download the installer
- Run the installer

### Update

The installer does not allow for inplace updates, to make version change of your installation you will have to uninstall the current version and follow the installation process anew for the version you want.

!!! info "SDK Capabilities"
    The CosmoTech SDK provides the `csm` CLI for building simulators, managing projects, and running simulations locally.

!!! warning "Installation References"
    Installation references are locked behind an account having access to the Cosmo Tech documentation Portal:
    
    - [SDK User Guide](https://portal.cosmotech.com/docs//documentation/Studio_Guide_Portal/11.3/Content/WhatsNew.htm)
    - [Official installation guide](https://portal.cosmotech.com/docs//documentation/Studio_Guide_Portal/11.3/Content/SDK_OK/Studio%20Installation%20Guide.htm)

## Azure CLI Installation

Azure CLI is required for managing Azure resources and container registries.

### On Debian 13/WSL

Install using the official Microsoft repository:

```bash title="Install Azure CLI"
cd ~/Tooling

# Install dependencies
sudo apt update
sudo apt install -y ca-certificates curl apt-transport-https lsb-release gnupg

# Add Microsoft signing key
curl -sL https://packages.microsoft.com/keys/microsoft.asc | \
  gpg --dearmor | \
  sudo tee /etc/apt/keyrings/microsoft.gpg > /dev/null

# Add Azure CLI repository
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/microsoft.gpg] https://packages.microsoft.com/repos/azure-cli/ $(lsb_release -cs) main" | \
  sudo tee /etc/apt/sources.list.d/azure-cli.list

sudo apt update
sudo apt install -y azure-cli

# Verify installation
az --version
```

## Babylon CLI Installation

Babylon is the deployment tool for managing CosmoTech platform resources.

```bash title="Portable babylon install"
cd ~/Tooling

git clone git@github.com:Cosmo-Tech/Babylon.git Babylon

cd Babylon
git checkout 4.2.3

python3 -m venv .venv

source .venv/bin/activate
pip install .
deactivate

cd ~/ToolingBins
ln -s ~/Tooling/Babylon/.venv/bin/babylon babylon

# Verify installation
babylon --version
```

!!! info "Babylon Usage"
    Babylon will be used to deploy solutions, workspaces, and manage platform resources through YAML configuration files.

## Power BI Desktop (Windows Only)

Power BI Desktop is required for creating and editing dashboards that will be embedded in the web application.

### Installing Power BI Desktop

1. Download Power BI Desktop from the [Microsoft Store](https://aka.ms/pbidesktopstore) or [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=58494)
2. Install following the setup wizard
3. Sign in with your Microsoft account that has Power BI Pro licensing

!!! warning "Power BI Requirements"
    - Power BI Desktop requires Windows (cannot be installed in WSL)
    - You need a Power BI Pro license for the hands-on session
    - Ensure you have access to the Azure tenant used for training

## Additional Tools

### jq

jq is a lightweight and flexible command-line JSON processor. It's essential for parsing, filtering, and manipulating JSON data in shell scripts and command-line workflows.

```bash title="install jq"
sudo apt-get update
sudo apt-get install -y jq
```

### CoAL (CosmoTech Acceleration Library)

```bash title="Portable CoAL install"
cd ~/Tooling

git clone https://github.com/Cosmo-Tech/CosmoTech-Acceleration-Library.git CoAL

cd CoAL
python -m venv .venv

source .venv/bin/activate
pip install .
deactivate

cd ~/ToolingBins
ln -s ~/Tooling/CoAL/.venv/bin/csm-data csm-data
ln -s ~/Tooling/CoAL/.venv/bin/csm-orc csm-orc

# Verify installation
csm-data --version
csm-orc --version
```

### Dotenv CLI

Dotenv CLI is a command-line tool for loading environment variables from `.env` files. It's essential for managing configuration and environment variables in development and deployment workflows.

```bash title="Portable dotenv-cli install"
cd ~/Tooling

# Create a dedicated directory for dotenv-cli
mkdir dotenv-cli
cd dotenv-cli

# Create virtual environment
python3 -m venv .venv

# Activate virtual environment and install dotenv-cli
source .venv/bin/activate
pip install dotenv-cli
deactivate

cd ~/ToolingBins
ln -s ~/Tooling/dotenv-cli/.venv/bin/dotenv dotenv

# Verify installation
dotenv --version
```

## Environment Variables Setup

During the hands-on session, you'll receive the following environment variables from your instructor:

```bash title="Required environment variables"
# Babylon connection
export BABYLON_ORG_NAME=<provided_value>
export BABYLON_TOKEN=<provided_value>
export BABYLON_SERVICE=<provided_value>

# Azure authentication
export AZURE_TENANT_ID=<provided_value>
export AZURE_CLIENT_ID=<provided_value>
export AZURE_CLIENT_SECRET=<provided_value>

# CosmoTech API
export CSM_API_URL=<provided_value>
export CSM_API_SCOPE=<provided_value>
```

!!! warning "Environment Variables"
    These variables will be provided by your instructor at the beginning of the hands-on session. 
    Do not proceed without them.

## Verification Checklist

Before proceeding to the next step, verify all tools are properly installed:

- [ ] WSL with Debian 13 (Windows users only)
- [ ] Git configured with your credentials
- [ ] Docker running and accessible
- [ ] Docker Desktop running (Windows users only)
- [ ] CosmoTech SDK (`csm --version`)
- [ ] Azure CLI (`az --version`)
- [ ] Babylon CLI (`babylon --version`)
- [ ] Power BI Desktop installed (Windows users only)
- [ ] CoAL installed (`csm-data --version`, `csm-orc --version`)
- [ ] Dotenv CLI installed (`dotenv --version`)

## Troubleshooting

### Common Issues

**WSL Installation Problems**: Ensure virtualization is enabled in BIOS and Windows features for WSL are enabled.

**Docker Permission Issues**: Make sure your user is added to the docker group and you've logged out/in after the change.

**Azure CLI Authentication**: Run `az login` to authenticate before using Azure resources.

!!! info "Getting Help"
    If you encounter issues during setup, document the error messages and ask for assistance during the hands-on session.

This completes the environment setup.

Now we need to check that all permission and access needed for the formation are ready for you

## Permission check

Before going further you can check with your instructor that all permissions needed for the formation have been set up.

- [ ] `OrganizationUser` into the Cosmo Tech API
- [ ] Member of `BuildMyApp` team in Github
- [ ] Have a licence for the Power BI service
- [ ] Be part of a BuildMyApp channel in Slack (created for your training session)

Once all tools are installed and verified, and you have all the required access, 
you'll be ready to proceed with deploying your first pre-packaged solution in the next section.
