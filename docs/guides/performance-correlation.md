# Review logs and performance

Use execution logs to identify where a run stopped and what the application was
showing. When your project enables performance monitoring, review the available
measurements alongside the testcase result.

Use INFO for routine logs. Choose DEBUG when your support team needs more detail,
then return to routine logging after investigation. Detailed logs may contain
application information; follow your team's sharing policy.

When reporting an issue, include the testcase name, run identifier, failed step,
error message and relevant evidence. In parallel runs, keep each run's identifiers
and evidence together so different workers' results are not mixed.

A quick action does not imply a correct business outcome. Performance data supports
investigation; your explicit test checks decide whether the required result occurred.
