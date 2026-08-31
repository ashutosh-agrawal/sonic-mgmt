# Fixtures and device objects

Fixtures provide dependency injection and lifecycle management. Device objects
make remote systems usable through a consistent Python interface.

## Core fixtures

| Fixture | Meaning | Typical scope |
|---|---|---|
| `tbinfo` | Parsed selected testbed and topology data | session |
| `duthosts` | Collection of selected SONiC DUT objects | session |
| `rand_one_dut_hostname` | Stable random DUT choice for a module | module |
| `ptfhost` / `ptfhosts` | PTF host object(s) | session |
| `ptfadapter` | Packet send/receive interface backed by PTF | function |
| `nbrhosts` | Mapping of logical neighbors to objects/config | session |
| `fanouthosts` | Mapping of lab fanout devices | session |
| `localhost` | Ansible-backed local runner object | session |

Multi-DUT tests should use `duthosts` plus an explicit hostname selector rather
than assume `duthosts[0]` is the desired frontend node.

## Fixture lookup

Pytest searches through installed plugins, plugins registered by
`tests/conftest.py`, the global conftest itself, parent and feature conftests,
and finally the test module. A nearer definition can override a broader one.

Use `pytest --fixtures <test-path>` or search for `def <fixture_name>` to find
the candidates.

## Host wrappers

`tests/common/devices/base.py::AnsibleHostBase` wraps a host supplied by
pytest-ansible. Unknown attributes are resolved as Ansible modules:

```python
duthost.shell("show version")
```

This invokes the Ansible `shell` module for that host and returns its module
result. Subclasses add domain behavior:

- `SonicHost` adds SONiC commands and facts;
- `MultiAsicSonicHost` exposes per-ASIC namespaces and objects;
- `PTFHost` adds PTF-specific helpers;
- `EosHost`, `CiscoHost`, and others represent neighbors;
- `FanoutHost` provides a facade over supported fanout types.

Do not confuse the wrapper with the remote machine. Attribute access is local,
while most method calls perform remote work.

## DUT selection

`duthosts` is a `DutHosts` collection keyed by hostname as well as iterable:

```python
def test_feature(duthosts, rand_one_dut_hostname):
    duthost = duthosts[rand_one_dut_hostname]
    result = duthost.shell("show feature status")
```

Topology-aware selectors exist for frontend nodes, ASICs, and hardware SKUs.
Reuse them instead of inventing a random choice that might select a supervisor
or incompatible node.

## Cleanup

Prefer a `yield` fixture for temporary configuration:

```python
@pytest.fixture
def configured_feature(duthost):
    enable_feature(duthost)
    yield
    disable_feature(duthost)
```

Cleanup must tolerate partial setup and should restore prior state rather than
a presumed default. A teardown failure can contaminate later tests.

