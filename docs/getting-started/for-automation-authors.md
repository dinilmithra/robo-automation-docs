# Run a test

1. Open the terminal in your prepared test project.
2. Confirm application access, approved test data and required entry steps.
3. Choose the browser mode, wait time and evidence settings before execution.
4. Run the selected testcase using your project's test command.
5. Review the outcome and any available evidence.

For a project using pytest, a selected file can be run with:

```shell
poetry run pytest tests/test_example.py -v -s
```

Replace the example filename with an existing testcase in your project.
A new browser session does not inherit a recording session's sign-in.
Your project must provide any required authentication or recorded entry steps.

See [browser options](../browser-visibility.md), [evidence](../guides/action-snapshots.md)
and [troubleshooting](error-handling.md).
