---
tags:
  - Environment
  - Configuration
  - Bash
  - Requirement
---
# Dotenv

Dotenv CLI is a command-line tool for loading environment variables from `.env` files. It's essential for managing configuration and environment variables in development and deployment workflows.

## Install dotenv-cli

The recommended way is to create a portable installation using a virtual environment:

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
```

## Add dotenv to ToolingBins

Create a symlink to make the `dotenv` command available across environments:

```bash title="Symlink dotenv"
cd ~/ToolingBins
ln -s ~/Tooling/dotenv-cli/.venv/bin/dotenv dotenv
```

Now your `dotenv` installation is ready and will be available across multiple environments without interfering with other dependencies.

## Update dotenv-cli

To update dotenv-cli to the latest version:

```bash title="Update dotenv-cli"
cd ~/Tooling/dotenv-cli
source .venv/bin/activate
pip install --upgrade dotenv-cli
deactivate
```

## Usage

Once installed, you can use dotenv to load environment variables from `.env` files:

```bash title="Basic dotenv usage"
# Load .env file and run a command
dotenv run -- your-command

# Load specific .env file
dotenv -f path/to/your/.env run -- your-command

# List variables from .env file
dotenv list
```

## References

- [dotenv-cli on PyPI](https://pypi.org/project/dotenv-cli/)
- [dotenv-cli GitHub Repository](https://github.com/venthur/dotenv-cli)
- [Environment Variables Best Practices](https://12factor.net/config)
