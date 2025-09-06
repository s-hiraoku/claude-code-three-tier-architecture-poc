# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a Proof of Concept for a **3-tier architecture orchestration system** using Claude Code features. The design establishes clear separation of concerns across three distinct layers, enabling scalable workflow management without the complexity of monolithic orchestrator files.

## Architecture

The system implements a pure 3-tier hierarchy with centralized orchestration:

```
Layer 1: Orchestrator         - Central coordination and integration of workflows
Layer 2: Workflow Commands    - Independent specialized workflow execution
Layer 3: Sub-Agents          - Atomic task execution via Task tool
```

**Key Design Principle**: Centralized flow control with clear separation of concerns:
- **Layer 1**: Orchestrates workflows sequentially, integrates results from completed workflows
- **Layer 2**: Executes independent workflows, returns to Layer 1 upon completion
- **Layer 3**: Performs specific, atomic tasks when called by Layer 2

## Development Commands

### Install Dependencies
```bash
npm install
```

### Execute POML Files
```bash
# Execute POML with context
npx pomljs --file poml/commands/{namespace}/{file}.poml --context "CONTEXT=your_context"

# Execute specific character workflows
npx pomljs --file poml/commands/with-context/zundamon.poml --context "CONTEXT=test"
npx pomljs --file poml/commands/with-context/lum.poml --context "CONTEXT=test"
```

### Test Commands
```bash
# Test full 3-tier orchestration
/with-context:orchestrator "your test context"

# Test individual workflows
/no-context:zundamon
/no-context:lum-chan
```

## Key Architecture Patterns

### Layer 1: Orchestrator Commands
- Read POML files using `npx pomljs --file {poml-path} --context "CONTEXT={input}"`
- Coordinate execution of multiple Layer 2 workflows
- Integrate and present final results
- Focus purely on orchestration, not implementation details

### Layer 2: Workflow Commands
- Execute specialized workflows independently
- Use POML files for dynamic behavior: `npx pomljs --file poml/commands/{namespace}/{workflow}.poml`
- Call Layer 3 sub-agents via Task tool when needed
- Return results to Layer 1 upon completion
- **No direct Layer 2 to Layer 2 communication** - all coordination happens through Layer 1

### Layer 3: Sub-Agents
- Defined in `.claude/agents/{namespace}/` 
- Called via Task tool: `Task(subagent_type="agent-name", description="...", prompt="...")`
- Behavior defined in corresponding `poml/agents/{namespace}/*-behavior.poml` files
- Handle atomic, specific tasks

## File Structure

```
.claude/
├── commands/{namespace}/     # Custom slash commands (Layer 1 & 2)
└── agents/{namespace}/       # Sub-agent definitions (Layer 3)

poml/
├── commands/{namespace}/     # POML behavior files for commands
├── agents/{namespace}/       # POML behavior files for agents
└── context/                  # Context management files

context/
└── context.poml             # Shared context storage
```

## Development Rules

### Verified Communication Patterns
- **Layer 1 → Layer 2**: Use Read tool to load workflow .md files, execute instructions directly
- **Layer 2 → Layer 1**: Return results upon workflow completion (no direct Layer 2 to Layer 2)
- **Layer 2 → Layer 3**: Use Task tool to call sub-agents
- **POML Execution**: Always use `npx pomljs --file {path} --context "CONTEXT={value}"`

### Prohibited Patterns (Verified Failures)
- ❌ Calling custom slash commands via `claude -p` (causes session conflicts)
- ❌ Using Task tool for Layer 2 .md commands (wrong abstraction level)
- ❌ Direct agent calls without proper POML context loading

### Context Management
- Context flows through POML context variables
- Results are accumulated in `context/context.poml` 
- Use JSONL format for saving agent responses

## Implementation Status

### Verified Capabilities
- ✅ **Full 3-tier orchestration**: Layer 1 coordinating multiple Layer 2 workflows sequentially
- ✅ **Layer 2 independence**: Each workflow executes independently and returns to Layer 1
- ✅ **Layer 2 to Layer 3 execution**: Workflows calling atomic sub-agents
- ✅ **Context management**: Shared state across workflow executions
- ✅ **Template system**: Reusable patterns in `/templates/` directory

### Core Testing Examples
- `/with-context:orchestrator "test context"` - Full 3-tier workflow with centralized orchestration
- Individual workflow testing via `/no-context:zundamon`, `/no-context:lum-chan`

### Templates Available
- `templates/layer1-orchestrator.poml` - Layer 1 orchestrator template
- `templates/layer2-command-with-calling.md` - Layer 2 command with peer calling capability

This POC establishes the architectural foundation for scalable, modular workflow systems using Claude Code's 3-tier approach.