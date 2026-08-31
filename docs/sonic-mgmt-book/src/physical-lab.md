# Physical lab bring-up and operations

A physical lab is successful only when its declared model, cabling, management
access, and runtime observations agree.

## Configuration layers

| Layer | What it owns |
|---|---|
| inventory | management addresses, groups, connection types, variables |
| credentials | DUT, server, neighbor, fanout, console, and PDU access |
| testbed entry | selected DUTs, PTF, server, topology, VM base |
| topology file | logical neighbor and host-facing intent |
| connection graph | physical DUT/fanout links, console, and power relations |
| server configuration | management bridge, PTF, VMs/containers, OVS/VLAN links |
| DUT runtime | deployed config, interface state, routes, and services |

Version these files in an access-controlled lab repository. Do not bake the
only copy into a disposable management container.

## Bring-up order

1. Record cable labels, device serials, management addresses, console, and PDU
   outlets.
2. Verify management access to each device independently.
3. Build inventory and credential groups; test read-only commands.
4. Create the connection graph and compare every link with physical labels.
5. Prepare the test server, management bridge, container images, and neighbor
   images.
6. Define the testbed entry and logical topology.
7. Deploy fanout VLAN behavior appropriate for the fanout platform.
8. Add the topology, deploy DUT configuration, and validate each layer.
9. Run a read-only fact test before a packet test or disruptive suite.

## Layered acceptance checklist

- Management: SSH/API access works without relying on accidental local state.
- Infrastructure: console and power control target the intended device.
- Links: DUT and fanout port names match the connection graph.
- Server: expected PTF and neighbor interfaces exist.
- Control plane: interfaces and routing sessions converge.
- Dataplane: a known packet traverses the expected PTF/DUT path.
- Recovery: topology removal and baseline restoration are documented.

## Operations after bring-up

Maintain reservation ownership, change logs, image history, known-failure
records, and recovery procedures. Audit connection data after recabling or
hardware replacement. Keep topology changes separate from test debugging so a
failed experiment does not silently become the new lab baseline.

## Exercise

Pick one DUT port and trace it through five names: physical label, connection
graph endpoint, fanout port, DUT runtime interface, and PTF index. Repeat for a
console and PDU connection. Any missing mapping is an operational gap even if
today's test happens to pass.

Deeper references: [physical testbed setup](https://github.com/sonic-net/sonic-mgmt/blob/master/docs/testbed/README.testbed.Setup.md),
[testbed configuration](https://github.com/sonic-net/sonic-mgmt/blob/master/docs/testbed/README.new.testbed.Configuration.md),
and [minigraph deployment](https://github.com/sonic-net/sonic-mgmt/blob/master/docs/testbed/README.testbed.Minigraph.md).
