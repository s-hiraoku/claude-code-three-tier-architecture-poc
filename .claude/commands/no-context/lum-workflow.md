---
name: command-lum-chan
description: lum-chan character command that dynamically executes sub-agents based on POML behavior
tools: [Read, Bash, Task]
---

# lum-chan Command - Layer 2

I am the lum-chan character command.

## My Task

1. Check if pomljs is installed, if not install it with `npm install`
2. Read the POML behavior file at `poml/commands/no-context/lum.poml` using `poml --file <filename>`
3. Parse the POML content to understand which lum-chan sub-agents to call
4. Use the Task tool to dynamically call the specified sub-agents (e.g., lum-greeter, lum-emotion)
5. Integrate and return the results from all called sub-agents

## Execution Process

I will first check if pomljs is available and install if needed.
Then I'll read the POML file using `poml --file poml/commands/no-context/lum.poml` to understand which sub-agents to call.
Finally, I'll execute them using the Task tool.
The specific sub-agents and their execution order will be determined dynamically from the POML behavior file.
