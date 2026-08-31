# Traffic generators and Snappi

PTF is ideal for functional packet examples. A traffic generator is used when
the contract depends on line rate, many flows, precise timing, congestion, or
convergence measurements.

## Components

```text
pytest
  -> Snappi fixtures and helpers
  -> OTG/Snappi API server
  -> traffic-generator chassis ports
  -> DUT or multi-DUT topology
  -> flow metrics, captures, and counters
```

The API server and chassis are distinct resources. Inventory identifies how to
reach them; link data maps chassis/card/port endpoints to DUT interfaces; the
testbed entry binds those resources to the selected DUTs.

## Test construction

Shared code under `tests/common/snappi_tests/` discovers ports, builds test
parameters, configures flows, starts traffic, waits for state, and reads
metrics. Feature tests under `tests/snappi_tests/` define the scenario: BGP
convergence, RDMA/PFC, ECN, packet trimming, scale, or other performance
behavior.

A typical test has four contracts:

1. topology and link mapping are correct;
2. DUT control-plane configuration is converged;
3. the traffic generator accepted and started the intended configuration;
4. measured loss, latency, rate, or convergence satisfies the threshold.

## Multi-DUT considerations

Do not flatten port identity to one integer. Preserve DUT hostname, DUT port,
traffic-generator location, and peer relationship. Shared helpers include
multi-DUT parameter support; use them so route and flow endpoints remain tied
to the correct device.

## Measurement discipline

- Clear counters and flow metrics before the measurement interval.
- Separate warm-up, steady-state, fault injection, and recovery windows.
- Record the offered rate and expected packet count, not only observed loss.
- Use polling with a deadline for protocol and flow state.
- Treat API success as configuration acceptance, not proof that traffic flowed.
- Capture the generated configuration and per-flow metrics as artifacts.

## Safe exercise

Without reserving chassis ports, choose one test and trace its fixtures to the
port-discovery helper, flow builder, start/stop operation, and metric assertion.
Draw one row per flow with source port, destination port, headers, rate, and
expected result. This reveals hidden topology assumptions before lab use.

Common failures include stale port ownership, speed/FEC mismatch, wrong link
CSV data, incompatible API-server versions, insufficient warm-up, and using a
threshold that ignores measurement resolution.

Deeper references: [Snappi tests README](https://github.com/sonic-net/sonic-mgmt/blob/master/tests/snappi_tests/README.md),
[testbed integration](https://github.com/sonic-net/sonic-mgmt/blob/master/docs/testbed/README.testbed.SnappiTests.md),
and [shared Snappi helpers](https://github.com/sonic-net/sonic-mgmt/tree/master/tests/common/snappi_tests).
