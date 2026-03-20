---
hide:
  - path
  - toc
---

# Pre-requisites

First, assert that you are using an enviromnent under Debian 13. Then, as some tools are required for some elements I recommend you do separate install of those.

## System preparation

To get ready to make your tooling installation folders you can do the following commands.

You can also make your own system, but most tutoriels here will suppose you have made the following and that you are
using a bash based terminal. (Unix or Windows with WSL2)

How to update your tooling will be added in the installation tutorial of the tools.

```bash title="Set-up tooling folder"
cd ~

mkdir Tooling
mkdir ToolingBins

echo 'export PATH=~/ToolingBins:$PATH' >> ~/.bashrc
```

Now every tool that you install can be installed in `Tooling`, and you can add any executable or script you may need
over multiple environment in `ToolingBins`

<main class="grid" markdown>

<article markdown>
<div class="text" markdown>
:material-keyboard-return: __Git__

---
Git is a distributed version control system essential for managing source code and collaboration.

You will need Git to clone repositories, manage code versions, and collaborate with other developers throughout the development process.
<footer markdown>
[:octicons-arrow-right-24: Git](./git.md)
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
[:octicons-arrow-right-24: docker](./docker.md)
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
:material-keyboard-return: __kubectl__

---
kubectl is the command-line tool for interacting with Kubernetes clusters.

You will need it to manage and inspect deployed workloads and resources in your Kubernetes environment.
<footer markdown>
[:octicons-arrow-right-24: kubectl](./kubectl.md)
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
:material-keyboard-return: __Terraform__

---
Terraform is an infrastructure-as-code tool for provisioning and managing cloud resources.

You will use it to deploy and configure the infrastructure required by your solution.
<footer markdown>
[:octicons-arrow-right-24: Terraform](./terraform.md)
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

</main>
