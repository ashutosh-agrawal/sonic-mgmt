# End-to-end annotated test

This chapter traces one current test:
`tests/arp/test_arpall.py::test_arp_unicast_reply`. It is a useful teaching
example because pytest prepares the DUT, launches a remote PTF test, then
checks DUT state.

## 1. Collection and selection

The module declares:

```python
pytestmark = [pytest.mark.topology("t1", "t2", "lrh", "urh", "m1", "c0")]
```

The topology plugin compares this declaration with the selected testbed. A
`t0` run is skipped before the function body. Conditional-mark rules can add
another skip or xfail based on DUT facts.

The `enum_frontend_asic_index` argument also triggers collection-time
parameterization. On a multi-ASIC DUT, pytest may produce one item per eligible
frontend ASIC.

## 2. Fixture graph

The test requests three inputs:

```text
test_arp_unicast_reply
  |
  +-- common_setup_teardown
  |     +-- duthosts
  |     +-- ptfhost
  |     +-- selected DUT and ASIC
  |
  +-- intfs_for_test
  |     +-- tbinfo
  |     +-- running config facts
  |     +-- extended minigraph facts
  |
  +-- enum_frontend_asic_index
```

`common_setup_teardown` chooses the DUT, obtains the router MAC, copies PTF
tests to `/root/ptftests`, and yields `(duthost, ptfhost, router_mac)`.

`intfs_for_test` obtains the selected ASIC's minigraph facts, selects two
eligible interfaces, and maps each DUT name through
`minigraph_ptf_indices`. On non-`t0` topologies it temporarily adds test IP
addresses. Its teardown removes those addresses and restores any port-channel
membership it changed.

## 3. The test call

The function gets the namespace-aware ASIC object:

```python
asichost = duthost.asic_instance(enum_frontend_asic_index)
```

It clears the ARP cache, then passes the router MAC and selected PTF index to:

```python
ptf_runner(
    ptfhost,
    "ptftests",
    "arptest.VerifyUnicastARPReply",
    "/root/ptftests",
    params=params,
    log_file=log_file,
    is_python3=True,
)
```

This crosses an execution boundary. Pytest is still running on the test
runner, while `arptest.VerifyUnicastARPReply` executes in the PTF environment.
The integer `port` identifies a PTF interface, not a DUT front-panel name.

## 4. Dataplane and state assertion

The PTF test emits an ARP request through the mapped interface and verifies the
DUT's unicast reply. Control returns to pytest only after the remote test
finishes.

Pytest then reads the ASIC-specific ARP table:

```python
switch_arptable = asichost.switch_arptable()["ansible_facts"]
```

The final assertions verify both the learned MAC and the DUT interface. The
test therefore checks two related contracts: dataplane reply behavior and
control-plane learning state.

## 5. Teardown and post-processing

Pytest unwinds fixtures in reverse order. `intfs_for_test` restores interface
configuration. `common_setup_teardown` uses a `finally` block to reload the
saved DUT configuration even when setup or the PTF call raises an exception.
After fixture teardown, Log Analyzer and post-test sanity may still fail the
module.

## Read the artifacts in order

1. Pytest report: was the failure in setup, call, or teardown?
2. Remote PTF log: was a packet sent, received, malformed, or timed out?
3. DUT facts and commands: was the selected ASIC/interface correct?
4. Log Analyzer: did the DUT emit an unexpected error?
5. Post-sanity output: did cleanup restore the testbed?

## Reproducible reading exercise

Without running the test, locate each fixture definition, draw its dependency
graph, and write down where every value originates. Then compare the selected
DUT interface with `minigraph_ptf_indices`. This exercise catches the most
common review mistake: treating fixture inputs as unexplained constants.

Deeper references: [test_arpall.py](https://github.com/sonic-net/sonic-mgmt/blob/master/tests/arp/test_arpall.py),
[ARP conftest](https://github.com/sonic-net/sonic-mgmt/blob/master/tests/arp/conftest.py),
and [ptf_runner.py](https://github.com/sonic-net/sonic-mgmt/blob/master/tests/ptf_runner.py).
