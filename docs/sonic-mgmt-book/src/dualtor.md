# Dual-ToR and mux simulation

Dual-ToR tests add a second DUT and a stateful server-facing path. A packet's
expected path depends on which ToR is active for that server link.

## Topology families

- `dualtor` models active-standby smart-cable behavior. OVS flows represent the
  cable dataplane and `mux_simulator` controls those flows through HTTP.
- `dualtor-aa` models active-active NIC behavior. Network namespaces, OVS, and
  `nic_simulator` provide the gRPC-facing NIC control plane.
- `dualtor-mixed` contains both port types and therefore needs both simulators.

“Upper” and “lower” identify testbed positions, not permanent active and
standby roles. Mux state can change per interface during a test.

## Control and data paths

```text
pytest fixtures ---------> mux/nic simulator API ---------> OVS flow state
       |                                                     |
       +---- DUT mux state and routes                        |
       |                                                     v
       +---- PTF traffic ----------------------------> selected ToR path
```

A valid test checks both views: simulator state and DUT state must agree before
traffic assertions are meaningful.

## Current code structure

`tests/dualtor/conftest.py` prepares common fixtures and responders.
`tests/common/dualtor/` contains simulator clients, state toggles, dataplane
helpers, failure injection, tunnel checks, and traffic utilities. Individual
tests under `tests/dualtor/` should express a scenario using those shared
operations.

Setup also depends on per-testbed simulator ports in
`ansible/group_vars/all/mux_simulator_http_port_map.yml` and
`nic_simulator_grpc_port_map.yml`.

## A reliable test sequence

1. Select both ToRs and one or more mux interfaces.
2. Read initial simulator and DUT mux state.
3. Establish responders and baseline forwarding.
4. Inject one failure or toggle.
5. Wait for state convergence; do not rely on a fixed sleep alone.
6. Verify downstream, upstream, and tunnel behavior as applicable.
7. Restore the original state and prove convergence again.

## Common failures

| Symptom | First check |
|---|---|
| state API unreachable | simulator port map and service |
| upper/lower disagree | testbed ordering and fixture selection |
| control state changed but traffic did not | OVS flows and PTF mapping |
| intermittent loss | convergence wait and stale packets |
| later tests fail | mux state or responder not restored |

## Exercise

Trace one toggle fixture into `mux_simulator_control.py`. Record the simulator
request, expected DUT state, OVS effect, and teardown action. Then inspect one
test and identify which of those layers it actually asserts.

Deeper references: [Dualtor Testbed Setup](https://github.com/sonic-net/sonic-mgmt/blob/master/docs/testbed/README.testbed.DualtorSetup.md)
and [dual-ToR helpers](https://github.com/sonic-net/sonic-mgmt/tree/master/tests/common/dualtor).
