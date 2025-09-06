---
description: Layer 2 generic agent command with parameter parsing support
argument-hint: [agent-name or key=value parameters]
allowed-tools: [Read, Bash, Edit]
---

# Enhanced Generic Agent Command - Layer 2

Generic agent command that supports both simple and key-value parameter formats from orchestrator.poml.

## Usage

```bash
# Simple format
/with-context:command-agent zundamon

# Key-value format (from orchestrator.poml)
/with-context:command-agent agent=zundamon,context_file=context/context.poml
```

## Task

Execute the following processes with enhanced parameter parsing:

1. **Parse Arguments**: Handle multiple parameter formats

   ```bash
   # Parse arguments to extract command name and context file
   if [[ "$ARGUMENTS" == *"="* ]]; then
     # Key-value format: command=zundamon,context_file=...
     eval $(echo "$ARGUMENTS" | tr ',' '\n')
     AGENT_NAME="$command"
     CONTEXT_FILE="${context_file:-context/context.poml}"
   else
     # Simple format: zundamon
     AGENT_NAME="$ARGUMENTS"
     CONTEXT_FILE="context/context.poml"
   fi
   ```

2. **Load Context**: Read context from specified file

   ```bash
   USER_INPUT=$(grep 'user_input' "$CONTEXT_FILE" | sed 's/.*value="\([^"]*\)".*/\1/')
   ACCUMULATED_RESULTS=$(grep 'accumulated_results' "$CONTEXT_FILE" | sed 's/.*value=.\([^}]*\)}.*/\1/')
   ```

3. **Execute POML**: Run agent with proper context

   ```bash
   AGENT_RESPONSE=$(npx pomljs --file "poml/commands/with-context/$AGENT_NAME.poml" \
     --context "user_input=$USER_INPUT" \
     --context "accumulated_results=$ACCUMULATED_RESULTS")
   ```

4. **Update Context**: Save response to accumulated_results

   ```bash
   # Update the context file with new response
   sed -i '' "s/\"${AGENT_NAME}_response\": \"[^\"]*\"/\"${AGENT_NAME}_response\": \"$AGENT_RESPONSE\"/" "$CONTEXT_FILE"
   ```

5. **Display Results**: Show agent response

## Implementation Details

This command now supports:

- Parameter parsing for orchestrator.poml compatibility
- Flexible context file specification
- Proper JSON escaping for accumulated_results updates
- Error handling for missing files or invalid parameters

The agent response is captured and saved to the appropriate field in accumulated_results.
