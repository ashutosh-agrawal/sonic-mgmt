# Packet tests and PTF

PTF gives tests programmatic access to dataplane ports. There are two common
execution styles.

## `ptfadapter` in pytest

The pytest process creates packets with PTF/Scapy helpers and uses the adapter:

```python
from ptf import testutils

def test_forwarding(ptfadapter, src_port, dst_port, packet, expected):
    ptfadapter.dataplane.flush()
    testutils.send(ptfadapter, src_port, packet)
    testutils.verify_packet(ptfadapter, expected, dst_port)
```

This works well when control-plane setup and assertions live in pytest.

## A remote PTF test

Pytest prepares the DUT and parameters, then `ptf_runner` launches a test class
from `tests/ptftests/` in the PTF environment. The remote test performs packet
operations and returns a result.

## Packet path

For a typical physical `t0` testbed:

```text
pytest test
  -> PTF API or remote PTF test
  -> PTF interface with integer index
  -> OVS/VLAN tunnel on the test server
  -> fanout path
  -> DUT front-panel interface
  -> DUT forwarding pipeline
  -> another DUT interface
  -> fanout/OVS
  -> PTF receive interface
  -> packet verification
```

Virtual and specialized testbeds change the middle of this path, but still need
a correct mapping among logical ports, PTF indices, and DUT interfaces.

## Port mapping

Derive ports from facts:

```python
mg_facts = duthost.get_extended_minigraph_facts(tbinfo)
ptf_index = mg_facts["minigraph_ptf_indices"][dut_port]
```

Never assume an integer index is a stable front-panel port across hardware.
Multi-DUT and dual-ToR topologies may need composite or state-aware maps. Reuse
the port-map fixtures in `tests/common/fixtures/ptfhost_utils.py`.

## Expected packets and masks

Forwarding changes fields. Tests use `ptf.mask.Mask` to ignore irrelevant or
nondeterministic fields and explicitly update predictable changes such as MAC
addresses, TTL/hop-limit, checksums, VLAN tags, and tunnel headers.

A mask should express the contract being tested. Masking too much accepts bad
packets; comparing every byte makes tests brittle.

## Debug packet failures

1. Confirm the selected DUT and topology.
2. Log DUT interface names and PTF indices.
3. Verify interface and link state on both ends.
4. Flush stale PTF packets before sending.
5. Capture on PTF interfaces and, where possible, intermediate OVS links.
6. Check DUT counters, routes, neighbors, ACLs, and drop counters.
7. Compare the observed packet with the expected mask field by field.
