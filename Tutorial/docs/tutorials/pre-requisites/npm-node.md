# npm / Node.js

npm and Node.js are required for running JavaScript tools and building many web applications.

## Install npm and Node.js on Debian

The recommended way is to use the official NodeSource repository:

```bash title="Install Node.js and npm"
cd ~/Tooling

# Install dependencies
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg

# Add NodeSource GPG key
curl -fsSL https://deb.nodesource.com/gpgkey/nodesource.gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg

# Add Node.js repository (replace 20.x with desired version)
echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_20.x $(lsb_release -cs) main" | \
  sudo tee /etc/apt/sources.list.d/nodesource.list

sudo apt-get update
sudo apt-get install -y nodejs
```

## Add Node.js and npm to ToolingBins

Node.js and npm are installed system-wide, but you can add symlinks for convenience:

```bash title="Symlink node and npm"
cd ~/ToolingBins
ln -s /usr/bin/node node
ln -s /usr/bin/npm npm
```

## Update Node.js and npm

To update Node.js and npm, run:

```bash title="Update Node.js and npm"
sudo apt-get update
sudo apt-get install -y nodejs
```
