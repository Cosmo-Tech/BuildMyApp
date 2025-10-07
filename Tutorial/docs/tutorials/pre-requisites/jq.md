---
tags:
  - JSON
  - Command Line
  - Data Processing
  - Requirement
---
# jq

jq is a lightweight and flexible command-line JSON processor. It's essential for parsing, filtering, and manipulating JSON data in shell scripts and command-line workflows.

## Install jq on Debian

Install using the package manager:

```bash title="Install jq"
sudo apt-get update
sudo apt-get install -y jq
```

## Update jq

To update jq, run:

```bash title="Update jq"
sudo apt-get update
sudo apt-get install --only-upgrade jq
```

## Basic Usage

Once installed, you can use jq to process JSON data:

```bash title="Basic jq usage"
# Pretty-print JSON
echo '{"name":"John","age":30}' | jq .

# Extract specific field
echo '{"name":"John","age":30}' | jq '.name'

# Filter arrays
echo '[{"name":"John","age":30},{"name":"Jane","age":25}]' | jq '.[].name'

# Process JSON from file
jq '.field' input.json
```

## References

- [jq Official Documentation](https://jqlang.github.io/jq/)
- [jq Manual](https://jqlang.github.io/jq/manual/)
- [jq Tutorial](https://jqlang.github.io/jq/tutorial/)
- [jq GitHub Repository](https://github.com/jqlang/jq)
