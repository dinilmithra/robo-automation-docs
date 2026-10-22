# Runtime configuration

`robo-automation` resolves runtime configuration from typed configuration objects. Consumers can normally configure the framework with environment variables; projects that need programmatic policy can override the public pytest fixtures.

Configuration precedence is:

1. consumer fixture override;
2. consumer environment variable;
3. immutable `robo-automation` default.

Boolean flags accept `1`, `true`, `yes`, `y`, or `on` (case-insensitive) as true. Any other non-empty value is false. Examples below use `Y` and `N` for readability.

## Browser and timeout flags

| Flag | Default | Usage |
| --- | --- | --- |
| `BROWSER` | `chromium` | Session browser: `chromium`, `firefox`, or `webkit`. |
| `HEAD_LESS` | `Y` | Run the session browser without a visible window. Set `N` for interactive local debugging. |
| `WAIT_TIME` | `90` | Default framework timeout in **seconds**. The resolved value is clamped to at least 1 second. |
| `ENABLE_BROWSER_LAUNCH_DELAY` | `N` | Enable process-safe launch pacing for parallel workers. |
| `BROWSER_LAUNCH_INTERVAL_SECONDS` | `0` | Minimum seconds between browser launches when launch pacing is enabled. `0` disables the delay. |
| `CACHE_PATH` | `artifacts/runtime/cache` | Directory used for the launch-pacing lock and timestamp files. Relative paths resolve from the pytest root. |

Example:

```ini
BROWSER=chromium
HEAD_LESS=Y
WAIT_TIME=90
ENABLE_BROWSER_LAUNCH_DELAY=Y
BROWSER_LAUNCH_INTERVAL_SECONDS=2
CACHE_PATH=artifacts/runtime/cache
```

`WAIT_TIME` is also the default used by semantic component actions unless a component supplies its own positive timeout. State queries such as Appian `is_enabled()` can define their own immediate/waiting semantics and should be documented by the higher-level component package.

## Logging flags

| Flag | Default | Usage |
| --- | --- | --- |
| `PYTEST_LOG_LEVEL` | `INFO` | Framework/file logging level. |
| `PYTEST_LOG_CLI_LEVEL` | unset | Optional console logging level. |
| `PYTEST_LOG_CLI_FORMAT` | unset | Optional pytest console log format. |
| `PYTEST_LOG_CLI_DATE_FORMAT` | unset | Optional pytest console date format. |
| `PYTEST_PARALLEL_LOG_TO_FILE` | `N` | Enable the correlated execution log file. |
| `PYTEST_PARALLEL_LOG_PATH` | `artifacts/logs/worker` | Execution-log directory. |
| `TESTCASE_LOG_ENABLED` | `Y` | Enable per-testcase log files. |
| `TESTCASE_LOG_PATH` | `artifacts/logs/testcases` | Per-testcase log directory. |

Example:

```ini
PYTEST_LOG_LEVEL=INFO
PYTEST_PARALLEL_LOG_TO_FILE=Y
TESTCASE_LOG_ENABLED=Y
```

## Artifact and snapshot flags

| Flag | Default | Usage |
| --- | --- | --- |
| `ARTIFACTS_ROOT` | `artifacts` | Root artifact directory. |
| `EXECUTION_SNAPSHOT_PATH` | `${ARTIFACTS_ROOT}/snapshots` | Folder where screenshots are saved. |
| `CAPTURE_ACTION_SNAPSHOTS` | `N` | Enable normal after-only evidence for instrumented browser actions. |
| `ENABLE_FAILURE_SNAPSHOT` | `N` | Capture failure evidence when the failure is not already represented by action evidence. |
| `CAPTURE_FAILURE_HTML_SNAPSHOT` | `Y` | HTML for enabled failure evidence |
| `CAPTURE_FAILURE_IMAGE_SNAPSHOT` | `Y` | PNGs for enabled failure evidence |
| `CAPTURE_FAILURE_JSON_SNAPSHOT` | `Y` | JSON metadata for enabled failure evidence |
| `CAPTURE_EXECUTION_HTML_SNAPSHOT` | `Y` | Include live HTML DOM in enabled execution action snapshots. |
| `CAPTURE_EXECUTION_IMAGE_SNAPSHOT` | `Y` | PNG images for enabled execution action evidence |
| `CAPTURE_EXECUTION_JSON_SNAPSHOT` | `Y` | JSON metadata for enabled execution action evidence |
| `SNAPSHOT_FULL_PAGE` | `N` | Capture the full page instead of only the visible area. |

Paths support recursive `${VAR}` references. This makes a shared layout possible without hardcoding an absolute path:

```ini
ARTIFACTS_ROOT=../artifacts
DOWNLOAD_PATH=${ARTIFACTS_ROOT}/downloads
EXECUTION_SNAPSHOT_PATH=${ARTIFACTS_ROOT}/snapshots
CAPTURE_ACTION_SNAPSHOTS=N
ENABLE_FAILURE_SNAPSHOT=Y
CAPTURE_FAILURE_HTML_SNAPSHOT=Y
CAPTURE_FAILURE_IMAGE_SNAPSHOT=Y
CAPTURE_FAILURE_JSON_SNAPSHOT=Y
SNAPSHOT_FULL_PAGE=N
```

Referenced variables must exist and be non-empty. Unresolved, empty, cyclic, or excessively recursive references raise a configuration error instead of silently producing an incorrect path.

See [Action Snapshots](action-snapshots.md) for lifecycle, deduplication, and evidence layout.

## Performance flags

Performance monitoring is enabled by default and writes worker-aware telemetry beneath `PERF_MONITOR_PATH`.

| Flag | Default | Usage |
| --- | --- | --- |
| `PERF_MONITOR_ENABLED` | `Y` | Enable performance monitoring. |
| `PERF_MONITOR_ACTIONS` | `Y` | Measure supported browser actions. |
| `PERF_MONITOR_FRAMEWORK` | `Y` | Measure framework lifecycle operations. |
| `PERF_MONITOR_CONSOLE` | `N` | Emit qualifying timing records to the console. |
| `PERF_MONITOR_PATH` | `artifacts/logs/performance` | Performance output directory. |
| `PERF_MONITOR_INTERVAL` | `1.0` | Sampling interval in seconds; minimum `0.1`. |
| `PERF_MONITOR_FLUSH_EVERY` | `20` | Flush the JSONL stream after this many pending writes; minimum `1`. |
| `PERF_MONITOR_THRESHOLD_MS` | `500.0` | Minimum duration in milliseconds for threshold classification; minimum `0`. |
| `PERF_MONITOR_SLOW_MS` | `2000.0` | Duration in milliseconds classified as slow; minimum `0`. |
| `PERF_MONITOR_SUMMARY_LIMIT` | `50` | Maximum slowest-operation entries retained in summaries; minimum `1`. |
| `PERF_MONITOR_RUN_ID` | `run-unknown` | Correlation identifier for a performance run. Parallel controller startup creates a run id when one is not already supplied. |

Example:

```ini
PERF_MONITOR_ENABLED=Y
PERF_MONITOR_ACTIONS=Y
PERF_MONITOR_FRAMEWORK=Y
PERF_MONITOR_CONSOLE=N
PERF_MONITOR_THRESHOLD_MS=500
PERF_MONITOR_SLOW_MS=2000
```

See [Performance and Correlation](performance-correlation.md) for output and correlation behavior.

## Browser diagnostics configuration

The typed configuration model currently defines these settings:

`ENABLE_BROWSER_DIAGNOSTICS`, `BROWSER_DIAGNOSTICS_CONSOLE`, `BROWSER_DIAGNOSTICS_PAGE_ERRORS`, `BROWSER_DIAGNOSTICS_NETWORK`, `BROWSER_DIAGNOSTICS_NAVIGATION`, and `BROWSER_DIAGNOSTICS_PATH`.

They are exposed through `DiagnosticsConfig` / `robo_diagnostics_config`, but the current runtime does **not** install a browser-diagnostics capture service from these values. Treat them as reserved/configuration-only for now; setting them does not by itself produce diagnostic artifacts. This distinction prevents configuration availability from being mistaken for implemented capture behavior.

## Public configuration fixtures

The pytest plugin exposes these session-scoped fixtures:

- `robo_runtime_config`
- `robo_logging_config`
- `robo_diagnostics_config`
- `robo_performance_config`
- `robo_snapshot_config`
- `robo_artifact_paths`
- `wait_time`

For example, an environment can set `PYTEST_LOG_LEVEL=DEBUG` without writing pytest code. A consumer that needs a programmatic override can replace the relevant fixture:

```python
import pytest
from robo_automation import LoggingConfig


@pytest.fixture(scope="session")
def robo_logging_config() -> LoggingConfig:
    return LoggingConfig(level="DEBUG", parallel_file_enabled=True)
```

The fixture value is consumed by the corresponding runtime service and takes precedence over the environment-derived default.

## Configuration ownership

`robo_automation.config` owns translation of runtime environment variables into typed configuration objects. Runtime services consume those resolved objects. Early pytest bootstrap resolves environment/default configuration before normal fixtures exist; fixture resolution then reapplies the final consumer configuration for the session.

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

### Snapshot environment naming

Use full mode and format names consistently. The following names replace the
previous abbreviated names; the previous names remain compatibility aliases.
An explicitly supplied new name takes precedence, including `N`.

| Previous name | Canonical name |
| --- | --- |
| `ENABLE_RECORDING_AFTER_AND_BEFORE_SNAPSHOT` | `ENABLE_RECORDING_BEFORE_AFTER_SNAPSHOT` |
| `ENABLE_EXECUTION_AFTER_AND_BEFORE_SNAPSHOT` | `ENABLE_EXECUTION_BEFORE_AFTER_SNAPSHOT` |
| `CAPTURE_REC_HTML_SNAPSHOT` | `CAPTURE_RECORDING_HTML_SNAPSHOT` |
| `CAPTURE_REC_IMG_SNAPSHOT` | `CAPTURE_RECORDING_IMAGE_SNAPSHOT` |
| `CAPTURE_REC_JSON_SNAPSHOT` | `CAPTURE_RECORDING_JSON_SNAPSHOT` |
| `CAPTURE_EXE_HTML_SNAPSHOT` | `CAPTURE_EXECUTION_HTML_SNAPSHOT` |
| `CAPTURE_EXE_IMG_SNAPSHOT` | `CAPTURE_EXECUTION_IMAGE_SNAPSHOT` |
| `CAPTURE_EXE_JSON_SNAPSHOT` | `CAPTURE_EXECUTION_JSON_SNAPSHOT` |

The normal flags, failure flags and snapshot path names remain unchanged.
Capture modes, format defaults, evidence paths and duplicate prevention are unchanged.
New environment examples and CI parameters use the canonical names.
