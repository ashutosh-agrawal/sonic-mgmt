# Topology and configuration

In `sonic-mgmt`, “topology” is often used for related but different things.

## Physical and logical topology

The **physical topology** is the real cabling among DUTs, fanout switches, and
test servers. A connection graph records these links and device metadata. It
answers: “Which cable and fanout port reach this DUT interface?”

The **logical topology** is the network role presented to the DUT:

- `t0`: server-facing ports below a ToR and emulated T1 neighbors above;
- `t1`: emulated T0 neighbors below and T2 neighbors above;
- `t2`: multiple DUT nodes or line cards with T1 and T3 neighbors;
- `ptf`: DUT ports exposed to PTF without routed neighbor VMs.

Names such as `t0-56` and `t1-lag` are variants. The family matters to test
markers, while the exact variant determines the port and VM layout.

## Topology definition

An `ansible/vars/topo_<name>.yml` file describes a logical topology. Common
sections include:

- `topology.host_interfaces`: PTF-facing or server-facing indices;
- `topology.VMs`: neighbor names, VM offsets, and VLAN indices;
- `topology.DUT`: DUT details and optional VLAN layouts;
- `configuration_properties`: reusable network properties;
- `configuration`: per-neighbor BGP and interface configuration.

These integers are topology indices. They are not automatically SONiC
front-panel names such as `Ethernet0`.

## Testbed instance

An entry in `ansible/testbed.yaml` or a site-specific file binds a logical
topology to resources:

```yaml
- conf-name: vms-example-t0
  topo: t0
  ptf: ptf_vms-example
  server: server_1
  vm_base: VM0100
  dut:
    - dut-01
  inv_name: lab
```

The topology says what a `t0` looks like. The testbed entry says which DUT,
PTF host, server, and VM range realize it.

## No single configuration file is complete

```text
inventory files ---------> connection targets and host/group variables --+
                                                                        |
testbed YAML ------------> selected DUT/PTF/server/topology -------------+---> pytest fixtures
                                                                        |
topo_<name>.yml ---------> logical links, neighbors, BGP, addresses -----+
                                                                        |
connection graph --------> physical DUT/fanout links --------------------+
                                                                        |
DUT minigraph/config DB -> runtime port and feature facts ---------------+
```

### Inventory

Ansible inventory answers “how do I contact this named device?” It supplies
hosts, groups, management addresses, connection types, credentials, and site
variables. It does not by itself choose a testbed.

### Testbed data

`--testbed_file` selects a YAML/CSV file and `--testbed` selects an entry.
`tests/common/testbed.py::TestbedInfo` parses it. The session-scoped `tbinfo`
fixture exposes the normalized result.

### Connection graph

Connection-graph data maps physical DUT ports to fanout ports and can describe
console, power, and other infrastructure. `conn_graph_facts` loads it when a
test needs physical wiring knowledge or link control.

### Runtime facts

The deployed DUT is the final authority for many tests. Calls such as
`get_extended_minigraph_facts(tbinfo)` and `config_facts()` read actual state.
They connect topology indices, DUT interfaces, and PTF indices.

## Topology marker

Tests declare compatible topology families:

```python
pytestmark = [pytest.mark.topology("t0", "t1")]
```

This is a compatibility declaration, not a deployment request. Pytest will not
turn a deployed `t0` testbed into `t1`.

## Trace an unexplained value

1. Identify the fixture that supplied it.
2. Find the fixture in the nearest `conftest.py`, then walk toward
   `tests/conftest.py`.
3. Check whether it reads CLI options, `tbinfo`, inventory variables, topology
   properties, connection-graph facts, or DUT facts.
4. Locate the selected testbed entry and its `topo_<name>.yml`.
5. Compare declared data with runtime DUT facts.

