# Claude Code Three Tier Architecture POC Implementation Guide

## Overview

The Claude Code Three Tier Architecture POC implements orchestration functionality using a three-tier architecture that leverages Claude Code's capabilities. This document provides generic implementation guidelines applicable to any project.

## 🏗️ Three-Tier Architecture Structure

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

## 📁 File Structure Patterns

### Layer 1: Orchestrator Files

```
.claude/commands/{namespace}/
└── {orchestrator-name}.md      # Orchestrator command definition

poml/commands/{namespace}/
└── {orchestrator-name}.poml    # Orchestrator behavior control POML
```

### Layer 2: Custom Slash Command Files

```
.claude/commands/{namespace}/
├── {command-name-1}.md        # Specialized command 1 execution instructions
└── {command-name-2}.md        # Specialized command 2 execution instructions

poml/commands/{namespace}/
├── {command-name-1}.poml      # Specialized command 1 behavior control POML
└── {command-name-2}.poml      # Specialized command 2 behavior control POML
```

### Layer 3: Sub-Agent Files

```
.claude/agents/{namespace}/
├── {subagent-type-1}.md               # Specialized sub-agent 1 definition
├── {subagent-type-2}.md               # Specialized sub-agent 2 definition
└── {subagent-type-3}.md               # Specialized sub-agent 3 definition

poml/agents/{namespace}/
├── {subagent-type-1}-behavior.poml    # Specialized sub-agent 1 behavior definition
├── {subagent-type-2}-behavior.poml    # Specialized sub-agent 2 behavior definition
└── {subagent-type-3}-behavior.poml    # Specialized sub-agent 3 behavior definition
```

## 🗂️ Systematic POML File Organization

POML files are systematically organized in the root `poml/` directory:

```
poml/
├── commands/{namespace}/           # Layer 1 & Layer 2 command behavior definitions
│   ├── {orchestrator-name}.poml   # Orchestrator behavior control
│   ├── {command-name-1}.poml      # Specialized command 1 behavior control
│   └── {command-name-2}.poml      # Specialized command 2 behavior control
├── agents/{namespace}/             # Layer 3 sub-agent behavior definitions
│   ├── {subagent-type-1}-behavior.poml
│   ├── {subagent-type-2}-behavior.poml
│   └── {subagent-type-3}-behavior.poml
└── context/{namespace}/            # Context management
    └── {context-type}.poml
```

**Naming Conventions**:

- `{namespace}`: Workflow classification namespace (kebab-case)
  - Examples: `development`, `testing`, `deployment`, `code-review`, `spec-generation`
- `{orchestrator-name}`: Name representing the orchestrator's role
- `{command-name}`: Command name representing specialized domain
- `{subagent-type}`: Name representing sub-agent's specialty
- `-behavior`: Suffix indicating sub-agent behavior definition

## 🔧 Implementation Methods

### 1. Orchestrator Layer Implementation

#### POML Design Principles

```xml
<poml>
  <role>Integrated management of {orchestrator-domain}</role>

  <task>
    List only files to execute and their order:
    <list>
      <item>Load and execute `.claude/commands/{namespace}/{command-name-1}.md`</item>
      <item>Load and execute `.claude/commands/{namespace}/{command-name-2}.md`</item>
      <item>Integrate and display execution results</item>
    </list>
  </task>

  <p>Execution procedure:</p>
  <list>
    <item>1. Load the first specialized command and execute its content directly</item>
    <item>2. Load the second specialized command and execute its content directly</item>
    <item>3. Display execution results</item>
  </list>

  <!-- Required constraints -->
  <p>Important: Do not execute loaded markdown file content with the Task tool. Read the file content and execute directly according to its instructions.</p>

  <!-- Output format definition -->
  <output-format>
    ## 🎯 {Domain} Execution Results

    ### 📋 Execution Summary
    - Layer 1: {Orchestrator Name}
    - Layer 2: {Specialized Commands}
    - Layer 3: {Domain-Specific Sub-Agents}

    ### 🟢 Command Execution Results
    [Display execution results of each specialized command]

    ### ✅ Execution Completion Confirmation
    [Summary of execution status]
  </output-format>
</poml>
```

#### Separation of Concerns Rules

- ✅ **Should include**: File paths, execution order, expected result levels
- ❌ **Should not include**: Layer 2 implementation details, tool names, sub-agent types

### 2. Custom Slash Commands Layer Implementation

#### Markdown File Design

```markdown
---
name: {command-name}
description: Command that executes {domain-specific} processing
tools: [Read, Bash, Task]
---

# {Command Name} - Layer 2

Command responsible for specialized processing in the {domain} domain

## My Task

1. Load `poml/commands/{namespace}/{command-name}.poml` file to determine behavior
2. Call appropriate sub-agents via Task tool based on POML content
3. Integrate sub-agent execution results and return

## Execution Process

Execute specialized sub-agents sequentially according to behavior defined in POML file,
and generate integrated output.
Specific sub-agent types and execution details are dynamically determined by POML file.
```

**Design Principles**:

- Do not directly specify concrete sub-agent names
- Structure that dynamically determines execution content from POML files
- Focus on domain-specific processing while maintaining generality

### 3. Sub-Agents Layer Implementation

#### Sub-Agent Registration Method

`.claude/agents/{namespace}/{subagent-type}.md` file:

```markdown
---
name: {subagent-type}
description: Sub-agent responsible for {domain-specific} {specialized-function}
model: {claude-model}  # sonnet, opus, haiku
tools: [{required-tools}]  # Read, Bash, Edit, etc.
---

# {Subagent Name} - Layer 3

Sub-agent specialized in {domain-specific} {specialized-function}

## My Role

I execute {specialized-domain} processing as {subagent-persona}

## Task Execution

Execute {domain-specific} tasks according to instructions in corresponding POML file
(`poml/agents/{namespace}/{subagent-type}-behavior.poml`) and return results in expected format
```

#### POML Behavior Definition Patterns

```xml
<poml>
  <role>Responsible for {specialized-domain} as {subagent-persona}</role>

  <task>
    Execute {domain-specific} tasks:
    <list>
      <item>{task-requirement-1}</item>
      <item>{task-requirement-2}</item>
      <item>{output-requirement}</item>
    </list>
  </task>

  <example>
    Input: {example-input}
    Output: {example-output}
  </example>

  <output-format>
    {specific-output-format-requirements}
  </output-format>
</poml>
```

**Sub-Agent Design Guidelines**:

- Single Responsibility Principle: One sub-agent focuses on one specialized function
- Reusability: Generic design callable from multiple commands
- Clear I/O: Clearly define expected input and output formats

## ⚡ Execution Flow

### Generic Execution Pattern

1. **Orchestrator Launch**

   ```bash
   /{namespace}:{orchestrator-name}
   ```

2. **Layer 2 Execution** (Executed within Orchestrator)

   ```javascript
   // Load specialized command markdown file with Read tool
   // Execute directly according to file content instructions
   // Specific Layer 2 processing content defined in each markdown file
   ```

3. **Layer 3 Execution** (Executed from Layer 2)
   ```javascript
   // Call specialized sub-agent with Task tool
   Task({
     subagent_type: "{specialized-agent-type}",
     description: "{domain-specific-description}",
     prompt: "{context-aware-execution-instruction}",
   });
   ```

### Execution Flow Design Principles

1. **Dynamic Execution Content Determination**: Determine behavior at runtime via POML files
2. **Layer Separation of Concerns**: Each layer functions independently, maintaining loose coupling
3. **Context Propagation**: Properly propagate context from upper to lower layers
4. **Result Integration**: Appropriately integrate results at each layer and pass to next layer

## 🚫 Implementation Precautions

### Methods NOT to Execute

- ❌ `claude -p "/{namespace}:{command-name}"` (Output not displayed in Terminal)
- ❌ Execute markdown files (Layer 2) with Task tool
- ❌ Execute custom slash commands with Task tool
- ❌ Include Layer 2 implementation details in Orchestrator layer

### Correct Execution Methods

- ✅ Load markdown files with Read tool and execute content directly
- ✅ Execute only sub-agents (Layer 3) with Task tool
- ✅ Clearly separate concerns of each layer and maintain loose coupling
- ✅ Structure that dynamically determines behavior via POML files

### Separation of Concerns Principles

- **Layer 1**: Only file loading & execution order management
- **Layer 2**: Only specialized domain processing & sub-agent selection
- **Layer 3**: Only concrete task execution

## 📊 Generic Output Format Patterns

Design output format patterns according to project requirements, referencing the following:

```xml
<output-format>
  ## 🎯 {Domain} {Workflow-Type} Execution Results

  ### 📋 Execution Summary
  - Layer 1: {Orchestrator-Name} ({orchestrator-role})
  - Layer 2: {Domain-Commands} ({specialized-functions})
  - Layer 3: {Domain-Sub-Agents} ({specific-capabilities})

  ### 🟢 Specialized Command Execution Results
  #### {Command-1} Execution Results:
```

[{Command-1-Output}]

```

#### {Command-2} Execution Results:
```

[{Command-2-Output}]

```

### ✅ {Workflow-Type} Completion Confirmation
| Layer | Function | Status | Execution Components |
|-------|----------|--------|---------------------|
| Layer 1 | {Orchestrator-Function} | ✅ Normal | - |
| Layer 2 | {Specialized-Commands} | ✅ Normal | {N} commands |
| Layer 3 | {Domain-Sub-Agents} | ✅ Normal | {M} agents |

**Overall Result: {Domain} {Workflow-Type} completed**
</output-format>
```

## 🎯 Design Success Factors

Important elements as generic three-tier architecture implementation guidelines:

1. **Loose Coupling Between Layers**: Each layer is independent, changes in one don't affect others
2. **Dynamic Behavior Determination**: Ensure flexibility through runtime behavior determination via POML files
3. **Clear Responsibilities**: Each layer has single responsibility, high maintainability
4. **Reusability**: Templating enables reuse across different domains
5. **Extensibility**: Easy addition of new sub-agents and commands

## 🔄 Application Process

Process for applying these implementation guidelines to new workflows:

1. **Define Workflow Classification**: Identify target workflows (development, testing, deployment, etc.)
2. **Determine Namespace**: Decide `{namespace}` based on workflow type
3. **File Structure**: Create appropriate directory structure
4. **Layer 3 Design**: Define required sub-agents and their functions
5. **Layer 2 Design**: Create specialized commands and POML files
6. **Layer 1 Design**: Define orchestrator and its behavior
7. **Validation & Adjustment**: Verify operation through actual execution, adjust as needed

## 📝 Implementation Guidelines Summary

The Claude Code Three Tier Architecture POC's three-tier architecture is a generic orchestration foundation that leverages Claude Code's capabilities. Through namespace management by workflow classification, loose coupling between layers, dynamic behavior determination, and clear separation of concerns, it achieves a highly extensible architecture applicable to various development workflows.