# Browser visibility and fixture override recommendation

## Available configuration

`robo-automation` defaults to a visible browser: `HEAD_LESS=N`.
Set `HEAD_LESS=Y` for headless pytest execution. Browser-launch logs show the
effective `headless=False` or `headless=True` value.

In `robo-recorder`, use the project `.env` for routine configuration:

```dotenv
HEAD_LESS=N
WAIT_TIME=90
```

Its root `conftest.py` loads the file relative to that file, before browser
resources are created. Other consumers can instead opt into loading with
`robo_env_file = .env` in `pytest.ini`, relative to the pytest root. Use one
approach; the library does not automatically discover parent environment files. Missing explicitly configured files cause a configuration
error. Entries support KEY=VALUE, optional surrounding quotes, and full-line
comments. Values are literal; shell execution and inline comments are unsupported.

Current precedence, highest first:

1. Existing process environment, including PowerShell overrides.
2. Project environment file, when explicitly configured.
3. Library default: visible browser.

For a temporary override in PowerShell:

```powershell
$env:HEAD_LESS = "Y"
poetry run pytest .\tests\generated\test_vendor_date_01.py -v -s
Remove-Item Env:HEAD_LESS -ErrorAction SilentlyContinue
```

To use workspace library changes, run from `robo-recorder`:

```powershell
poetry add --editable ..\robo-automation
```

Restart VS Code after updating the dependency. The interactive recording/review
browser always opens visibly; `HEAD_LESS` controls pytest execution.

## Recommended future fixture override

**Status: proposed; a public `headless` fixture is not implemented.** The example
below is a design example and will not configure the current browser lifecycle.
Continue using the environment settings above until this feature is implemented.

The library should expose a session-scoped `headless` fixture that resolves the
existing environment setting. Browser creation must explicitly depend on that
fixture and pass its boolean value to browser launch. This supports both session
and per-test browser lifetimes without a scope mismatch.

Consumers needing execution-specific policy could then override the fixture in
their own `conftest.py`:

```python
# Proposed future usage only; requires library fixture support first.
import pytest

@pytest.fixture(scope="session")
def headless():
    return False  # Visible browser; True would select headless execution.
```

Recommended precedence after implementation:

1. Consumer `headless` fixture override.
2. Existing process environment.
3. Explicitly configured project environment file.
4. Library default: `False` (`HEAD_LESS=N`).

Use `.env` for normal project settings; use a fixture override for consumer-specific
execution policy. Changing environment values inside a test is too late once the
browser has launched. An autouse fixture alone does not guarantee ordering relative
to browser creation; use an explicit dependency instead. Keep fixture scope compatible
with the configured browser lifetime.

The fixture should affect only launch visibility. Preserve fresh browser/context
behavior, worker credential isolation, resource cleanup, and existing timeout policy.
The existing root `robo-recorder/conftest.py` loads environment settings and configures a resizable viewport;
this recommendation adds no `headless` fixture override.


## Maximize the execution browser

```dotenv
HEAD_LESS=N
BROWSER_MAXIMIZED=Y
```

`BROWSER_MAXIMIZED=Y` launches headed Chromium maximized and lets the application
viewport follow the window. `N` uses the normal launch window and Playwright's
normal viewport. The library default is `N`; robo-recorder's project `.env` enables
`Y`. This controls pytest execution, including runs started from Testcase Studio.
Headless execution and non-Chromium browsers ignore this option. A consumer's
explicit `viewport` or `no_viewport` context option takes precedence for page sizing.
Maximized mode retains browser toolbars; it does not enter F11/kiosk mode.

For a temporary PowerShell override:

```powershell
$env:BROWSER_MAXIMIZED = "N"
poetry run pytest .\tests\generated -v -s
Remove-Item Env:BROWSER_MAXIMIZED
```

Restart Testcase Studio after changing project environment values so newly started
execution processes receive the current project configuration. Recording/review
window behavior is unchanged.


For responsive navigation such as **SETTINGS**, headless execution needs a
large viewport even though there is no visible window. robo-recorder sets:

```dotenv
BROWSER_VIEWPORT_WIDTH=1920
BROWSER_VIEWPORT_HEIGHT=1080
```

Set both dimensions to positive integers. They apply when headed Chromium is not
being maximized, including headless runs. A maximized headed Chromium window uses
the actual available desktop size instead. Explicit consumer context viewport
options still take precedence. Without dimension flags, the library keeps the
normal Playwright viewport. Verify the menu at your selected size; page zoom and
application layout can also affect responsive navigation.
