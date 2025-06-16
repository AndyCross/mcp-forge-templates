# MCP-Forge Templates

This repository contains official and community templates for [MCP-Forge](https://github.com/AndyCross/mcp-forge), a powerful CLI tool for managing Claude Desktop MCP server configurations.

## 📋 Table of Contents

- [Overview](#overview)
- [Template Structure](#template-structure)
- [Variable System](#variable-system)
- [Templating Engine](#templating-engine)
- [Best Practices](#best-practices)
- [Examples](#examples)
- [Contributing](#contributing)
- [Validation](#validation)

## 🎯 Overview

MCP-Forge templates provide a standardized way to configure MCP servers with:
- **Variable substitution** using Handlebars templating
- **Type-safe variables** with validation
- **Cross-platform compatibility**
- **Rich metadata** for discovery and documentation
- **Conditional logic** for flexible configurations

## 📁 Template Structure

Every template is a JSON file with the following structure:

```json
{
  "name": "template-name",
  "version": "1.0.0",
  "description": "Brief description of what this template does",
  "author": "Your Name or Organization",
  "tags": ["category", "type", "keywords"],
  "platforms": ["windows", "macos", "linux"],
  "variables": {
    // Variable definitions (see Variable System section)
  },
  "config": {
    "command": "executable-name",
    "args": ["arg1", "arg2", "{{variable}}"],
    "env": {
      "ENV_VAR": "{{variable}}"
    }
  },
  "requirements": {
    "nodejs": ">=18.0.0",
    "python": ">=3.8.0"
  },
  "setup_instructions": "Additional setup instructions for users"
}
```

### Required Fields

- **`name`**: Unique identifier (lowercase, hyphens allowed)
- **`version`**: Semantic version (e.g., "1.0.0")
- **`description`**: Clear, concise description
- **`author`**: Author name or organization
- **`tags`**: Array of relevant tags for discovery
- **`platforms`**: Supported platforms
- **`config`**: Server configuration with command, args, and environment

### Optional Fields

- **`variables`**: Variable definitions for user input
- **`requirements`**: System requirements
- **`setup_instructions`**: Additional setup guidance

## 🔧 Variable System

Variables enable dynamic configuration based on user input. MCP-Forge supports five variable types:

### 1. String Variables

```json
{
  "api_key": {
    "type": "string",
    "description": "Your API key",
    "required": true
  },
  "host": {
    "type": "string",
    "description": "Server hostname",
    "default": "localhost",
    "required": false
  }
}
```

**Properties:**
- `type`: "string"
- `description`: User-friendly description
- `required`: Boolean (default: false)
- `default`: Default value
- `validation`: Optional validation rule name (basic validation only)

### 2. Number Variables

```json
{
  "port": {
    "type": "number",
    "description": "Server port",
    "default": 8080,
    "required": false
  }
}
```

**Properties:**
- `type`: "number"
- `default`: Default number
- `description`: User-friendly description
- `required`: Boolean (default: false)

### 3. Boolean Variables

```json
{
  "readonly": {
    "type": "boolean",
    "description": "Enable read-only mode",
    "default": false,
    "required": false
  }
}
```

**Properties:**
- `type`: "boolean"
- `default`: true | false
- `description`: User-friendly description
- `required`: Boolean (default: false)

### 4. Array Variables

```json
{
  "paths": {
    "type": "array",
    "description": "List of directories to access",
    "default": ["/home/user/docs", "/home/user/projects"],
    "required": true
  }
}
```

**Properties:**
- `type`: "array"
- `default`: Array of default values
- `description`: User-friendly description
- `required`: Boolean (default: false)
- `validation`: Optional validation rule applied to each array element

### 5. Select Variables

```json
{
  "ssl_mode": {
    "type": "select",
    "description": "SSL connection mode",
    "options": ["disable", "allow", "prefer", "require"],
    "default": "prefer",
    "required": false
  }
}
```

**Properties:**
- `type`: "select"
- `options`: Array of valid choices (required for select type)
- `default`: Must be one of the options
- `description`: User-friendly description
- `required`: Boolean (default: false)

## 🎨 Templating Engine

MCP-Forge uses Handlebars for variable substitution and conditional logic.

### Basic Variable Substitution

```json
{
  "config": {
    "command": "myserver",
    "args": ["--host", "{{host}}", "--port", "{{port}}"],
    "env": {
      "API_KEY": "{{api_key}}",
      "DEBUG": "{{debug}}"
    }
  }
}
```

### Conditional Logic

Use Handlebars conditionals for optional configuration:

```json
{
  "env": {
    "REQUIRED_VAR": "{{required_value}}",
    "{{#if optional_var}}OPTIONAL_VAR{{/if}}": "{{optional_var}}",
    "{{#unless readonly}}WRITE_MODE{{/unless}}": "true"
  }
}
```

### Array Iteration

Process arrays with `{{#each}}`:

```json
{
  "args": [
    "myserver",
    "{{#each paths}}--path={{this}}{{#unless @last}} {{/unless}}{{/each}}"
  ]
}
```

### Built-in Variables

MCP-Forge provides built-in variables:

- `{{home_dir}}`: User's home directory
- `{{config_dir}}`: Application config directory
- `{{platform}}`: Current platform (windows/macos/linux)

## ✨ Best Practices

### 1. Naming Conventions

- **Template names**: lowercase-with-hyphens
- **Variable names**: snake_case
- **Environment variables**: UPPER_CASE

### 2. Security Considerations

- Mark sensitive variables appropriately (they will be masked in output)
- Use descriptive names for credential variables (api_key, token, password)
- Provide setup instructions for credential management

### 3. User Experience

- Provide clear, helpful descriptions
- Use sensible defaults when possible
- Group related variables logically
- Include comprehensive setup instructions

### 4. Cross-Platform Compatibility

- Test on all supported platforms
- Use platform-agnostic paths when possible
- Consider platform-specific requirements

### 5. Error Handling

- Use validation to catch common errors
- Provide clear error messages in descriptions
- Include troubleshooting in setup instructions

## 📚 Examples

### Simple API Server Template

```json
{
  "name": "simple-api",
  "version": "1.0.0",
  "description": "Simple API server with authentication",
  "author": "MCP-Forge",
  "tags": ["api", "simple", "auth"],
  "platforms": ["windows", "macos", "linux"],
  "variables": {
    "api_key": {
      "type": "string",
      "description": "Your API key for authentication",
      "required": true
    },
    "base_url": {
      "type": "string",
      "description": "API base URL",
      "default": "https://api.example.com",
      "required": false
    },
    "timeout": {
      "type": "number",
      "description": "Request timeout in seconds",
      "default": 30,
      "required": false
    }
  },
  "config": {
    "command": "npx",
    "args": ["-y", "@example/api-server"],
    "env": {
      "API_KEY": "{{api_key}}",
      "BASE_URL": "{{base_url}}",
      "TIMEOUT": "{{timeout}}"
    }
  },
  "requirements": {
    "nodejs": ">=18.0.0"
  },
  "setup_instructions": "Obtain an API key from https://example.com/api-keys and ensure Node.js is installed."
}
```

### Database Connection Template

```json
{
  "name": "mysql-connector",
  "version": "1.0.0",
  "description": "MySQL database connection with SSL support",
  "author": "MCP-Forge",
  "tags": ["database", "mysql", "sql"],
  "platforms": ["windows", "macos", "linux"],
  "variables": {
    "host": {
      "type": "string",
      "description": "MySQL server hostname",
      "default": "localhost",
      "required": true
    },
    "port": {
      "type": "number",
      "description": "MySQL server port",
      "default": 3306,
      "required": false
    },
    "database": {
      "type": "string",
      "description": "Database name",
      "required": true
    },
    "username": {
      "type": "string",
      "description": "Database username",
      "required": true
    },
    "password": {
      "type": "string",
      "description": "Database password",
      "required": true
    },
    "ssl_mode": {
      "type": "select",
      "description": "SSL connection mode",
      "options": ["disabled", "preferred", "required", "verify_ca", "verify_identity"],
      "default": "preferred",
      "required": false
    },
    "connection_limit": {
      "type": "number",
      "description": "Maximum number of connections",
      "default": 10,
      "required": false
    }
  },
  "config": {
    "command": "npx",
    "args": ["-y", "@example/mysql-server"],
    "env": {
      "MYSQL_HOST": "{{host}}",
      "MYSQL_PORT": "{{port}}",
      "MYSQL_DATABASE": "{{database}}",
      "MYSQL_USER": "{{username}}",
      "MYSQL_PASSWORD": "{{password}}",
      "MYSQL_SSL_MODE": "{{ssl_mode}}",
      "{{#if connection_limit}}CONNECTION_LIMIT{{/if}}": "{{connection_limit}}"
    }
  },
  "requirements": {
    "nodejs": ">=18.0.0",
    "mysql": ">=8.0.0"
  },
  "setup_instructions": "Ensure MySQL server is running and accessible. Create the database and user with appropriate permissions. For production, use SSL certificates and secure connection settings."
}
```

### File System Access Template

```json
{
  "name": "advanced-filesystem",
  "version": "1.0.0",
  "description": "Advanced filesystem access with multiple directories and permissions",
  "author": "MCP-Forge",
  "tags": ["filesystem", "files", "advanced"],
  "platforms": ["windows", "macos", "linux"],
  "variables": {
    "read_paths": {
      "type": "array",
      "description": "Directories with read access",
      "default": ["{{home_dir}}/Documents", "{{home_dir}}/Desktop"],
      "required": true
    },
    "write_paths": {
      "type": "array",
      "description": "Directories with write access",
      "default": ["{{home_dir}}/Projects"],
      "required": false
    },
    "readonly_mode": {
      "type": "boolean",
      "description": "Enable global read-only mode",
      "default": false,
      "required": false
    },
    "max_file_size": {
      "type": "number",
      "description": "Maximum file size in MB",
      "default": 100,
      "required": false
    }
  },
  "config": {
    "command": "npx",
    "args": [
      "-y",
      "@example/filesystem-server",
      "{{#each read_paths}}--read={{this}}{{#unless @last}} {{/unless}}{{/each}}",
      "{{#if write_paths}}{{#each write_paths}}--write={{this}}{{#unless @last}} {{/unless}}{{/each}}{{/if}}"
    ],
    "env": {
      "{{#if readonly_mode}}READONLY_MODE{{/if}}": "{{readonly_mode}}",
      "{{#if max_file_size}}MAX_FILE_SIZE{{/if}}": "{{max_file_size}}"
    }
  },
  "requirements": {
    "nodejs": ">=18.0.0"
  },
  "setup_instructions": "Ensure all specified directories exist and have appropriate permissions. The server will respect system file permissions in addition to the configured access levels."
}
```

## 🤝 Contributing

### Adding New Templates

1. **Create the template file** in the appropriate directory:
   - `official/` for official templates
   - `community/` for community contributions

2. **Follow naming conventions**:
   - File: `template-name.json`
   - Template name: `template-name`

3. **Update catalog.json** with template metadata:

```json
{
  "template-name": {
    "name": "template-name",
    "version": "1.0.0",
    "description": "Template description",
    "author": "Your Name",
    "tags": ["tag1", "tag2"],
    "platforms": ["windows", "macos", "linux"],
    "category": "community",
    "path": "community/template-name.json"
  }
}
```

4. **Test thoroughly**:
   - Validate JSON syntax
   - Test variable substitution
   - Verify cross-platform compatibility
   - Test with MCP-Forge CLI

### Template Categories

- **official**: Maintained by MCP-Forge team
- **community**: Community-contributed templates
- **experimental**: Experimental or beta templates

### Quality Guidelines

- **Complete documentation**: All fields properly documented
- **Sensible defaults**: Provide defaults where appropriate
- **Clear descriptions**: User-friendly variable descriptions
- **Basic validation**: Use validation field for basic checks
- **Security awareness**: Handle credentials appropriately (they will be automatically masked)
- **Cross-platform**: Test on multiple platforms

## ✅ Validation

### JSON Schema Validation

Templates are validated against a JSON schema that enforces:
- Required fields presence
- Correct data types
- Valid platform names
- Proper variable type definitions
- Select variables must have options array

### Runtime Validation

MCP-Forge performs additional validation:
- Variable substitution syntax using Handlebars
- Required variables must be provided
- Select variables must use one of the defined options
- Basic template rendering validation

### Testing Your Template

```bash
# Validate template syntax
mcp-forge template validate your-template.json

# Test template installation
mcp-forge add test-server your-template --dry-run

# Test with variables
mcp-forge add test-server your-template --vars "key=value" --dry-run
```

## 🏷️ Common Tags

Use these standardized tags for better discoverability:

**Categories:**
- `api` - API integrations
- `database` - Database connections
- `filesystem` - File system access
- `search` - Search engines
- `web` - Web services
- `ai` - AI/ML services
- `cloud` - Cloud services
- `dev-tools` - Development tools

**Types:**
- `official` - Official templates
- `community` - Community templates
- `experimental` - Experimental templates
- `core` - Core functionality
- `advanced` - Advanced features

**Technologies:**
- `nodejs` - Node.js based
- `python` - Python based
- `rest` - REST APIs
- `graphql` - GraphQL APIs
- `sql` - SQL databases
- `nosql` - NoSQL databases

## 📖 Additional Resources

- [MCP-Forge Documentation](https://github.com/AndyCross/mcp-forge)
- [Handlebars Documentation](https://handlebarsjs.com/)
- [MCP Protocol Specification](https://modelcontextprotocol.io/)
- [Template Examples](./official/)

---

**Happy templating!** 🚀

For questions or support, please open an issue in the [MCP-Forge repository](https://github.com/AndyCross/mcp-forge/issues).