---
name: test-bash-embed
description: Test bash command embedding in slash commands
tools: [Bash, Read]
---

# Test Bash Command Embedding

This is a test to see if we can embed bash commands in slash commands.

## Current time test:
!date

## Directory listing test:
!ls -la

## Claude version test:
!claude --version

## Try calling another slash command:
!echo "Attempting to call slash command..."
!claude -p --session-id "$(uuidgen)" "/help" 2>&1 || echo "Failed to call slash command"