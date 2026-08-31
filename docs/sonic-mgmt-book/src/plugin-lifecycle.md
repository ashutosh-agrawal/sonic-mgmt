# Plugin lifecycle

Plugins explain why behavior appears before and after a short test function.
The useful question is not only “what does this plugin do?” but “at which
pytest phase can it change the outcome?”

## Timeline

| Phase | Important sonic-mgmt behavior |
|---|---|
| command parsing | plugins register repository-specific options |
| configuration | custom markers are registered |
| collection | test modules become items; parameters are generated |
| collection modification | topology and conditional skip/xfail rules are applied |
| setup | fixtures create hosts, PTF agents, log markers, and sanity baselines |
| call | the test body runs |
| teardown | fixtures restore state and analyze logs |
| session finish | aggregate sanity/reporting state can affect the exit status |

## Custom markers

`tests/common/plugins/custom_markers/__init__.py` registers topology, feature,
ASIC, connection-type, device-type, and completeness markers. Its
`pytest_collection_modifyitems` hook checks topology compatibility. Its
`pytest_runtest_setup` hook enforces other marker constraints.

A topology marker is declarative compatibility. It neither creates a topology
nor proves all feature prerequisites are satisfied.

## Conditional marks

`conditional_mark` loads condition files, gathers basic DUT facts, matches
rules against collected node IDs, evaluates expressions, and adds `skip` or
`xfail` marks. Its collection-modification hook runs late so earlier topology
selection can avoid expensive DUT fact loading when every item is already
skipped.

When a test is unexpectedly skipped, record the exact node ID and search all
conditional-mark YAML files for the matching prefix before editing the test.

## Sanity checks

The module-scoped autouse `sanity_check` fixture wraps test execution.
Pre-checks can validate services, interfaces, BGP, processes, and other
baseline state. Post-checks detect damage left by the test. Recovery may run
when enabled.

In parallel execution, leader/follower coordination prevents every worker from
performing conflicting full-testbed recovery. A sanity failure is therefore
not equivalent to an assertion failure in the test body.

## Log Analyzer

The autouse `loganalyzer` fixture places markers in DUT logs before the test
and analyzes the bounded interval afterward. Expected and ignored regexes can
be extended by tests, but broad ignore patterns can hide real regressions.

`--disable_loganalyzer` is a debugging control, not a normal way to make a test
pass. Determine whether the log is caused by the test, stale environmental
noise, or an incomplete expectation rule.

## PTF adapter

The module-scoped `ptfadapter` fixture creates or connects to PTF NN agents,
constructs device-socket mappings, and returns a `PtfTestAdapter`. Tests then
send and verify packets while pytest keeps control-plane orchestration local.

Remote `ptf_runner` tests are a separate execution style. Both use PTF, but
their process boundary, logs, and failure reporting differ.

## Debugging exercise

Run `pytest --collect-only` for one node ID with normal verbosity. Then answer:

1. Which plugin registered each custom option?
2. Which collection hook could skip the item?
3. Which autouse fixtures run despite not appearing in the function signature?
4. Which failures can occur after the call phase reports success?

Deeper references: [custom markers](https://github.com/sonic-net/sonic-mgmt/tree/master/tests/common/plugins/custom_markers),
[conditional marks](https://github.com/sonic-net/sonic-mgmt/tree/master/tests/common/plugins/conditional_mark),
[sanity checks](https://github.com/sonic-net/sonic-mgmt/tree/master/tests/common/plugins/sanity_check),
[Log Analyzer](https://github.com/sonic-net/sonic-mgmt/tree/master/tests/common/plugins/loganalyzer),
and [PTF adapter](https://github.com/sonic-net/sonic-mgmt/tree/master/tests/common/plugins/ptfadapter).
