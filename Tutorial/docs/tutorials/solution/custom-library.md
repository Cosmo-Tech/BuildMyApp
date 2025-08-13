# Custom library

Integrate and use custom libraries in your CosmoTech solution.

## Overview

Custom libraries allow you to create reusable code components for your CosmoTech solutions. They help avoid code duplication, encapsulate domain-specific logic, and create shared functionality across run templates and projects.

## What are Custom Libraries?

Custom libraries are:
- **Reusable Code Modules**: Common functions and classes
- **Project-Specific Logic**: Business logic for your domain
- **Shared Components**: Code used across multiple solutions
- **Helper Functions**: Utilities for common operations

## Types of Custom Libraries

Based on the meetings, you can create:
- **Solution Libraries**: Specific to your project (like Supply Chain Python Library)
- **Common Libraries**: Shared utilities within a solution
- **External Libraries**: Separate repositories with their own lifecycle
- **In-Project Libraries**: Located within the solution structure

## Library Organization

### Directory Structure
Libraries can be organized in different ways:
- **External Repository**: Separate Git repository (like Supply Chain Python Library)
- **Local Libraries**: Within `run_templates` directory structure
- **Common Folder**: Shared code in a `common` directory
- **Libs Directory**: Dedicated `libs` folder for libraries

*[Placeholder: Directory structure showing library organization options]*

### Integration Methods
- **Requirements.txt**: Reference external libraries
- **Local Installation**: Install from local paths
- **Docker Build**: Include in container image
- **Direct Import**: Import from solution directories

## Creating Custom Libraries

### Library Structure
A typical custom library includes:
- **Python Package**: Standard `__init__.py` and modules
- **Setup Configuration**: `pyproject.toml` or `setup.py`
- **Documentation**: README and API docs
- **Tests**: Unit and integration tests

*[Placeholder: Example library structure]*

### Common Patterns
Libraries typically provide:
- **Simulation Helpers**: Functions to run and process simulations
- **Data Processing**: Transform and validate data
- **API Wrappers**: Simplified interfaces to external services
- **Utility Functions**: Common operations and helpers

## Integration with Run Templates

### Using Libraries in Templates
Import and use custom libraries in run template scripts:
- Set up proper import paths
- Handle dependencies correctly
- Use library functions in step implementations
- Manage error handling and logging

*[Placeholder: Example showing library usage in run template]*

### Installation in Docker
When building Docker images:
- Only content of `run_templates` is copied to container
- External libraries must be in requirements or locally available
- Local libraries should be within `run_templates` structure
- Use pip install during Docker build process

## Development Workflow

### Local Development
1. **Create Library**: Set up Python package structure
2. **Develop Functions**: Implement reusable functionality
3. **Test Locally**: Unit test and integration test
4. **Install**: Install in development environment
5. **Use in Templates**: Import and use in run templates

### Library Distribution
- **Private Repositories**: Git-based distribution
- **Local Installation**: Direct pip install from source
- **Container Inclusion**: Package with Docker image
- **Requirements Management**: Version control and dependency management

## Best Practices

### Library Design
- **Single Responsibility**: Each library has a clear purpose
- **Clear Interfaces**: Well-defined APIs and documentation
- **Error Handling**: Consistent error handling patterns
- **Logging**: Standardized logging throughout

### Code Organization
- Keep libraries focused and modular
- Minimize dependencies between libraries
- Use meaningful names and documentation
- Follow Python packaging best practices

### Version Management
- Use semantic versioning for library releases
- Maintain compatibility across versions
- Document breaking changes
- Provide migration guides when needed

## Example: Supply Chain Library

Based on the meetings, the Supply Chain solution uses:
- **External Repository**: Separate Git repo for the library
- **Simulation Functions**: Code to run simulations with data
- **Requirements Integration**: Referenced in solution requirements
- **Reusable Components**: Shared across multiple run templates

*[Placeholder: Example showing Supply Chain library integration]*

## Library vs. Common Code

### When to Use Libraries
- Code used across multiple projects
- Complex functionality that needs testing
- Code that has its own release cycle
- Functionality that might be open-sourced

### When to Use Common Folder
- Project-specific utilities
- Simple helper functions
- Code that changes frequently with the solution
- Quick prototyping and development

## Integration with CoAL

Custom libraries often work alongside CoAL:
- **Extend CoAL**: Add domain-specific functionality
- **Use CoAL**: Leverage CoAL for common operations
- **Complement CoAL**: Fill gaps in standard functionality
- **Standard Patterns**: Follow CoAL conventions and patterns

## Further Reading

