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

## csm-orc

## csmcli

## Cosmotech SDK

## docker

## az cli

## npm / node

## ...