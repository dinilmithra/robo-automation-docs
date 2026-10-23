# Browser and waiting options

Configure these settings in your test project's supported environment configuration
before starting execution. Ask your project owner when you need help applying them.

| Setting | How to use it |
| --- | --- |
| `BROWSER` | Choose chromium, firefox or webkit; default chromium. Install that browser first. |
| `HEAD_LESS` | N shows a browser; Y runs without a visible window. Default N. |
| `WAIT_TIME` | Set the default wait in seconds; default 90. |

Use a visible browser to check a new workflow. A headless browser still needs a
suitable page size. Ask your project owner to set a wide enough layout for controls
that only appear in a full-width window.

A timeout is the permitted wait for a condition, not a fixed pause before each action.
Check unexpected pages and missing labels before raising it. Restart execution after
changing settings; an already-running browser keeps its existing configuration.

Recording in RoboTest Studio remains visible regardless of the execution headless setting.
