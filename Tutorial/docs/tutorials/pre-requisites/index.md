---
hide:
  - path
  - toc
---

# Pre-requisites

First as some tools are required for some elements I recommend you do separate install of those.

## System preparation

To get ready to make your tooling installation folders you can do the following commands.

You can also make your own system, but most tutoriels here will suppose you have made the following and that you are
using a bash based terminal. (Unix or Windows with WSL2)

How to update your tooling will be added in the installation tutorial of the tools.

```bash title="Set-up tooling folder"
cd ~

mkdir Tooling
mkdir ToolingBins

echo "export PATH=~/ToolingBins:$PATH" >> ~/.bashrc
```

Now every tool that you install can be installed in `Tooling`, and you can add any executable or script you may need
over multiple environment in `ToolingBins`

<main class="grid" markdown>

<article markdown>
<div class="text" markdown>
:material-keyboard-return: __docker__

---
Docker is required for containerized deployments and development environments.

A working solution requires a docker image to be built and deployed, you will need this tool for that.
<footer markdown>
[:octicons-arrow-right-24: docker](./docker.md)
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
[:octicons-arrow-right-24: Cosmotech SDK](./cosmotech-sdk.md)
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
[:octicons-arrow-right-24: Azure CLI](./az-cli.md)
</footer>
</div>
</article>

<article markdown>
<div class="text" markdown>
:material-keyboard-return: __nvm / npm / node__

---
npm and Node.js are required for running JavaScript tools and building web applications.

You will need those if you have to do customization of the webapps.
<footer markdown>
[:octicons-arrow-right-24: nvm / npm / Node.js](./npm-node.md)
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
[:octicons-arrow-right-24: Babylon](./babylon.md)
</footer>
</div>
</article>

<article markdown>
<div class="text" markdown>
:material-keyboard-return: __CoAL__

---
CoAL (CosmoTech Acceleration Library) provides a set of tools and utilities to accelerate CosmoTech solution development.

Installing CoAL gives access to two CLIs: `csm-data` for data management and `csm-orc` for orchestration workflows.
<footer markdown>
[:octicons-arrow-right-24: CoAL](./coal.md)
</footer>
</div>
</article>

<article markdown>
<div class="text" markdown>
:material-keyboard-return: __Power BI__

---
Power BI is the tool used to create dashboards embedded in the Cosmo Tech Webapp.

Its installation requires you to have access to a Windows environment, either natively (if you are on Windows and used WSL for the rest of the installation) or using a Virtual Machine.
<footer markdown>
[:octicons-arrow-right-24: Microsoft Power BI](./power-bi.md)
</footer>
</div>
</article>

</main>
