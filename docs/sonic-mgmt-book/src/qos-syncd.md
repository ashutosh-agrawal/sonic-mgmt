# QoS and syncd swap

QoS tests connect abstract behavior—lossless priorities, queue limits, PFC,
ECN, and watermarks—to platform-specific buffer and queue implementation.

## Parameter pipeline

```text
DUT ASIC/HWSKU + topology + port speed + cable length
        |
        v
QoS configuration and platform parameter files
        |
        v
pytest fixtures select ports and expected thresholds
        |
        v
remote sai_qos_tests on PTF
        |
        v
packet results + DUT counters + watermarks
```

`tests/qos/conftest.py` and the files under `tests/qos/files/` build the DUT and
test parameters. `tests/qos/test_qos_sai.py` launches cases such as PFC, XON,
headroom-pool, shared-resource, and queue tests through the PTF host.

Thresholds can depend on ASIC cell size, leak-out behavior, port mode, and
buffer profile. A value copied from another platform is not a portable
expectation.

## Why syncd is swapped

Some QoS/SAI tests need an RPC-capable syncd image so the PTF-side SAI test can
control or observe the ASIC through thrift/RPC. The session fixture controlled
by `--qos_swap_syncd` invokes the Docker helper to swap syncd and restores the
default image during teardown.

This changes a critical DUT container. Treat restoration as part of the test
contract. If setup fails halfway through, inspect the running syncd image and
service health before running unrelated tests.

## Debugging order

1. Confirm selected DUT, ASIC, ports, speed, and cable length.
2. Record the resolved QoS parameter profile.
3. Verify the expected syncd image and RPC service.
4. Check PTF port mapping and packet construction.
5. Compare transmitted packets with counters, queue occupancy, and watermarks.
6. Account for leak-out, cell rounding, and configured margins.
7. Verify default syncd and normal services after teardown.

## Exercise

Choose one `testQosSai*` case and follow the exact parameter name from its YAML
source through the pytest fixture into the `ptf_runner` argument dictionary.
For each threshold, write whether it is exact, rounded by cell size, or widened
by a margin.

Deeper references: [QoS tests](https://github.com/sonic-net/sonic-mgmt/tree/master/tests/qos),
[test_qos_sai.py](https://github.com/sonic-net/sonic-mgmt/blob/master/tests/qos/test_qos_sai.py),
and [swap_syncd.yml](https://github.com/sonic-net/sonic-mgmt/blob/master/ansible/swap_syncd.yml).
