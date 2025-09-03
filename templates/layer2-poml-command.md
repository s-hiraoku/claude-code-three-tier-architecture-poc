---
description: Layer 2 generic agent command that loads and executes POML files based on agent name
argument-hint: [agent-name]
allowed-tools: [Read, Bash]
---

# Generic Agent Command - Layer 2

Generic agent command for custom slash commands.
Loads and executes corresponding POML files based on the agent name specified in arguments.

## Usage

```
/command-agent [agent-name]
```

- `$ARGUMENTS`: Agent name to execute

## Task

Execute the following processes sequentially:

1. **Get Arguments**: Extract agent name from `$ARGUMENTS`
2. **Load Context**: Read the context file at `context/context.poml`
3. **Execute POML**: Read the POML behavior file at `poml/commands/{DIRECTORY_NAME}/$ARGUMENTS.poml` using `user_input="$(grep 'user_input' context/context.poml | sed 's/.*value="\([^"]*\)".*/\1/')" context="$(grep 'user_input' context/context.poml | sed 's/.*value="\([^"]*\)".*/\1/')" npx pomljs --file poml/commands/with-context/$ARGUMENTS.poml`
4. **Follow Instructions**: Parse and understand the instructions in the POML output
5. **Output Results**: Display the execution results

## Implementation

This command is a thin wrapper that loads and executes specified POML files.
Actual processing logic is defined in each behavior.poml file.
