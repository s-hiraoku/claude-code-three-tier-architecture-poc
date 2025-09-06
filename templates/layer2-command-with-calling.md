---
name: {COMMAND_NAME}
description: {COMMAND_DESCRIPTION} - Layer 2 command with inter-command calling capability
tools: [Read, Bash, Task]
---

# {COMMAND_NAME} - Layer 2 Specialized Command

I am a specialized Layer 2 command that can coordinate with other Layer 2 commands to complete complex workflows.

## My Responsibilities

1. Execute domain-specific processing for {DOMAIN}
2. Call other Layer 2 commands when needed using file-based coordination
3. Call Layer 3 sub-agents via Task tool
4. Integrate and return coordinated results

## Command-to-Command Calling Pattern

When I need to call other Layer 2 commands:

### Method: File-based Coordination
```javascript
// Step 1: Read target command file
Read(".claude/commands/{NAMESPACE}/{TARGET_COMMAND}.md")

// Step 2: Execute target command instructions directly
// Follow the instructions in the loaded .md file

// Step 3: If target has POML, execute it
Bash(`CONTEXT="{CONTEXT}" npx pomljs --file poml/commands/{NAMESPACE}/{TARGET_COMMAND}.poml --context "CONTEXT={CONTEXT}"`)
```

## Sub-Agent Calling Pattern

For Layer 3 sub-agents, use standard Task tool:
```javascript
Task({
  subagent_type: "{SUBAGENT_TYPE}",
  description: "{TASK_DESCRIPTION}",
  prompt: "{DETAILED_INSTRUCTIONS}"
})
```

## Execution Flow

1. **Context Loading**: Load context from shared context files if needed
2. **Dependency Resolution**: Identify which other commands/agents are needed
3. **Coordination**: Call other Layer 2 commands using file-based method
4. **Specialized Processing**: Execute domain-specific tasks via sub-agents
5. **Integration**: Combine results and return coordinated response

## CC-Deck v2 Compatibility

This command template supports:
- ✅ Layer 2 → Layer 2 command calling
- ✅ Layer 2 → Layer 3 sub-agent calling  
- ✅ Complex workflow decomposition
- ✅ Session conflict avoidance
- ✅ Scalable command orchestration

Perfect for decomposing 300+ line orchestrator files into manageable, specialized commands.