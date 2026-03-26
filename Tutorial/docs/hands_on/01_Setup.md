---
title: Setup
---

# Set up

Before following the actual training session, you need to set up your development environment with the required tools and resources.

## System Requirements

This guide will walk you through installing all necessary components for both Windows and Debian 13/WSL environments.

The Build-My-App Hands-On training requires specific tools depending on your operating system:

<div class="annotate" markdown>
- **Windows users only**: 
    - WSL (Windows Subsystem for Linux) with Debian 13
    - Docker Desktop
- **All users**: 
    - Git
    - Docker
    - CosmoTech SDK
    - Babylon
    - Azure CLI
    - Superset
    - any IDE of your choice
</div>



<main class="grid" markdown>

<article markdown>
<div class="text" markdown>
:material-keyboard-return: __Git__

---
Git is a distributed version control system essential for managing source code and collaboration.

You will need Git to clone repositories, manage code versions, and collaborate with other developers throughout the development process.
<footer markdown>
[:octicons-arrow-right-24: Git](./Setup/git.md)
</footer>
</div>
</article>

<article markdown>
<div class="text" markdown>
:material-keyboard-return: __docker__

---
Docker is required for containerized deployments and development environments.

A working solution requires a docker image to be built and deployed, you will need this tool for that.
<footer markdown>
[:octicons-arrow-right-24: docker](./Setup/docker.md)
</footer>
</div>
</article>

<article markdown>
<div class="text" markdown>
:material-keyboard-return: __Babylon__

---
Babylon is the tool made by our DevOps team to do most of the deployment operations on APIs.

You will need it to update high level configuration of you application.
<footer markdown>
[:octicons-arrow-right-24: Babylon](./Setup/babylon.md)
</footer>
</div>
</article>

<article markdown>
<div class="text" markdown>
:material-keyboard-return: __kubectl__

---
kubectl is the command-line tool for interacting with Kubernetes clusters.

You will need it to manage and inspect deployed workloads and resources in your Kubernetes environment.
<footer markdown>
[:octicons-arrow-right-24: kubectl](./Setup/kubectl.md)
</footer>
</div>
</article>

<article markdown>
<div class="text" markdown>
:material-keyboard-return: __Superset__

---
Apache Superset is an open-source data visualization and business intelligence platform.

You will use it to implement dashboards for the visualization of simulation results.
<footer markdown>
[:octicons-arrow-right-24: Superset](./Setup/superset.md)
</footer>
</div>
</article>

<article markdown>
<div class="text" markdown>
:material-keyboard-return: __Azure CLI__

---
Azure CLI is required for managing Azure resources from the command line.

You will be using it mostly to connect to azure image repository to send your solutions there.
<footer markdown>
[:octicons-arrow-right-24: Azure CLI](./Setup/az-cli.md)
</footer>
</div>
</article>

<article markdown>
<div class="text" markdown>
:material-keyboard-return: __Terraform__

---
Terraform is an infrastructure-as-code tool for provisioning and managing cloud resources.

You will use it to deploy and configure the infrastructure required by your solution.
<footer markdown>
[:octicons-arrow-right-24: Terraform](./Setup/terraform.md)
</footer>
</div>
</article>

<article markdown>
<div class="text" markdown>
:material-keyboard-return: __Cosmotech SDK__

---
The Cosmo Tech SDK is the main tool you will use to build your simulator that will be the core of your solution.

It comes embedded with the CLI `csm-cli` that will allow you to quickly interract with your project and accelerate some
operations.
<footer markdown>
[:octicons-arrow-right-24: Cosmotech SDK](./Setup/cosmotech-sdk.md)
</footer>
</div>
</article>

</main>



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

## Verification Checklist

Before proceeding to the next step, verify all tools are properly installed:

- [ ] WSL with Debian 13 (Windows users only)
- [ ] Git configured with your credentials
- [ ] Docker running and accessible
- [ ] Docker Desktop running (Windows users only)
- [ ] CosmoTech SDK (`csm --version`)
- [ ] Azure CLI (`az --version`)
- [ ] Babylon CLI (`babylon --version`)

## Troubleshooting

### Common Issues

**WSL Installation Problems**: Ensure virtualization is enabled in BIOS and Windows features for WSL are enabled.

**Docker Permission Issues**: Make sure your user is added to the docker group and you've logged out/in after the change.

**Azure CLI Authentication**: Run `az login` to authenticate before using Azure resources.

!!! info "Getting Help"
    If you encounter issues during setup, document the error messages and ask for assistance during the Hands-On training.

This completes the environment setup.

Now we need to check that all permission and access needed for the formation are ready for you

## Permission check

Before going further you can check with your instructor that all permissions needed for the formation have been set up.

- [ ] `OrganizationUser` into the Cosmo Tech API
- [ ] Member of `BuildMyApp` team in Github
- [ ] Have access to the Superset service
- [ ] Be part of a Build-My-App channel in Slack (created for your Hands-On training)

Once all tools are installed and verified, and you have all the required access, 
you'll be ready to proceed with deploying your first pre-packaged solution in the next section.

## Ressources

You will be given the following elements by the person in charge of the Hands-On training:

- A new local project deployment directory:
    - This directory will contain an example deployable project compatible with the example Simulator with which you will be provided
    - It contains the following elements :
        - a `variables.yaml` file that will contain most of the data you will need to update before doing your deployments
        - a `project/` folder containing the configuration for your project deployment
            - `solution.yaml` a consolidated solution file compatible with the `babylon` CLI to deploy resources
            - `workspace.yaml` a consolidated workspace file also compatible with `babylon`
- A Simulator repository, [onboarding-brewery-solution](https://github.com/Cosmo-Tech/onboarding-brewery-solution), hosted on GitHub.
    - This repository contains a minimal project that is "Deployment Ready" but has no real configuration
    - You have the full Conceptual Model of the "Brewery" which is the example project used for Modeling tutorials
    - It contains a working Simulator that will simulate the Conceptual Model
    - There are two minimal run templates :
        - Example: a simple run that will copy a fixed dataset and simulate it
        - ETL: an empty run template that just shows logs some environment variables
    - You have a single Simulation set-up : `BusinessApp_Simulation`
        - It takes a list of CSVs as inputs as found in `Simulation/Resource/default_dataset` and simulate it
        - With no specific configuration of the simulation it will output results in `Simulation/Output`
- A set of environment variables allowing Babylon to handle the Solution deployment:
    - `simulator_repository`: the solution simulator source repository
    - `cluster_name` / `cluster_domain`: the name and domain of the Kubernetes cloud cluster used for the database
    - `tenant`: the tenant id used to host the platform
- Access rights ready for the Hands-On training's cloud resources:
    - The Kubernetes cluster (depend de l'ajout dans le groupe Azure BuildMyApp)
    - The Superset service (avec Okta)
    - The tenant (ensemble avec le cluster)
    - an accès "share" l'access à Harbor
