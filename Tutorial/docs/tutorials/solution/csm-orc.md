# CSM-ORC

Learn about the CosmoTech Orchestrator and its integration in your solution.

## Overview

The CosmoTech Orchestrator (CSM-ORC) is the tool that executes run templates defined in JSON format. It orchestrates the different steps in your solution workflow, from data fetching to simulation execution and result processing.

## What is CSM-ORC?

CSM-ORC allows you to:

- Define workflows through JSON-based run templates
- Execute steps in a controlled order with dependencies
- Use environment variables for configuration
- Create reusable command templates
- Run the same workflow locally and in production

## Basic Run Template Structure

A run template is a JSON file that defines:

- **Steps**: Individual operations to execute
- **Precedents**: Dependencies between steps (which steps must complete before others)
- **Environment Variables**: Configuration values
- **Commands**: What to execute in each step

*[Placeholder: Basic JSON example showing steps with precedents]*

## Key Concepts from the Meetings

### Step Dependencies
- Steps with **precedents** wait for other steps to complete
- Steps with the same precedents run **in parallel**
- No circular dependencies allowed

### Environment Variables
The platform provides standard variables like:

- `COSMOTECH_API_URL`
- `CSM_ORGANIZATION_ID`
- `CSM_WORKSPACE_ID`
- `CSM_RUNNER_ID`
- `CSM_SIMULATION_ID`

### Command Templates
Instead of repeating complex commands, use **command IDs** that reference predefined templates:

*[Placeholder: JSON showing commandId usage vs full command]*

## CLI Usage

### Basic Commands

- `csm-orc run template.json` - Execute a run template
- `csm-orc run --dry-run template.json` - Validate without executing
- `csm-orc run --display-env --gen-env-target file.env template.json` - Generate environment file
- `csm-orc list-templates` - Show available command templates

### Local Development
Use `csm exec` to set up environment variables, then run templates locally with the same environment as production.

## Advanced Features

### Exit Handlers
Steps that always run at the end, regardless of success or failure. Useful for cleanup operations.

### Data Transfer Between Steps
Modern orchestrator versions support passing data between steps using outputs and inputs.

*[Placeholder: JSON showing step outputs and inputs configuration]*

## Integration with Docker

When building Docker images, CSM-ORC serves as the entry point. The platform automatically provides environment variables when running containers.

## Best Practices

- Use meaningful step names
- Use command templates for repeated operations
- Test locally before deploying

## Further Reading

