# npm / Node.js

npm and Node.js are required for running JavaScript tools and building many web applications.

## Install npm and Node.js using nvm

The recommended way is to use [nvm](https://github.com/nvm-sh/nvm):

```bash title="Install nvm"
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
# Restart your terminal or run:
export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"

# Install Node.js (replace 20 with desired version)
nvm install 20
nvm use 20
```

## Add Node.js and npm to ToolingBins

Node.js and npm installed via nvm are available in your shell.  
If you need symlinks for convenience:

```bash title="Symlink node and npm"
cd ~/ToolingBins
ln -s "$(which node)" node
ln -s "$(which npm)" npm
```

## Update or switch Node.js and npm versions

To update or switch Node.js and npm, use nvm:

```bash title="Update or switch Node.js version"
nvm install <version>   # Install a new version
nvm use <version>       # Switch to a version
nvm ls                  # List installed versions
```

## Update nvm

To update nvm to the latest version, run:

```bash title="Update nvm"
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
# Restart your terminal or reload nvm:
export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
```

## References

- [nvm Official Documentation](https://github.com/nvm-sh/nvm)
