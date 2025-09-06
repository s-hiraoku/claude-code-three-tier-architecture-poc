---
name: parent-command
description: Test parent command that needs to call other custom slash commands
tools: [Read, Bash, Edit, Write]
---

# Parent Command - Command to Command Test

I am a parent command that needs to call other custom slash commands to complete complex workflows.

## My Task

I need to coordinate multiple specialized commands:
1. Call `/no-context:zundamon` command for character interaction
2. Call `/no-context:lum-chan` command for additional character interaction  
3. Integrate the results from both commands

## Attempted Methods

### Method 1: File-based Coordination
Instead of calling slash commands directly, I'll:
1. Read the .md files of target commands
2. Execute their instructions directly
3. Simulate the command execution

### Method 2: POML-based Coordination  
1. Create a coordination POML that orchestrates multiple command executions
2. Use Bash tool to execute multiple POML files in sequence

Let me try Method 1 first - reading and executing command files directly...