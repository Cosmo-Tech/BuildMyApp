---
tags:
  - Bash
  - Infrastructure
  - Requirement
---
# Terraform

Terraform is an open-source Infrastructure as Code (IaC) tool by HashiCorp that lets you define and provision infrastructure using declarative configuration files.

## Install Terraform on Debian

Add the HashiCorp APT repository, then install Terraform:

```bash title="Add HashiCorp repository"
cd ~/Tooling

wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
```

```bash title="Install Terraform"
sudo apt-get update
sudo apt-get install -y terraform
```

Verify the installation:

```bash title="Check Terraform version"
terraform version
```

## Add Terraform to ToolingBins

```bash title="Symlink terraform"
cd ~/ToolingBins
ln -s /usr/bin/terraform terraform
```

## Update Terraform

```bash title="Update Terraform"
sudo apt-get update
sudo apt-get install --only-upgrade terraform
```

## References

- [Terraform Official Documentation](https://developer.hashicorp.com/terraform/docs)
- [Install Terraform](https://developer.hashicorp.com/terraform/install)
- [HashiCorp APT Repository](https://www.hashicorp.com/official-packaging-guide)
