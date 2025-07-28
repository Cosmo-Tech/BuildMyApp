---
tags:
  - Solution
  - Bash
  - Requirement
---
# CoAL

You will need a working installation of CoAL (CosmoTech Acceleration Library) further in the tutorials.

One easy way to make a portable installation is to execute the following script:

```bash title="Portable CoAL install"
cd ~/Tooling

git clone https://github.com/Cosmo-Tech/CosmoTech-Acceleration-Library.git CoAL

cd CoAL
python -m venv .venv

source .venv/bin/activate
pip install .
deactivate

cd ~/ToolingBins
ln -s ~/Tooling/CoAL/.venv/bin/csm-data csm-data
ln -s ~/Tooling/CoAL/.venv/bin/csm-orc csm-orc
```

This will make both `csm-data` and `csm-orc` CLIs available in your environment, isolated from other dependencies.

To update CoAL, use:

```bash title="Update CoAL"
cd ~/Tooling/CoAL
git fetch --all
git checkout <New_Version>
source .venv/bin/activate
pip install .
deactivate
```

## Dependencies

CoAL depends on the csm-orc project for orchestration features. Make sure to install csm-orc as described in its documentation.

## References

For more detailed information, refer to:

* [CoAL Documentation](https://cosmo-tech.github.io/CosmoTech-Acceleration-Library/)
* [csm-orc Documentation](https://cosmo-tech.github.io/run-orchestrator/)
* [CoAL GitHub Repository](https://github.com/Cosmo-Tech/CosmoTech-Acceleration-Library)
* [csm-orc GitHub Repository](https://github.com/Cosmo-Tech/run-orchestrator)
