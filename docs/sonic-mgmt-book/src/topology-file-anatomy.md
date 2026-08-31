# Anatomy of a topology file

`ansible/vars/topo_t0-8.yml` is small enough to read in one sitting and rich
enough to show the important relationships.

## Topology indices

The file begins with two groups of integers:

```yaml
topology:
  host_interfaces: [4, 5, 6, 7]
  VMs:
    ARISTA01T1:
      vlans: [0]
      vm_offset: 0
```

Indices `0` through `3` connect four emulated T1 neighbors. Indices `4`
through `7` are host-facing interfaces exposed to PTF. These are positions in
the topology model. They are not literal `Ethernet0` through `Ethernet7` DUT
names.

`vm_offset` selects a neighbor instance relative to the testbed entry's
`vm_base`. Reusing the topology with another `vm_base` changes the concrete VM
names without changing the logical layout.

## DUT VLAN intent

The `DUT.vlan_configs` section groups host indices into `Vlan1000`:

```yaml
one_vlan_a:
  Vlan1000:
    id: 1000
    intfs: [4, 5, 6, 7]
    prefix: 192.168.0.1/21
    prefix_v6: fc02:1000::1/64
    tag: 1000
```

This says which logical host links belong to the VLAN and supplies its
addresses. Deployment code combines this intent with the DUT port mapping; it
does not assume index `4` is a particular front-panel port on every platform.

## Shared configuration properties

`configuration_properties.common` defines values reused by neighbors: DUT
role, AS numbers, subnet allocation parameters, and next-hop addresses. The
indirection keeps repeated neighbor blocks consistent.

Values such as `dut_asn` are topology intent. Runtime tests should still read
deployed facts when verifying the actual device.

## Per-neighbor configuration

Each entry under `configuration` references `common` and defines its BGP and
interface configuration:

```yaml
ARISTA01T1:
  properties: [common]
  bgp:
    asn: 64600
    peers:
      65100:
        - 10.0.0.56
        - FC00::71
  interfaces:
    Port-Channel1:
      ipv4: 10.0.0.57/31
```

The peer address and neighbor interface address are opposite sides of the
same link. The topology role and template determine how this neighbor-side
configuration becomes DUT minigraph content.

## How an index becomes a runtime port

```text
topo_t0-8.yml index
        + testbed instance and VM base
        + DUT hardware/port mapping
        + deployed minigraph or Config DB
        |
        v
get_extended_minigraph_facts(tbinfo)
        |
        +-- minigraph_ports
        +-- minigraph_neighbors
        +-- minigraph_ptf_indices
        +-- minigraph_vlans
```

The runtime map is the safe boundary for tests. Given `dut_port`, use
`mg_facts["minigraph_ptf_indices"][dut_port]`; do not reverse-engineer a port
name from the integer in the YAML.

## Exercise

1. List indices `0` through `7` and label each as neighbor-facing or
   host-facing.
2. For `ARISTA01T1`, pair its BGP peer addresses with its own interface
   addresses.
3. Find a testbed entry whose `topo` is `t0-8` and record its `vm_base`, DUT,
   PTF host, and server.
4. On a deployed instance, print `minigraph_ptf_indices` and locate indices
   `4` through `7`.
5. Explain any difference between your expected and observed DUT port names.

Common mistakes are confusing topology indices with Linux interface numbers,
editing a topology without checking every consumer, and expecting a topology
marker to deploy this file automatically.

Deeper reference: [topo_t0-8.yml](https://github.com/sonic-net/sonic-mgmt/blob/master/ansible/vars/topo_t0-8.yml).
