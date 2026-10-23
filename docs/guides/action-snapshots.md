# Capture and review evidence

Capture is off by default. Your test project can enable action and failure evidence
independently. Choose formats based on what you need to review.

| Format | Use it for |
| --- | --- |
| Image | See the rendered page at the captured moment. |
| HTML | Share a captured page with support for investigation. |
| JSON | Share structured capture details with support or reporting tools. |

## Choose when to capture

| Normal action capture | Before-and-after capture | Result |
| --- | --- | --- |
| Off | Off | No action snapshots. |
| On | Off | Normal after-action capture. |
| Off | On | Before and after supported actions. |
| On | On | Before and after supported actions, without requesting the same phase twice. |

Failure capture remains independent. Existing action evidence is reused where it
represents the same failure event, avoiding duplicate captures. Distinct action
phases and different failed states can produce separate evidence.

## Project options

| Setting | Purpose |
| --- | --- |
| `ENABLE_EXECUTION_SNAPSHOT` | Normal execution action capture. |
| `ENABLE_EXECUTION_BEFORE_AFTER_SNAPSHOT` | Before-and-after capture. |
| `CAPTURE_EXECUTION_HTML_SNAPSHOT` | Include HTML action evidence. |
| `CAPTURE_EXECUTION_IMAGE_SNAPSHOT` | Include image action evidence. |
| `CAPTURE_EXECUTION_JSON_SNAPSHOT` | Include structured action evidence. |
| `ENABLE_FAILURE_SNAPSHOT` | Capture failed states. |
| `CAPTURE_FAILURE_HTML_SNAPSHOT` | Include HTML failure evidence. |
| `CAPTURE_FAILURE_IMAGE_SNAPSHOT` | Include image failure evidence. |
| `CAPTURE_FAILURE_JSON_SNAPSHOT` | Include structured failure evidence. |
| `EXECUTION_SNAPSHOT_PATH` | Project location for execution evidence. |

Use Y to enable and N to disable. Selecting a format alone does not enable capture.
Selecting no formats produces no snapshots. Changing capture settings does not
remove old evidence or recover earlier uncaptured events.

Review the failed step, observed page and expected outcome together. Treat evidence
as diagnostic information, not proof that the business requirement passed.
Use approved test data: captures may include application information.
