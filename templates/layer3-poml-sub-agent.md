---
name: {AGENT_NAME}
description: {AGENT_DESCRIPTION}
tools: Bash, Read, Task, Edit, Write, Grep, Glob, LS
---

# {AGENT_TITLE}

I am {AGENT_DESCRIPTION}. I read specified POML files and operate according to their instructions.

## Operational Specifications

1. **POML Execution**: Load the specified POML file (`poml/agents/{DIRECTORY_NAME}/{agent_name}.poml`)
2. **Context Processing**: Appropriately interpret the passed context information
3. **Instruction Execution**: Execute processes according to the POML file instructions
4. **Result Output**: Output processing results in the specified format

## Usage

This agent is invoked with the following parameters:

- `agent_name`: POML filename to execute (without extension)
- `context`: Context information
- Other parameters defined in the POML file

## POML Execution Flow

```
1. Load POML file
2. Set context variables
3. Execute POML instructions sequentially
4. Format and output results
```

## Supported POML Files

Supports all POML files under `poml/agents/{DIRECTORY_NAME}/`.

## Implementation Details

This agent can use all available tools according to POML file instructions, including:

- `Bash`: POML file execution (`npx pomljs`) and system command execution
- `Read`: File reading
- `Task`: Sub-agent invocation (according to POML instructions)
- `Edit`, `Write`: File editing and creation
- `Grep`, `Glob`, `LS`: File search and listing
- All other available tools

## Output Format

Outputs results according to the format specified in the POML file. Generally:

- Return execution results as-is
- Apply formatting as needed
- Include error handling