# Run templates

How to use and configure run templates for your solution.

## Overview

Run templates are JSON files that define the workflow for your CosmoTech solution. They orchestrate data processing, simulation execution, and result handling by defining a sequence of steps with dependencies.

## What are Run Templates?

Run templates define:
- **Execution Steps**: Individual operations like data fetch, validation, simulation
- **Step Order**: Using precedents to control execution sequence
- **Environment**: Variables and parameters for configuration
- **Commands**: What gets executed in each step

## Basic Structure

A run template contains:
- **Steps**: Named operations with commands and arguments
- **Precedents**: Define which steps must complete before others start
- **Environment Variables**: Configuration and data passing
- **Templates**: Reusable command definitions

*[Placeholder: Basic JSON structure with steps and precedents]*

## Key Concepts from the Meetings

### Step Dependencies
- **Sequential**: Steps run one after another using precedents
- **Parallel**: Steps with same precedents run simultaneously
- **No Cycles**: Circular dependencies are not allowed

Example workflow: fetch data → validate → run simulation → post-process

### Environment Variables
Standard platform variables include:
- Data paths and scenario information
- API connection details
- Workspace configuration

### Command Templates vs Direct Commands
- **Direct Commands**: Full command definition in the template
- **Command IDs**: Reference to predefined command templates
- **Templates**: Reduce duplication and improve maintainability

*[Placeholder: Example showing commandId vs full command definition]*

## Types of Run Templates

Based on the meetings, there are different types:
- **Simulation Templates**: Execute digital twin simulations
- **ETL Templates**: Extract, transform, and load data
- **Validation Templates**: Data quality checks
- **Processing Templates**: Custom data workflows

## Integration with Simulators

Run templates integrate with CosmoTech simulators by:
- Calling `csm simulate` command with simulation files
- Using environment variables for configuration
- Processing simulation outputs

*[Placeholder: Example showing simulator integration step]*

## Environment and Parameters

### Standard Environment Variables
The platform provides variables for:
- Scenario/Runner identification
- File paths (`/mnt/scenariorun-data`)
- API connection information

### Parameter Passing
- **Environment Variables**: System-level configuration
- **Command Arguments**: Direct parameter passing
- **File-based**: Configuration files and data files

## Local Development

### Running Locally
1. Set up the same environment variables as production
2. Use `csm-orc run template.json` to execute
3. Use `--dry-run` for validation without execution

### Environment Files
- Generate environment files from templates
- Use for consistent local development
- Match production environment setup

## Advanced Features

### Exit Handlers
Steps that always execute at the end, regardless of success/failure. Useful for:
- Cleanup operations
- Sending notifications
- Logging completion status

### Data Transfer Between Steps
Modern versions support passing data between steps:
- **Outputs**: Data produced by a step
- **Inputs**: Data consumed by a step
- Replaces file-based data sharing

*[Placeholder: JSON showing outputs/inputs configuration]*

## Best Practices

- Use clear, descriptive step names
- Minimize dependencies for better parallelization
- Use command templates for repeated operations
- Test locally before deploying
- Implement proper error handling with exit handlers

## Integration with Platform

### API Integration
- Templates integrate with CosmoTech API
- Use runner API for modern templates
- Handle authentication automatically

### Docker Deployment
- Templates run in containerized environments
- Same environment variables in local and production
- Container serves as execution environment

## Further Reading

