# Choose a learning path

## New to sonic-mgmt

Read the foundational chapters in order:

1. [Testbed architecture](architecture.md)
2. [Topology and configuration](topology-and-configuration.md)
3. [The pytest execution lifecycle](execution.md)
4. [Fixtures and device objects](fixtures-and-hosts.md)
5. [Packet tests and PTF](packet-tests.md)
6. [End-to-end annotated test](annotated-test.md)
7. [Plugin lifecycle](plugin-lifecycle.md)
8. [A guided code tour](code-tour.md)

Then run one small test in an already-deployed environment before learning how
to deploy a testbed. This keeps test behavior separate from lab provisioning.

## I need to run an existing test

Read [The pytest execution lifecycle](execution.md), then consult
`docs/tests/pytest.run.md` for the complete command reference. Begin with one
pytest node ID rather than a feature suite.

## I need to write or review a test

Read [Fixtures and device objects](fixtures-and-hosts.md) and [Packet tests and
PTF](packet-tests.md). Then inspect one small test in the same feature and its
nearest `conftest.py`.

## I need to create or deploy a testbed

Read [Topology and configuration](topology-and-configuration.md). Continue with
[Anatomy of a topology file](topology-file-anatomy.md), then choose the
[Hands-on virtual lab](virtual-lab.md) or [Physical lab bring-up and
operations](physical-lab.md).

## I work with chassis or specialized testbeds

Start with [Multi-DUT and multi-ASIC tests](multi-dut-multi-asic.md), then choose
the relevant track:

- [Dual-ToR and mux simulation](dualtor.md)
- [Traffic generators and Snappi](traffic-generators.md)
- [Reboot and upgrade testing](reboot-upgrade.md)
- [QoS and syncd swap](qos-syncd.md)
- [SAI and PTF testing](sai-ptf.md)
- [SmartSwitch and DPU topologies](smartswitch-dpu.md)
- [SPyTest architecture](spytest.md)

## I am debugging CI

Use the execution lifecycle to identify the failing layer before reading
feature code:

- topology/test selection;
- inventory or connectivity;
- pre-test sanity or global plugins;
- fixture setup;
- test body or PTF dataplane check;
- fixture teardown or post-test checks;
- result collection.

Then use [CI and reporting](ci-and-reporting.md) to map that layer to the
available artifacts.
