# Multi-DUT and multi-ASIC tests

Multi-DUT and multi-ASIC describe different dimensions. A testbed can contain
several physical DUTs, and each DUT can expose one or several ASIC namespaces.

## Object hierarchy

```text
duthosts: DutHosts
  |
  +-- one MultiAsicSonicHost per selected DUT
        |
        +-- sonichost: global host context
        +-- asics: SonicAsic objects
              +-- frontend ASICs
              +-- backend/fabric ASICs
```

`DutHosts.frontend_nodes` excludes supervisor-only nodes.
`DutHosts.supervisor_nodes` identifies control-plane cards in a chassis.
Within one `MultiAsicSonicHost`, `frontend_asics` and `backend_asics` describe
packet-facing and internal/fabric roles.

## Select explicitly

Use a hostname selector supplied by pytest:

```python
def test_example(duthosts, enum_rand_one_per_hwsku_frontend_hostname):
    duthost = duthosts[enum_rand_one_per_hwsku_frontend_hostname]
```

If the test is ASIC-specific, request an ASIC parameter and resolve it:

```python
asichost = duthost.asic_instance(enum_frontend_asic_index)
```

Avoid positional assumptions such as `duthosts[0]` or `asic_instance(0)` unless
the test contract explicitly requires the first element and documents why.
Enumeration fixtures encode topology, role, and hardware-SKU rules that a
local random choice can silently bypass.

## Namespace-aware operations

Some methods on `MultiAsicSonicHost` dispatch to the global host, one ASIC, or
all ASICs. Others return namespace-indexed data. Read the helper contract
before assuming a method operates in every namespace.

For direct commands, prefer `SonicAsic` helpers or namespace-aware wrapper
methods. Manually prefixing commands with `ip netns exec` is harder to review
and can mishandle the default namespace.

## Chassis roles

A modular chassis introduces supervisors, frontend line cards, and often
backend/fabric ASICs. Tests that need dataplane ports usually target frontend
nodes and frontend ASICs. Tests of chassis control-plane services may need
supervisors. Fabric tests intentionally target backend paths.

The selected testbed can also be distributed across multiple test servers.
Do not infer PTF or neighbor ownership from the first DUT.

## Common hazards

| Hazard | Consequence |
|---|---|
| first-DUT assumption | supervisor or wrong line card selected |
| ASIC 0 assumption | backend ASIC or wrong namespace selected |
| global DB command | reads only default namespace |
| flattened port map | identical-looking indices collide across DUTs |
| one cleanup action | only one namespace is restored |
| global service assumption | service actually runs per ASIC |

## Review exercise

Choose a chassis test and label every operation with two coordinates:
`(DUT hostname, ASIC namespace)`. Mark operations that deliberately use the
global namespace. If either coordinate is implicit, identify the fixture or
helper that supplies it. This makes accidental single-DUT assumptions visible.

Deeper references: [DutHosts](https://github.com/sonic-net/sonic-mgmt/blob/master/tests/common/devices/duthosts.py),
[MultiAsicSonicHost](https://github.com/sonic-net/sonic-mgmt/blob/master/tests/common/devices/multi_asic.py),
and [VOQ chassis pytest guide](https://github.com/sonic-net/sonic-mgmt/blob/master/tests/docs/pytest.voq_chassis.md).
