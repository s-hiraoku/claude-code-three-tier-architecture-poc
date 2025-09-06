---
description: Layer 1 {DESCRIPTION} - dynamically reads POML behavior and calls Layer 2 custom slash commands
argument-hint: [{ ARGUMENT_HINT }]
allowed-tools: [Read, Bash, Task]
---

# {DISPLAY_NAME} (Layer 1)

I am the Layer 1 {ROLE_DESCRIPTION} for Claude Code Three Tier Architecture POC.

## My Task

1. Read the POML behavior file at `poml/commands/{DIRECTORY_NAME}/{POML_FILE_NAME}.poml` using `npx pomljs --file poml/commands/{DIRECTORY_NAME}/{POML_FILE_NAME}.poml --context "CONTEXT=$ARGUMENTS"`
2. Parse and understand the instructions in the POML output
3. Execute the commands as instructed by the POML output
4. Display the integrated results

## Execution

I'll read the POML {POML_TYPE} file with the user's arguments as context, then execute the commands as instructed.

User arguments: $ARGUMENTS

I'll use the Bash tool to execute the POML {POML_TYPE} file with the user's arguments as context:

```bash
npx pomljs --file poml/commands/{POML_FILE_NAME}.poml --context "CONTEXT=$ARGUMENTS"
```

Then I'll follow the instructions from the POML output.

## Template Variables

Replace the following placeholders when using this template:

- `{DESCRIPTION}`: Brief description of the command's purpose - used in frontmatter `description` field
- `{ARGUMENT_HINT}`: Hint for expected arguments (e.g., "topic", "query") - used in frontmatter `argument-hint` field
- `{DISPLAY_NAME}`: Human-readable name displayed in the interface - used in markdown heading
- `{ROLE_DESCRIPTION}`: Description of the agent's role - used in body text
- `{POML_FILE_NAME}`: Full path to POML file (e.g., "with-context/orchestrator") - used in execution commands
- `{POML_TYPE}`: Type description of the POML file (e.g., "orchestrator", "agent") - used in descriptive text

Note: Command name is determined by the filename, not by a frontmatter field.

## Anthropic Custom Slash Command Specification Compliance

This template follows Anthropic's custom slash command format:

- **Frontmatter**: YAML frontmatter with fields (`description`, `argument-hint`, `allowed-tools`)
- **Standard Variables**: Uses `$ARGUMENTS` for all user input, or `$1`, `$2` for individual arguments
- **Tool Declaration**: Uses `allowed-tools` to specify permitted tools (not `tools`)
- **No Name Field**: Command name is derived from filename (not frontmatter)
- **Markdown Format**: Standard markdown with clear section structure

## Layer Architecture

This template creates Layer 1 commands that:

- Receive user input directly through `$ARGUMENTS`
- Read and execute Layer 1 POML files
- Call and coordinate multiple Layer 2 custom slash commands
- Provide integrated analysis and results

## Usage Example

For a Layer 1 orchestrator command (saved as `orchestrator.md`):

- `{DESCRIPTION}` → "orchestrator that coordinates multiple agents"
- `{ARGUMENT_HINT}` → "topic"
- `{DISPLAY_NAME}` → "Multi-Agent Orchestrator"
- `{ROLE_DESCRIPTION}` → "orchestrator coordinator"
- `{POML_FILE_NAME}` → "with-context/orchestrator"
- `{POML_TYPE}` → "orchestrator"
