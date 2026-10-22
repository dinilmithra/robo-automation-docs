# Action and Failure Snapshots

Snapshots provide visual evidence of what happened while an automation test was running. They are useful when reviewing test results or investigating a failure.

Configure snapshot capture using:

```text
CAPTURE_ACTION_SNAPSHOTS=N
ENABLE_FAILURE_SNAPSHOT=Y
CAPTURE_FAILURE_HTML_SNAPSHOT=Y
CAPTURE_FAILURE_IMAGE_SNAPSHOT=Y
CAPTURE_FAILURE_JSON_SNAPSHOT=Y
CAPTURE_EXECUTION_HTML_SNAPSHOT=Y
CAPTURE_EXECUTION_IMAGE_SNAPSHOT=Y
CAPTURE_EXECUTION_JSON_SNAPSHOT=Y
SNAPSHOT_FULL_PAGE=N
EXECUTION_SNAPSHOT_PATH=${ARTIFACTS_ROOT}/snapshots
```

| Setting | Purpose |
| --- | --- |
| `CAPTURE_ACTION_SNAPSHOTS` | Enable normal after-only snapshots for supported automation actions. |
| `ENABLE_FAILURE_SNAPSHOT` | Capture screenshot evidence when a test fails. |
| `CAPTURE_FAILURE_HTML_SNAPSHOT` | HTML for enabled failure evidence |
| `CAPTURE_FAILURE_IMAGE_SNAPSHOT` | PNGs for enabled failure evidence |
| `CAPTURE_FAILURE_JSON_SNAPSHOT` | JSON metadata for enabled failure evidence |
| `CAPTURE_EXECUTION_HTML_SNAPSHOT` | Include live HTML DOM; default `Y`. Set `N` to skip HTML files. |
| `SNAPSHOT_FULL_PAGE` | Capture the full page instead of only the visible area. |
| `EXECUTION_SNAPSHOT_PATH` | Folder where snapshot evidence is stored. The standard project setting is `${ARTIFACTS_ROOT}/snapshots`. |

## Recommended configuration

For normal automation execution, failure snapshots provide useful evidence without capturing screenshots for every action:

```text
CAPTURE_ACTION_SNAPSHOTS=N
ENABLE_FAILURE_SNAPSHOT=Y
CAPTURE_FAILURE_HTML_SNAPSHOT=Y
CAPTURE_FAILURE_IMAGE_SNAPSHOT=Y
CAPTURE_FAILURE_JSON_SNAPSHOT=Y
CAPTURE_EXECUTION_HTML_SNAPSHOT=Y
CAPTURE_EXECUTION_IMAGE_SNAPSHOT=Y
CAPTURE_EXECUTION_JSON_SNAPSHOT=Y
SNAPSHOT_FULL_PAGE=N
EXECUTION_SNAPSHOT_PATH=${ARTIFACTS_ROOT}/snapshots
```

Enable action snapshots when additional evidence is useful while developing or troubleshooting a test:

```text
CAPTURE_ACTION_SNAPSHOTS=Y
```

## Snapshot files

Snapshot evidence is stored under `EXECUTION_SNAPSHOT_PATH` and organized by evidence type:

```text
${EXECUTION_SNAPSHOT_PATH}/
├── screenshots/
├── html/
└── metadata/
```

Action PNGs follow `CAPTURE_EXECUTION_IMAGE_SNAPSHOT`; failure PNGs follow
`CAPTURE_FAILURE_IMAGE_SNAPSHOT`.
HTML can separately be disabled using `CAPTURE_EXECUTION_HTML_SNAPSHOT=N`; JSON metadata
for action evidence follows `CAPTURE_EXECUTION_JSON_SNAPSHOT`. Failure JSON follows `CAPTURE_FAILURE_JSON_SNAPSHOT`. The
`screenshots/` directory is created only when images are enabled; existing PNG
files from earlier runs are not deleted by this setting.

`CAPTURE_EXECUTION_HTML_SNAPSHOT` defaults to `Y`. Disabling HTML keeps PNG images
and enabled JSON metadata. Existing artifacts are not deleted by format settings.

### HTML capture controls

Group recording and execution settings separately in the project `.env`:

```dotenv
# Recording snapshots
ENABLE_RECORDING_SNAPSHOT=Y
ENABLE_RECORDING_BEFORE_AFTER_SNAPSHOT=Y
CAPTURE_RECORDING_HTML_SNAPSHOT=Y
CAPTURE_RECORDING_IMAGE_SNAPSHOT=Y
CAPTURE_RECORDING_JSON_SNAPSHOT=Y
RECORDING_SNAPSHOT_PATH=artifacts/snapshots/recording

# Execution snapshots
ENABLE_EXECUTION_SNAPSHOT=Y
ENABLE_EXECUTION_BEFORE_AFTER_SNAPSHOT=Y
CAPTURE_EXECUTION_HTML_SNAPSHOT=Y
CAPTURE_EXECUTION_IMAGE_SNAPSHOT=Y
CAPTURE_EXECUTION_JSON_SNAPSHOT=Y
EXECUTION_SNAPSHOT_PATH=artifacts/snapshots/execution
```

Both HTML flags default to `Y` and require their mode's snapshot policy to be
enabled. Set `CAPTURE_RECORDING_HTML_SNAPSHOT=N` to omit recording HTML without stopping
the semantic recording. Image evidence and JSON metadata follow their own format flags. Set
`CAPTURE_EXECUTION_HTML_SNAPSHOT=N` to omit execution HTML while keeping PNG images
and enabled JSON metadata. Capture is disabled only when both normal and paired switches are `N`, or when all formats are `N`.

For existing consumers, `CAPTURE_SNAPSHOT_HTML` remains an execution fallback
when `CAPTURE_EXECUTION_HTML_SNAPSHOT` is unset. The new flag takes precedence.

### Image capture controls

| Flag | Library default | Effect |
| --- | --- | --- |
| `CAPTURE_RECORDING_IMAGE_SNAPSHOT` | `N` | Recording PNG observations when recording snapshots are enabled |
| `CAPTURE_RECORDING_JSON_SNAPSHOT` | `Y` | JSON metadata for enabled recording evidence |
| `CAPTURE_EXECUTION_IMAGE_SNAPSHOT` | `Y` | Execution action PNGs when action capture is enabled |
| `CAPTURE_EXECUTION_JSON_SNAPSHOT` | `Y` | JSON metadata for enabled execution action evidence |

The supplied recorder `.env` sets both image flags to `Y`. HTML flags remain
independent. Setting both normal and before/after switches to `N` disables capture for that mode.
Recording PNGs are stored in `RECORDING_SNAPSHOT_PATH/screenshots`; execution PNGs
are stored in `EXECUTION_SNAPSHOT_PATH/screenshots`. Existing files are not deleted.
When all three recording formats are disabled, no recording evidence is collected.
When HTML and PNG are disabled, enabled execution policies write metadata only if
`CAPTURE_EXECUTION_JSON_SNAPSHOT=Y`. With all three formats off, no action evidence files are written.

Recording screenshots mask password, username and one-time-code input fields and
the recorder panel. Other visible application content remains. Screenshots are
observations taken when the bridge processes the before/after event; the browser
may have advanced since the originating DOM event. They do not prove a business
outcome or guarantee the exact pre-click visual state. Image capture failures are
diagnostic and do not block semantic recording or enabled HTML capture.

### Normal and before/after snapshot modes

The normal and before/after switches are independent for recording and execution:

| Normal flag | Before/after flag | Capture mode |
| --- | --- | --- |
| `Y` | `N` | After only |
| `N` | `Y` | Before and after |
| `Y` | `Y` | Before and after, once per phase |
| `N` | `N` | No snapshots for that mode |

Normal recording uses `ENABLE_RECORDING_SNAPSHOT`; paired recording uses
`ENABLE_RECORDING_BEFORE_AFTER_SNAPSHOT`. Normal execution action capture uses
`ENABLE_EXECUTION_SNAPSHOT`; paired execution uses
`ENABLE_EXECUTION_BEFORE_AFTER_SNAPSHOT`. Both paired flags default to `N` when
unset; the supplied recorder `.env` sets them to `Y`. Explicit paired `Y` enables
both phases even if the corresponding normal switch is `N`. Neither flag
changes which HTML, PNG or JSON formats are enabled. Recording interactions and
test execution continue when evidence capture is off.

When `ENABLE_EXECUTION_SNAPSHOT` is unset, legacy `CAPTURE_ACTION_SNAPSHOTS`
selects normal after-only action capture. Failure capture remains independent
under `ENABLE_FAILURE_SNAPSHOT`, with its own HTML, PNG and JSON flags. Matching
failed-action after evidence is reused per format; only missing failure formats
are added to the same snapshot stem. Before evidence is a distinct observation.

| Failure setting | Default | Effect |
| --- | --- | --- |
| `ENABLE_FAILURE_SNAPSHOT` | `N` | Enable failure capture independently |
| `CAPTURE_FAILURE_HTML_SNAPSHOT` | `Y` | Include failure HTML |
| `CAPTURE_FAILURE_IMAGE_SNAPSHOT` | `Y` | Include failure PNGs |
| `CAPTURE_FAILURE_JSON_SNAPSHOT` | `Y` | Include failure JSON metadata |

Failure snapshots use their own master and formats. `ENABLE_EXECUTION_SNAPSHOT=N`
or disabling execution before/after capture does not disable failure evidence.
`ENABLE_FAILURE_SNAPSHOT=N` disables failure evidence regardless of failure format
flags. With the failure master on and all three formats off, no failure files are
written. Failure format flags do not disable normal action evidence. Existing
files are not deleted.

Failure files remain under `EXECUTION_SNAPSHOT_PATH`, separated into `html/`,
`screenshots/` and `metadata/`, grouped by process ID. There is no new failure
path variable. Without an open browser page (for example a collection failure),
only enabled failure JSON can be captured. Disabling failure JSON can prevent
report features that discover evidence through metadata from linking to failure
images or HTML. Report JSON settings and the recorder's `recording.json` are
unaffected by the failure JSON flag.

For the same failed action, execution after-evidence and failure evidence share
one snapshot stem. Failure capture reuses each successfully captured format and
writes only missing requested formats. With all requested formats present, it
writes nothing. Repeated callbacks and retries retain completed formats and retry
only missing formats. The before snapshot is a distinct observation and remains.

### Recording and execution JSON snapshots

`CAPTURE_RECORDING_JSON_SNAPSHOT` and `CAPTURE_EXECUTION_JSON_SNAPSHOT` default to `Y`.
They control snapshot JSON metadata independently of HTML and PNG formats and
require either normal or before/after capture for their mode. JSON-only capture is supported.
Setting all three formats to `N` writes no snapshots for that policy.

JSON snapshots contain observation/action metadata and evidence references; the
HTML DOM remains in HTML files. Recording JSON evidence is saved beneath
`RECORDING_SNAPSHOT_PATH/metadata`; execution action JSON evidence is saved beneath
`EXECUTION_SNAPSHOT_PATH/metadata`, grouped by process ID. Disabling these flags
does not disable or delete the semantic `recording.json`, generated Python tests,
report JSON, or failure snapshots. Failure metadata continues to use
`CAPTURE_FAILURE_JSON_SNAPSHOT`. Metadata-driven report links may be unavailable
when the corresponding snapshot JSON is disabled. Existing files are retained.
