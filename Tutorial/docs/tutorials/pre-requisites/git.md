---
tags:
  - Bash
  - Versionning
  - Requirement
---
# Git

Git is a distributed version control system widely used for source code management.

## Install Git on Debian

```bash title="Install Git"
sudo apt-get update
sudo apt-get install -y git
```

## Add Git to ToolingBins

```bash title="Symlink git"
cd ~/ToolingBins
ln -s /usr/bin/git git
```

## Create an SSH Key for Git Services

SSH keys allow secure authentication with Git services for pulling and pushing code.

To generate a new SSH key pair:

```bash title="Generate SSH key"
ssh-keygen -t ed25519 -C "your_email@example.com"
```

**Explanation of parameters:**

- `-t ed25519`: Specifies the type of key to create. `ed25519` is a modern, secure algorithm recommended for most users. You can use `rsa` for compatibility, but `ed25519` is preferred.
- `-C "your_email@example.com"`: Adds a comment to the key, typically your email address, to help identify the key later.

After running the command:

- You will be prompted for a file location (press Enter to accept the default: `~/.ssh/id_ed25519`).
- You can set a passphrase for additional security (optional).

The public key will be saved as `~/.ssh/id_ed25519.pub`.  
Add this public key to your Git service (GitHub, GitLab, etc.) under SSH keys to enable SSH-based authentication.

Then to allow your system to use that ssh key to connect to your services you need to add it to your `ssh-agent`

```bash title="Add SSH key to ssh-agent"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

## Update Git

```bash title="Update Git"
sudo apt-get update
sudo apt-get install --only-upgrade git
```

## References

- [Git Official Documentation](https://git-scm.com/doc)
- [GitHub SSH Key Setup](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)
- [GitLab SSH Key Setup](https://docs.gitlab.com/ee/ssh/)
