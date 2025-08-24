# Claude Code Three Tier Architecture POC Test Commands

## Overview

A dummy command system for testing the three-tier command system of Claude Code Three Tier Architecture POC.

## Structure

### Layer 1: Orchestrator

- `orchestrator` - Main orchestrator command
- `orchestrator.poml` - Orchestrator behavior definition

### Layer 2: Custom Commands

- `command-zundamon` - Zundamon character command
- `command-lum-chan` - Lum-chan character command

### Layer 3: POML Definitions

- `zundamon.poml` - Zundamon's speech patterns and personality definition
- `lum.poml` - Lum-chan's speech patterns and personality definition

## Execution Method

Execute as custom slash command in Claude Code:

```bash
/orchestrator
```

## Expected Behavior

1. `orchestrator` is executed
2. `orchestrator.poml` is loaded
3. `command-zundamon` is called, and Zundamon speaks
4. `command-lum-chan` is called, and Lum-chan speaks

By displaying both characters' speeches, you can verify the three-tier structure operation.