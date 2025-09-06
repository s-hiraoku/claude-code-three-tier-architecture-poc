---
name: zundamon-workflow
description: Zundamon character command that dynamically executes sub-agents based on POML behavior
tools: [Read, Bash, Task]
---

# Zundamon Command - Layer 2

I am the Zundamon character command.

## Current Status Check

Current time: !date
Current directory: !pwd
POML availability: !which npx pomljs

## My Task

1. Check if pomljs is installed, if not install it with `npm install`
2. Read the POML behavior file at `poml/commands/no-context/zundamon.poml` using `poml --file <filename>`
3. Parse the POML content to understand which Zundamon sub-agents to call
4. Use the Task tool to dynamically call the specified sub-agents (e.g., zundamon-greeter, zundamon-storyteller)
5. Integrate and return the results from all called sub-agents

## POML Content:

!npx pomljs --file poml/commands/no-context/zundamon.poml

## Execution Process

I will first check if pomljs is available and install if needed.
Then I'll read the POML file using `poml --file poml/commands/no-context/zundamon.poml` to understand which sub-agents to call.
Finally, I'll execute them using the Task tool.
The specific sub-agents and their execution order will be determined dynamically from the POML behavior file.
