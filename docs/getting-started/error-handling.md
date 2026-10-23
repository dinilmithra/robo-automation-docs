# Troubleshooting a run

| What happened | Next step |
| --- | --- |
| Browser is not visible | Set HEAD_LESS to N in your project's execution configuration. |
| Browser cannot start | Confirm the selected browser is installed in the project environment. |
| Test stops at warning or sign-in | Complete the required entry flow for the new session. |
| Action times out | Inspect the page, label and permissions; increase WAIT_TIME only for slowness. |
| Menu is hidden | Check the execution window or viewport size. |
| No snapshots | Check capture mode and enabled formats before rerunning. |
| Logs are too limited | Ask for DEBUG logging and collect the relevant run details. |
| Different parallel results | Ask your project owner to check data isolation and worker credentials. |

A setup or cleanup problem can be reported as an error; a failed expected-result
check is a test failure. Read the error details before deciding whether to retry.
Confirm whether an earlier action changed business data before rerunning a test.
