# SmartSwitch and DPU topologies

A SmartSwitch testbed separates network switching and accelerated service
processing across an NPU-side SONiC host and one or more DPUs. Tests may need
to coordinate both control planes and the dataplane between them.

## Roles

- The NPU host performs the switch role and exposes external network ports.
- A DPU runs SONiC services and a programmable pipeline for offloaded behavior.
- Neighbor VMs or containers provide surrounding T0/T2 roles.
- PTF injects and observes packets at topology endpoints.
- DASH tests program and validate DPU/offload objects.

Do not use “DUT” without stating whether an operation targets the NPU host, a
DPU, or the whole SmartSwitch system.

## Fixture model

The global framework can create `dpuhosts` when a DPU pattern is supplied.
SmartSwitch fixtures add DPU-specific setup and Log Analyzer expectations.
Tests under `tests/smartswitch/` cover platform and reload behavior; tests under
`tests/dash/` build and validate DASH configuration and traffic.

## Two planes, several boundaries

```text
pytest
  +-- management -> NPU SONiC
  +-- management -> DPU SONiC
  +-- API/gNMI ---> DASH configuration
  +-- PTF traffic -> NPU port -> DPU pipeline -> network endpoint
```

A healthy NPU does not prove DPU readiness. Check DPU management, services,
pipeline initialization, underlay reachability, and dataplane separately.

## Topology data

Topology files such as `topo_smartswitch-t1.yml` bind NPU, DPU, PTF, and
neighbor-facing indices. Current physical and vendor variants may use
different DPU counts or connection models, so verify the selected topology and
runtime facts before using a port map from another setup.

## Exercise

For one DASH test, build a table with columns: operation, target host, API or
command path, expected state, and packet observation point. Then inspect
cleanup and identify which configured DASH objects and DPU state it restores.

Common failures are selecting only the NPU inventory, stale DPU images,
pipeline/API version mismatch, missing underlay routes, wrong PTF endpoint,
and collecting logs from the NPU while the error occurred on the DPU.

Deeper references: [SmartSwitch virtual setup](https://github.com/sonic-net/sonic-mgmt/blob/master/docs/testbed/README.testbed.SmartSwitch.VsSetup.md),
[SmartSwitch tests](https://github.com/sonic-net/sonic-mgmt/tree/master/tests/smartswitch),
and [DASH tests](https://github.com/sonic-net/sonic-mgmt/tree/master/tests/dash).
