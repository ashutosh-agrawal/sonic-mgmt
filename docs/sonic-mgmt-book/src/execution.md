# The pytest execution lifecycle

Think of a test run as a pipeline with clear failure boundaries.

```text
run_tests.sh
    |
    v
pytest startup and plugin registration
    |
    v
collection -> topology and conditional selection
    |
    v
session fixtures -> host objects -> sanity checks
    |
    v
module/class fixtures -> test function -> teardown
    |
    v
log analysis, post-checks, artifacts, reports
```

## Command construction

`tests/run_tests.sh` is the supported wrapper. It translates options into
pytest arguments, prepares logging and results, and can execute suites or node
IDs. Direct pytest invocation is useful during development.

The essential inputs identify the test, inventory, DUT host pattern, testbed
name, testbed file, and environment-specific reporting settings.

## Plugin registration

`tests/conftest.py` registers global plugins for the PTF adapter, Ansible
fixtures, sanity checks, Log Analyzer, markers, conditional marks, monitoring,
and reporting. Feature directories can add local `conftest.py` files.

This is why a short test function can trigger substantial setup.

## Collection and selection

Pytest imports modules and builds test items. Plugins apply topology markers,
conditional skip/xfail rules, completeness rules, and parameterization. A
collected test is not necessarily an executed test.

When investigating a skip, inspect markers and selection plugins before the
test body.

## Session fixtures and sanity

Global fixtures parse the testbed, resolve DUTs, and construct host objects.
Session scope means a failure here can prevent every test from starting.
Pre-test sanity can validate services, interfaces, BGP, processes, and other
baseline state.

## Setup, call, and teardown

Pytest resolves fixtures from broadest to narrowest scope. A `yield` fixture
performs setup before `yield` and cleanup afterward, even when the test fails.
Autouse fixtures and plugins can add behavior not visible in the function
signature.

Always classify a failure as `setup`, `call`, or `teardown` first.

## Cross-device work

During a test the runner may execute DUT commands, reconfigure neighbors,
control fanout or power ports, launch a PTF test, inject packets through
`ptfadapter`, or poll until asynchronous state converges.

## Post-processing

Log Analyzer checks unexpected DUT messages, post-test sanity detects damaged
state, and plugins write JUnit, Allure, or other artifacts. A test whose
assertions passed can still fail here.

## Failure map

| Symptom | First area to inspect |
|---|---|
| unknown CLI option | test path ordering, local `conftest.py`, plugin loading |
| test skipped | topology marker, conditional marks, prerequisites |
| host fixture failed | testbed, inventory, host pattern, credentials/connectivity |
| setup error | requested fixtures and dependencies |
| assertion/packet failure | test logic, DUT state, port mapping, dataplane |
| teardown error | cleanup fixture and partial setup |
| Log Analyzer failure | DUT logs and ignore rules |
| post-sanity failure | state left damaged by test or environment |

