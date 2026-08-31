# A guided code tour

Use this tour to connect the concepts to current code. Read small, targeted
sections rather than entire large files.

## Stop 1: one test module

Choose a modest feature test. Identify its `pytestmark`, function fixtures,
local fixtures, DUT commands and facts, PTF operations, and cleanup after every
`yield`.

Avoid starting with reboot, upgrade, dual-ToR, chassis, QoS, or
traffic-generator suites; they intentionally use more infrastructure.

## Stop 2: `tests/conftest.py`

Read only these landmarks first:

- `pytest_plugins` — global capabilities;
- `pytest_addoption()` — framework command-line inputs;
- `get_tbinfo()` and `tbinfo` — selected testbed data;
- `fixture_duthosts()` — DUT object creation;
- `ptfhost` and `ptfhosts` — PTF object creation;
- `nbrhosts()` — neighbor object creation;
- `pytest_generate_tests()` — topology-aware parameter generation;
- `pytest_collection_modifyitems()` — collection-time behavior.

The file is large because it holds years of shared behavior. Treat it as a set
of subsystems, not a document to read top-to-bottom.

## Stop 3: testbed parsing

Read `tests/common/testbed.py::TestbedInfo`. Follow YAML/CSV loading,
normalization into `testbed_topo`, topology-file loading, and topology-family
recognition. Compare it with one `ansible/testbed.yaml` entry and one small
`ansible/vars/topo_*.yml` file.

## Stop 4: device abstraction

Read `tests/common/devices/base.py::AnsibleHostBase`, especially `__getattr__()`
and `_run()`. Then skim `SonicHost` in `tests/common/devices/sonic.py` and
`PTFHost` in `tests/common/devices/ptf.py`.

This explains why an Ansible module looks like a normal Python method.

## Stop 5: plugins

Inspect `custom_markers`, `conditional_mark`, `sanity_check`, `loganalyzer`,
and `ptfadapter` under `tests/common/plugins/`. For each one, ask whether it
acts at collection, setup, call, teardown, or reporting time.

## Stop 6: deployment

Only after understanding runtime tests, follow `ansible/testbed-cli.sh` into
the playbooks and roles used by `start-topo-vms` and `add-topo`. This separate
lifecycle creates the environment pytest later consumes.

