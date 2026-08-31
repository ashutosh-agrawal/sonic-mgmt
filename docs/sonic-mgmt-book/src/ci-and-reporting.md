# CI and reporting

A CI result is the end of a pipeline, not a direct reflection of one Python
assertion.

## Result flow

```text
change or scheduled run
  -> pipeline and test-plan selection
  -> testbed allocation and health check
  -> one or more pytest invocations
  -> logs, JUnit XML, dumps, and platform artifacts
  -> aggregation and publication
  -> dashboards and failure triage
```

## Selection and orchestration

Files under `.azure-pipelines/` define repository checks, collection checks,
impacted-area selection, test-plan handling, testbed recovery, and execution
templates. The external CI system can partition suites across jobs or testbeds;
pytest plugins then coordinate operations that must be leader-only within a
parallel run.

A missing test in CI can therefore result from pipeline selection, suite
selection, pytest collection, topology filtering, or conditional marks. Check
those boundaries in that order.

## What `run_tests.sh` produces

`tests/run_tests.sh` builds common pytest options from the inventory, DUT,
testbed name, and testbed file. It creates separate log and JUnit XML outputs
for pre-test, test, and post-test phases. For some invocation modes it writes
per-test XML and log files.

The wrapper prints the final pytest command. Preserve that command: it is the
best local reproduction starting point, provided the same testbed and image
are available.

## Artifacts are layered evidence

| Artifact | Question it answers |
|---|---|
| pipeline log | what job, image, testbed, and command were selected? |
| pytest report | which item and phase failed? |
| JUnit XML | how was the result classified and timed? |
| test log | what did fixtures and the test do? |
| PTF log or capture | what happened on the dataplane? |
| DUT logs and dumps | what did SONiC services report? |
| sanity output | was baseline state healthy before and after? |

Do not diagnose from the dashboard label alone. For example, a test function
can pass while teardown, Log Analyzer, or post-sanity makes the JUnit case fail.

## Reporting pipeline

`test_reporting/junit_xml_parser.py` parses result XML. The reporting tools can
collect results, normalize records, and upload them to Kusto/Azure Data
Explorer using configured authentication. KQL files define tables and views
for reporting and analysis.

Uploading is a separate trust boundary. Credentials and cluster endpoints
belong in the supported environment or identity mechanism, never in test code,
logs, or committed configuration.

## Triage workflow

1. Record commit, image, testbed, topology, DUTs, and exact node ID.
2. Identify the failing phase and first causal error.
3. Compare sibling jobs to distinguish test-specific from testbed-wide failure.
4. Check pre-sanity and infrastructure before feature assertions.
5. Inspect teardown and post-sanity before calling the environment reusable.
6. Reproduce the smallest node ID with the printed pytest command.
7. Classify the owner: infrastructure, framework, test, feature, or reporting.

Deeper references: [run_tests.sh](https://github.com/sonic-net/sonic-mgmt/blob/master/tests/run_tests.sh),
[Azure pipeline definitions](https://github.com/sonic-net/sonic-mgmt/tree/master/.azure-pipelines),
and [test reporting](https://github.com/sonic-net/sonic-mgmt/blob/master/test_reporting/README.md).
