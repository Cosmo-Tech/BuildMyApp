---
tags:
  - Babylon
  - Deployment
  - Bash
  - Requirement
---
# Babylon

You will need a working installation of `babylon` further in the tutorials

One easy way to make a portable instalation of it is to execute the following script

```bash title="Portable babylon install"
cd ~/Tooling

git clone git@github.com:Cosmo-Tech/Babylon.git Babylon

cd Babylon
git checkout 4.2.3

python3 -m venv .venv

source .venv/bin/activate
pip install .
deactivate

cd ~/ToolingBins
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

## References

For more detailed information about Babylon, please refer to the official documentation:

* [Babylon Documentation](https://cosmo-tech.github.io/Babylon/) — Developer documentation
* [Babylon End-User Documentation](https://cosmo-tech.github.io/Babylon-End-User-Doc)
* [Babylon GitHub Repository](https://github.com/Cosmo-Tech/Babylon)
