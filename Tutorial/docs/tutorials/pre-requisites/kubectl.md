---
tags:
  - Bash
  - Kubernetes
  - Requirement
---
# kubectl

kubectl is the command-line tool for interacting with Kubernetes clusters. It lets you deploy applications, inspect resources, and manage cluster operations.

## Install kubectl on Debian

Add the Kubernetes APT repository, then install kubectl:

```bash title="Add Kubernetes repository"
cd ~/Tooling

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.32/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.32/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

```bash title="Install kubectl"
sudo apt-get update
sudo apt-get install -y kubectl
```

Verify the installation:

```bash title="Check the kubectl version"
kubectl version --client
```

## Add kubectl to ToolingBins

```bash title="Symlink kubectl"
cd ~/ToolingBins
ln -s /usr/bin/kubectl kubectl
```

## Configure kubectl

kubectl uses a `kubeconfig` file (default: `~/.kube/config`) to locate and authenticate to Kubernetes clusters.

To set a specific kubeconfig file for your session:

```bash title="Set KUBECONFIG environment variable"
export KUBECONFIG=~/.kube/my-cluster-config
```

To merge multiple kubeconfig files and switch between contexts:

```bash title="List available contexts"
kubectl config get-contexts
```

```bash title="Switch context"
kubectl config use-context <context-name>
```

## Update kubectl

```bash title="Update kubectl"
sudo apt-get update
sudo apt-get install --only-upgrade kubectl
```

## References

- [kubectl Official Documentation](https://kubernetes.io/docs/reference/kubectl/)
- [Install kubectl on Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)
- [kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
