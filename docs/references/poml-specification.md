# POML (Prompt Orchestration Markup Language) Specification

## Overview

POML is an HTML-like markup language designed for structured prompt engineering with Large Language Models. It employs semantic components to encourage modular design and includes a built-in templating engine for dynamic content generation.

## Core Syntax

POML follows XML/HTML-like syntax with semantic components:
- Well-formed XML structure with opening and closing tags
- Self-closing tags supported with `/>`
- Attributes enclosed in quotes
- Case-sensitive tag names

## Semantic Components

### Basic Components
- `<role>` - Define agent roles and personas
- `<task>` - Specify tasks and instructions
- `<example>` - Provide examples for context
- `<document>` - Structure document content
- `<table>` - Display tabular data
- `<img>` - Embed images (file path or base64)
- `<audio>` - Embed audio files (file path or base64)

### Meta Components
- `<stylesheet>` - Apply styling and presentation rules
- `<meta>` - Document metadata and configuration

## Template Engine

### Template Expressions
Template expressions use double curly brackets `{{}}` for dynamic content:

```poml
{{variableName}}
{{a + b}}
{{firstName + " " + lastName}}
{{condition ? "yes" : "no"}}
```

### Variable Definitions (`<let>`)

#### Direct Assignment
```poml
<let name="greeting">Hello, world!</let>
<let name="count" value="5" />
```

#### File Import
```poml
<let name="users" src="users.json" />
<let src="config.json" />
```

#### Inline JSON
```poml
<let name="person">
  {
    "name": "Alice",
    "age": 30
  }
</let>
```

### Conditional Rendering
Use the `if` attribute on any element:

```poml
<p if="isVisible">This paragraph is visible.</p>
<div if="user.isAdmin">Admin content</div>
```

### Looping
Use the `for` attribute for iteration:

```poml
<item for="item in ['apple', 'banana', 'cherry']">
  {{item}}
</item>

<div for="user in users">
  Name: {{user.name}}, Age: {{user.age}}
</div>
```

#### Loop Variables
- `loop.index` - Current iteration index (0-based)
- `loop.length` - Total number of items
- `loop.first` - Boolean, true if first iteration
- `loop.last` - Boolean, true if last iteration

### File Inclusion
```poml
<include src="snippet.poml" />
<include src="row.poml" for="i in [1,2,3]" />
```

## Data Types and Type Casting

POML supports automatic type conversion for:
- **Boolean**: `true`, `false`
- **Number**: Integer and floating-point
- **Object**: JSON objects
- **String**: Text values

## Advanced Features

### Styling System
```poml
<stylesheet>
  role {
    font-weight: bold;
    color: blue;
  }
</stylesheet>
```

### External Data Integration
- JSON files via `src` attribute in `<let>`
- Image and audio file embedding
- Modular component inclusion

### Context Variables
Variables can be passed via CLI using `--context` parameter:
```bash
pomljs file.poml --context "{'variable': 'value'}"
```

## Important Notes

### What POML Does NOT Support
- `<read>` tag (does not exist in POML specification)
- Complex control flow beyond `if` and `for`
- Direct file system operations within templates

### Best Practices
1. Use semantic components for structure
2. Separate content from presentation with stylesheets
3. Leverage templating for dynamic content
4. Use `<include>` for modular design
5. Pass dynamic variables via CLI `--context` parameter

### Error Types
- Syntax Errors: Invalid XML/POML structure
- Component Validation: Unknown or incorrect component usage
- Attribute Errors: Invalid attributes for components
- File Reference Issues: Problems with external resources
- Expression Evaluation Errors: Issues with template expressions

## Development Tools

### Visual Studio Code Extension
- Syntax highlighting
- Auto-completion
- Real-time previews
- Inline diagnostics

### SDKs Available
- TypeScript API
- Python API

## Context Passing Architecture

For multi-layer architectures like Claude Code Three Tier:
1. Use `<let>` variables for context definition
2. Pass context via CLI `--context` parameter
3. Reference variables with `{{variableName}}` syntax
4. Use conditional rendering with `if` attribute
5. Leverage `<task>` components for agent delegation

Example context passing:
```bash
pomljs orchestrator.poml --context "{'TOPIC': 'machine learning', 'MODE': 'collaborative'}"
```

```poml
<let name="topic" value="{{TOPIC}}"/>
<let name="mode" value="{{MODE}}"/>

<task if="mode == 'collaborative'" agent="collaborative-agent">
  Topic: {{topic}}
</task>