# Claude Code Three Tier Architecture POC

## Overview

Claude Code Three Tier Architecture POC is an implementation example of an **orchestration foundation using a three-tier architecture** that leverages Claude Code's capabilities.

It provides a highly extensible architecture applicable to various development workflows through generic implementation guidelines.

## 🏗️ Architecture Structure

```
┌─────────────────────────────────────────┐
│ Layer 1: Orchestrator                  │
│ - Behavior control via POML files      │
│ - Markdown file loading & execution     │
│ - Overall flow management               │
└─────────────────────────────────────────┘
┌─────────────────────────────────────────┐
│ Layer 2: Custom Slash Commands         │
│ - Defined as markdown files (.md)      │
│ - Domain-specific processing logic      │
│ - Sub-agent selection & invocation     │
└─────────────────────────────────────────┘
┌─────────────────────────────────────────┐
│ Layer 3: Sub-Agents                    │
│ - Specialized agents executed via Task │
│ - Actual work execution (greetings,    │
│   stories, emotion expressions, etc.)   │
│ - Behavior defined by POML files       │
└─────────────────────────────────────────┘
```

## 📁 File Structure

### Current Implementation Example (Character Agents)

```
.claude/
├── commands/
│   ├── command-zundamon.md          # Zundamon command definition
│   ├── command-lum-chan.md          # Lum-chan command definition
│   └── orchestrator.md              # Orchestrator definition
└── agents/
    ├── zundamon-greeter.md          # Zundamon greeting sub-agent
    ├── zundamon-storyteller.md      # Zundamon storytelling sub-agent
    ├── lum-greeter.md               # Lum-chan greeting sub-agent
    └── lum-emotion.md               # Lum-chan emotion sub-agent

poml/
├── commands/
│   ├── orchestrator.poml            # Orchestrator behavior control
│   ├── zundamon.poml                # Zundamon command behavior control
│   └── lum.poml                     # Lum-chan command behavior control
└── agents/
    ├── zundamon-greeter-behavior.poml      # Zundamon greeting behavior definition
    ├── zundamon-storyteller-behavior.poml # Zundamon storytelling behavior definition
    ├── lum-greeter-behavior.poml          # Lum-chan greeting behavior definition
    └── lum-emotion-behavior.poml          # Lum-chan emotion behavior definition
```

## 🚀 Usage

### Basic Execution

```bash
/orchestrator
```

### Individual Command Execution

```bash
/zundamon    # Zundamon character command
/lum-chan    # Lum-chan character command
```

## ⚡ Execution Flow

1. **Layer 1**: Orchestrator loads POML files and determines execution order
2. **Layer 2**: Each character command selects sub-agents based on POML files
3. **Layer 3**: Specialized sub-agents execute specific tasks (greetings, story generation, emotion expression)

## 🎯 Design Features

- **Loose Coupling Between Layers**: Each layer is independent, minimizing the impact of changes
- **Dynamic Behavior Determination**: Runtime behavior determination via POML files
- **Clear Responsibilities**: Each layer has a single responsibility, ensuring high maintainability
- **Reusability**: Templating enables reuse across different domains
- **Extensibility**: Easy addition of new sub-agents and commands

## 📚 Documentation

For detailed implementation guidelines and guidance, please refer to:

- [Implementation Guidelines (Japanese)](docs/architecture-guide.md)
- [Implementation Guide (English)](docs/architecture-guide-en.md)

## 🛠️ Application Method

This POC is designed as a generic implementation guideline and can be applied to any workflow using the following steps:

1. Define workflow classification
2. Determine namespace
3. Design required sub-agents
4. Create specialized commands
5. Define orchestrator

For details, please refer to [docs/architecture-guide-en.md](docs/architecture-guide-en.md).