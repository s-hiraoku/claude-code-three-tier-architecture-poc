# Test Orchestrator Command

This orchestrator tests the `claude -p` mechanism for calling custom slash commands.

## My Task

1. Display start message
2. Call the test-target command using `claude -p "/test:test-target"`
3. Display the result from test-target
4. Display completion message

## Execution

Arguments: $ARGUMENTS

I'll test the `claude -p` mechanism by calling the test-target command:

```bash
claude -p "/test:test-target" "$ARGUMENTS"
```

The orchestrator will capture and display the results from the target command.