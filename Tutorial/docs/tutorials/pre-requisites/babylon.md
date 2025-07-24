# Babylon

You will need a working installation of `babylon` further in the tutorials

One easy way to make a portable instalation of it is to execute the following script

```bash title="Portable babylon install"
cd ~/Tooling

git clone git@github.com:Cosmo-Tech/Babylon.git Babylon

cd Babylon
git checkout 4.2.3

python -m venv .venv

source .venv/bin/activate
pip install .
deactivate

cd ~/ToolingBin
ln -s ~/Tooling/Babylon/.venv/bin/babylon babylon
```

And know your `babylon` installation is ready and will be made available over multiple environments while not interacting with any other dependency you may have


```bash title="Update Babylon"
cd ~/Tooling/Babylon
git fetch -ftpa
git checkout <New_Version>
source .venv/bin/activate
pip install .
deactivate
```