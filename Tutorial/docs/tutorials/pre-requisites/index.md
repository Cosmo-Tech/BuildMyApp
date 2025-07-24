# Pre-requisites

First as some tools are required for some elements I recommend you do separate install of those.

## System preparation

To get ready to make your tooling installation folders you can do the following commands.

You can also make your own system, but most tutoriels here will suppose you have made the following and that you are using a bash based terminal.

How to update your tooling will be added in the installation tutorial of the tools.

```bash title="Set-up tooling folder"
cd ~

mkdir Tooling
mkdir ToolingBins

echo "export PATH=~/ToolingBins:$PATH" >> ~/.bashrc
```

Now every tool that you install can be installed in `Tooling`, and you can add any executable or script you may need over multiple environment in `ToolingBins`

## Babylon

Babylon is the tool made by our DevOps team to do most of the deployment operations on APIs, 
you will need it to update high level configuration of you application.  
You can find the full installation tutoriel on this page : [Babylon](./babylon.md)

## CoAL

You can find installation instructions on this page: [CoAL](./coAL.md)

## csm-orc

You can find installation instructions on this page: [csm-orc](./csm-orc.md)

## csmcli

You can find installation instructions on this page: [csmcli](./csmcli.md)

## Cosmotech SDK

You can find installation instructions on this page: [Cosmotech SDK](./cosmotech-sdk.md)

## docker

Docker is required for containerized deployments and development environments.
A working solution requires a docker image to be built and deployed, you will need this tool for that.
You can find the installation tutorial on this page: [Docker](./docker.md)

## az cli

Azure CLI is required for managing Azure resources from the command line.
You will be using it mostly to connect to azure image repository to send your solutions there.
You can find the installation tutorial on this page: [Azure CLI](./az-cli.md)

## npm / node

npm and Node.js are required for running JavaScript tools and building web applications,
you will need those if you have to do customization of the webapps.

You can find the installation tutorial on this page: [npm / Node.js](./npm-node.md)

## ...
