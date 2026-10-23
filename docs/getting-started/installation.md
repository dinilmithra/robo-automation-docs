# Install RoboAutomation

RoboAutomation provides browser execution and evidence features for test projects.
Appian test authors normally receive these features through RoboAppian.

For a project using RoboAutomation directly, install the published package with Poetry
and install the required browser:

```shell
poetry add robo-automation
poetry run playwright install chromium
```

Use a supported Python version and your team's approved dependency versions.
Package installation does not create your business tests or configure application access.

Continue with [run a test](for-automation-authors.md).
