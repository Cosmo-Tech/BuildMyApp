# Custom commands

Define and use custom commands for advanced operations in your solution.

## Overview

Custom commands allow you to create reusable command templates that can be referenced by ID instead of repeating complex command definitions. They help reduce code duplication and create standardized operations across run templates.

## What are Custom Commands?

Custom commands are:
- **Command Templates**: Reusable command definitions with parameters
- **Command IDs**: Named references instead of full command definitions
- **Plugin Extensions**: Extend orchestrator functionality
- **Template System**: Parameterized command structures

## Command vs Command ID

### Direct Commands
Full command definition in run template:
```json
{
  "command": "python",
  "arguments": ["script.py", "--param", "value"],
  "environment": {"VAR": "value"}
}
```

### Command IDs
Reference to predefined template:
```json
{
  "commandId": "my-custom-command"
}
```

*[Placeholder: JSON showing commandId vs full command comparison]*

## Creating Custom Commands

### Plugin Structure
Custom commands are organized in plugins:
- **Plugin Directory**: `cosmotech/orchestrator_plugins/`
- **Template Files**: JSON files defining command templates
- **Manifest**: Plugin metadata and configuration

*[Placeholder: Plugin directory structure example]*

### Basic Template Definition
A command template includes:
- **ID**: Unique identifier for the command
- **Description**: Human-readable description
- **Command**: The actual command to execute
- **Arguments**: Parameter templates
- **Environment**: Environment variable definitions

*[Placeholder: Basic command template JSON structure]*

## Template System

### Variable Substitution
Templates support parameter substitution:
- Use variables in command arguments
- Environment variable replacement
- Default values for parameters
- Required vs optional parameters

*[Placeholder: Template with variable substitution example]*

### Plugin Loading
Based on the meetings:
- Plugins are loaded alphabetically
- Local templates override library templates
- Use `csm-orc list-templates` to see available templates
- Templates can be distributed as Python packages

## Common Template Patterns

### Data Processing Templates
Common patterns include:
- **fetch-data**: Standard data download operations
- **validate-data**: Data validation steps
- **transform-data**: Data transformation operations
- **send-to-storage**: Export to various storage systems

### Utility Templates
- **hello-world**: Simple example template
- **python-script**: Execute Python scripts with parameters
- **file-operations**: Common file manipulation tasks

*[Placeholder: Example template definitions for common patterns]*

## Advanced Features

### Exit Handlers
Templates can be marked as exit handlers:
- Always run at the end regardless of success/failure
- Useful for cleanup operations
- Send notifications or status updates
- Handle error conditions

*[Placeholder: Exit handler template example]*

### Template Parameters
Templates support various parameter types:
- **Environment Variables**: Runtime configuration
- **Command Arguments**: Direct parameter passing
- **Optional Parameters**: Parameters with defaults
- **Required Parameters**: Must be provided

## Plugin Development

### Creating Plugin Packages
Structure plugin packages for distribution:
- **Python Package**: Standard package structure
- **Entry Points**: Define plugin entry points
- **Installation**: Install as pip package
- **Version Management**: Semantic versioning

*[Placeholder: Plugin package structure example]*

### Local vs Distributed Plugins
- **Local Plugins**: Project-specific templates
- **Distributed Plugins**: Shared across projects
- **Installation Methods**: pip install, local development
- **Namespace Management**: Avoid naming conflicts

## Integration with CoAL

CoAL provides many standard templates:
- **CSM Data Templates**: Data operation shortcuts
- **Storage Templates**: Upload/download operations
- **API Templates**: Platform API interactions
- **Database Templates**: Database operation shortcuts

## Best Practices

### Template Design
- Use clear, descriptive IDs
- Provide comprehensive descriptions
- Design for reusability
- Handle errors gracefully

### Parameter Management
- Use environment variables for configuration
- Provide sensible defaults
- Validate required parameters
- Document parameter usage

### Plugin Organization
- Group related templates in plugins
- Use consistent naming conventions
- Maintain backward compatibility
- Document template APIs

## Development Workflow

### Local Development
1. **Create Template**: Define command template JSON
2. **Create Plugin**: Organize in plugin structure
3. **Install Locally**: Install as editable package
4. **Test**: Use in run templates
5. **Iterate**: Refine and improve

### Testing Templates
- Use `csm-orc list-templates` to verify availability
- Test with various parameter combinations
- Validate error handling
- Check integration with run templates

## Template Distribution

### Packaging
- Create Python package with proper structure
- Include manifest and metadata
- Use semantic versioning
- Provide documentation and examples

### Installation
- Install from Git repositories
- Use pip for package management
- Support development installations
- Handle dependencies properly

## Further Reading

