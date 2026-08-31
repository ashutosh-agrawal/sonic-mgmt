# Choose a learning path

The shortest useful route through `sonic-mgmt` depends on what you are trying
to change. Each path below ends with an observable checkpoint. Reading without
that checkpoint makes repository names familiar but does not prove that the
model is usable.

## Path A: understand the system

Use this path if the repository is new to you or if individual test files make
sense but their environment does not.

1. [Testbed architecture](architecture.md) — separate physical cabling,
   logical topology, test-server realization, and management.
2. [Topology and configuration](topology-and-configuration.md) — follow a
   testbed name through all description layers.
3. [The pytest execution lifecycle](execution.md) — place collection,
   fixtures, plugins, call, cleanup, and reporting on one timeline.
4. [Fixtures and device objects](fixtures-and-hosts.md) — understand how local
   Python objects operate remote components.
5. [Packet tests and PTF](packet-tests.md) — map packet APIs to dataplane
   interfaces.
6. [End-to-end annotated test](annotated-test.md) — join those models in one
   real test.

**Checkpoint:** choose one collected node ID and explain its compatible
topologies, selected DUT/PTF objects, packet path, setup side effects, teardown
obligations, and expected artifacts.

## Path B: run and debug an existing test

Read [The pytest execution lifecycle](execution.md), then use the repository's
[running-tests reference][pytest-run] for complete command-line options.

Start with one node ID:

```console
cd tests
./run_tests.sh -d <dut> -n <testbed> -i <inventory> \
  -u -e "--testbed_file <testbed-file>" \
  -c "arp/test_arpall.py::test_arp_unicast_reply"
```

The exact options depend on the branch and lab. Treat this as the command
shape, not a copy-and-run lab prescription.

Classify a failure before changing feature code:

| Earliest failing phase | Read next |
|---|---|
| No tests collected or test skipped | [Topology and configuration](topology-and-configuration.md) and [Plugin lifecycle](plugin-lifecycle.md) |
| Host/fixture setup failed | [Fixtures and device objects](fixtures-and-hosts.md) |
| Packet send, receive, or mask failed | [Packet tests and PTF](packet-tests.md) |
| Test body passed but final result failed | [Plugin lifecycle](plugin-lifecycle.md), especially log analysis and sanity |
| CI-only or evidence is incomplete | [CI and reporting](ci-and-reporting.md) |

**Checkpoint:** identify the earliest failing boundary and one artifact that
proves the previous boundary worked.

## Path C: write or review a pytest test

Read [Fixtures and device objects](fixtures-and-hosts.md), [Packet tests and
PTF](packet-tests.md), [Plugin lifecycle](plugin-lifecycle.md), and the
repository [test-writing guide][writing-tests].

Then inspect, in this order:

1. the test module and its module-level markers;
2. the nearest `conftest.py`;
3. shared fixtures and helper implementations;
4. one neighboring test with the same topology and host objects;
5. cleanup and failure paths; and
6. the relevant test plan or feature design.

Review the helper contract, not only its name. A fixture may be module-scoped,
parameterized, auto-used, topology-filtered, or backed by remote state. Check
those details in current code.

**Checkpoint:** state what the test changes, how it restores that state, which
topologies and roles it supports, and how the result can fail outside the test
assertion.

## Path D: create or deploy a testbed

Read [Testbed architecture](architecture.md), [Anatomy of a topology
file](topology-file-anatomy.md), and then choose:

- [Hands-on virtual lab](virtual-lab.md) for KVM/OVS-based environments;
- [Physical lab bring-up and operations](physical-lab.md) for fanouts,
  cabling, console, and PDU integration; or
- [Multi-DUT and multi-ASIC tests](multi-dut-multi-asic.md) before building a
  distributed or chassis environment.

Do not start by modifying topology YAML. First write down:

- the logical role of each link;
- the concrete DUT/server/fanout inventory names;
- the expected management path;
- the physical or virtual realization of each PTF interface; and
- the recovery path if ordinary SSH fails.

**Checkpoint:** prove management reachability, topology deployment, PTF-agent
reachability, expected DUT-to-PTF port mapping, neighbor sessions, and pre-test
sanity as separate checks.

## Path E: work on a specialized environment

Read the common architecture first; specialized chapters describe what is
added or replaced.

| Work | Common prerequisite | Specialized chapter |
|---|---|---|
| Active-standby or active-active ToR | PTF realization and plugins | [Dual-ToR and mux simulation](dualtor.md) |
| Hardware traffic generator | Topology/configuration and port mapping | [Traffic generators and Snappi](traffic-generators.md) |
| Disruptive image or control-plane event | Execution phases and recovery | [Reboot and upgrade testing](reboot-upgrade.md) |
| QoS dataplane/RPC tests | PTF modes and ASIC selection | [QoS and syncd swap](qos-syncd.md) |
| Direct SAI validation | PTF remote tests and service boundaries | [SAI and PTF testing](sai-ptf.md) |
| NPU plus DPU | Multi-DUT ownership and virtual realization | [SmartSwitch and DPU topologies](smartswitch-dpu.md) |
| SPyTest suite | General lab architecture only | [SPyTest architecture](spytest.md) |

**Checkpoint:** draw the specialized control service and dataplane path, then
mark which common pytest assumptions no longer apply.

## Path F: contribute repository-wide changes

Use [A guided code tour](code-tour.md), [Map of existing
documentation](documentation-map.md), and the [test guidelines][guidelines].
Before changing a shared fixture or plugin, search all call sites and test
collections that load it. Shared `conftest.py` and auto-used plugins can
change tests far outside the directory where the edit is made.

**Checkpoint:** list affected collection paths, fixture scopes, topology
families, cleanup behavior, and CI artifacts before opening the change.

[pytest-run]: https://github.com/sonic-net/sonic-mgmt/blob/master/docs/tests/pytest.run.md
[writing-tests]: https://github.com/sonic-net/sonic-mgmt/blob/master/docs/tests/writing.tests.help.md
[guidelines]: https://github.com/sonic-net/sonic-mgmt/blob/master/docs/tests/guidelines.md
