# Map of existing documentation

The repository already contains substantial documentation. The difficulty is
that overview, setup guide, HLD, test plan, generated API reference, and current
implementation answer different questions. This map organizes them by intent
and explains how to judge authority.

## Source types

| Source type | Best use | Common limitation |
|---|---|---|
| Architecture/overview | Stable concepts and system boundaries | May omit operational detail |
| Setup/run guide | Prerequisites and commands for a workflow | Often branch, image, vendor, or site specific |
| HLD/design | Motivation, proposed architecture, tradeoffs | May describe planned rather than merged behavior |
| Test plan | Feature intent, topology, cases, expected behavior | Implementation may have evolved |
| Generated API wiki | Discover fixture/helper names | Scope, signature, or text may be incomplete/stale |
| README beside code | Local contract and examples | Can lag implementation |
| Current implementation/tests | Present runtime behavior | May not explain intent or operating context |
| CI artifact/report | What one run observed | Specific to code, image, lab, and time |

Use at least one intent source and the current implementation for substantive
work.

## Understand testbed architecture

Read in this order:

1. [Testbed overview][testbed-overview] — physical/logical topology, PTF link
   types, management and backplane networks.
2. [Testbed internals][testbed-internal] — namespaces, OVS, veth, VLAN, VM and
   PTF realization.
3. [Testbed routing][testbed-routing] — ExaBGP, backplane, neighbors, and route
   advertisement.
4. [New testbed configuration][testbed-config] — testbed YAML, inventory,
   connection, fanout, console/PDU, and related schemas.
5. [Testbed CLI][testbed-cli] — operation reference.

Then read one concrete topology file and the deployment roles that consume it.

## Build a physical or virtual lab

- [Physical testbed setup][physical-setup] — devices, server, graph, fanout,
  PTF and deployment.
- [KVM/VS setup][vs-setup] — virtual DUT/neighbors, images, inventory, and
  lifecycle.
- [cEOS setup][ceos] — containerized neighbor details.
- [Fanout management][fanout] — credentials and supported deployment behavior.
- [Minigraph][minigraph] — topology-derived DUT configuration.
- [Multiple servers][multi-server] — distributed PTF and interface ownership.
- [Docker testbed][docker] — container placement and workflows.

The physical setup guide contains platform/site assumptions. Treat fanout
automation and credential names as implementation contracts to verify, not
vendor-neutral standards.

## Run, write, and debug pytest

1. [Pytest overview][pytest-overview]
2. [Running tests][pytest-run]
3. [Writing tests][writing-tests]
4. [Test guidelines][guidelines]
5. [Style guide][styleguide]
6. [Pytest logging][pytest-logging]

`docs/tests/pytest.org.md` is a historical organization proposal. It explains
some original design intent but does not define current fixture/plugin
behavior.

For current behavior, read:

- `tests/run_tests.sh`;
- `tests/conftest.py`;
- nearest feature `conftest.py`;
- test module; and
- shared helper/plugin implementation.

## Understand plugins

Plugin READMEs live beside code under `tests/common/plugins/`. Especially:

- conditional marks;
- sanity check and recovery;
- log analyzer;
- PTF adapter;
- test completeness; and
- feature/monitor plugins relevant to the suite.

Verify option and marker precedence in current plugin code. There is no one
global precedence rule.

## Look up fixtures and device APIs

[API wiki][api-wiki] groups:

- preconfigured fixtures;
- Ansible modules;
- `SonicHost` methods;
- `SonicAsic` methods; and
- multi-ASIC helpers.

Use it as an index:

1. find a candidate name;
2. open current definition;
3. check scope, parameters, return type, namespace behavior, and side effects;
4. search callers; and
5. inspect tests.

A `?` or thin generated page means “read the code,” not “the helper has no
contract.”

## Understand a feature

`docs/testplan/` is a large searchable catalog. A useful reading sequence is:

1. feature HLD/design in SONiC repositories when applicable;
2. `sonic-mgmt` test plan;
3. test module and local fixtures;
4. shared feature helpers;
5. topology/lab guide; and
6. recent CI evidence.

Search by feature, protocol, command, marker, fixture, and expected state—not
only the test filename.

Feature test plans can predate refactors. Compare case names and assertions
with current collected tests.

## Specialized testbeds and frameworks

| Topic | Starting document |
|---|---|
| Dual-ToR | [Dual-ToR setup][dualtor] and dual-ToR test plans |
| Snappi/TGEN | [Snappi testbed][snappi] and `tests/snappi_tests/README.md` |
| QoS RPC | [QoS RPC][qos-rpc] and `tests/qos/files/qos.md` |
| SAI qualification | [SAI quality][sai-quality] and PTF-SAIv2 guide |
| SmartSwitch/DPU | [SmartSwitch VS][smartswitch] plus DASH/SmartSwitch test plans |
| Chassis/VOQ | `tests/docs/pytest.voq_chassis.md` and chassis test plans |
| SPyTest | [SPyTest introduction][spytest] and `spytest/Doc/` |

Read the common architecture first, then identify what the specialized setup
adds or replaces.

## CI and result processing

- `.azure-pipelines/` — current repository pipeline templates.
- [Running tests][pytest-run] — local command/report behavior.
- [Test reporting][reporting] — JUnit parsing, Kusto/ADX upload, and auth.
- `test_reporting/` — current schemas, parsers, uploaders, queries.

External CI documentation and job artifacts are required to explain
allocation, secrets, and dashboards not defined in the repository.

## Ansible and deployment

Start with:

- [Ansible README][ansible-readme];
- [testbed Ansible overview][ansible-testbed];
- [test/deployment notes][ansible-test]; and
- `ansible/testbed-cli.sh`.

Then trace one CLI operation into playbooks, roles, templates, and variables.
Playbook behavior is the current authority when a setup guide is ambiguous.

## Judge freshness and authority

For any command, field, fixture, or API:

1. identify document type and stated status;
2. inspect git history/date when relevant;
3. compare flags with current `--help`;
4. search the exact name in current code;
5. inspect all significant callers;
6. compare schema/fixtures/tests;
7. distinguish branch/platform assumptions; and
8. state unresolved drift explicitly.

Prefer “the current implementation does X; the older proposal describes Y”
over silently merging both.

## Documentation contribution rule

Update the document that owns the contract:

- schema/field change → configuration/setup reference;
- fixture/plugin behavior → adjacent README/API reference and tests;
- feature intent/coverage → test plan;
- operational command → run/setup guide;
- cross-system explanation → this learning book.

Avoid duplicating an entire procedure here. Explain its place in the system
and link to the maintained how-to.

[testbed-overview]: https://github.com/sonic-net/sonic-mgmt/blob/master/docs/testbed/README.testbed.Overview.md
[testbed-internal]: https://github.com/sonic-net/sonic-mgmt/blob/master/docs/testbed/README.testbed.Internal.md
[testbed-routing]: https://github.com/sonic-net/sonic-mgmt/blob/master/docs/testbed/README.testbed.Routing.md
[testbed-config]: https://github.com/sonic-net/sonic-mgmt/blob/master/docs/testbed/README.new.testbed.Configuration.md
[testbed-cli]: https://github.com/sonic-net/sonic-mgmt/blob/master/docs/testbed/README.testbed.Cli.md
[physical-setup]: https://github.com/sonic-net/sonic-mgmt/blob/master/docs/testbed/README.testbed.Setup.md
[vs-setup]: https://github.com/sonic-net/sonic-mgmt/blob/master/docs/testbed/README.testbed.VsSetup.md
[ceos]: https://github.com/sonic-net/sonic-mgmt/blob/master/docs/testbed/README.testbed.cEOS.md
[fanout]: https://github.com/sonic-net/sonic-mgmt/blob/master/docs/testbed/README.testbed.Fanout.md
[minigraph]: https://github.com/sonic-net/sonic-mgmt/blob/master/docs/testbed/README.testbed.Minigraph.md
[multi-server]: https://github.com/sonic-net/sonic-mgmt/blob/master/docs/testbed/README.testbed.DeployWithMultipleServers.md.md
[docker]: https://github.com/sonic-net/sonic-mgmt/blob/master/docs/testbed/README.testbed.Docker.md
[pytest-overview]: https://github.com/sonic-net/sonic-mgmt/blob/master/docs/tests/README.md
[pytest-run]: https://github.com/sonic-net/sonic-mgmt/blob/master/docs/tests/pytest.run.md
[writing-tests]: https://github.com/sonic-net/sonic-mgmt/blob/master/docs/tests/writing.tests.help.md
[guidelines]: https://github.com/sonic-net/sonic-mgmt/blob/master/docs/tests/guidelines.md
[styleguide]: https://github.com/sonic-net/sonic-mgmt/blob/master/docs/tests/styleguide.md
[pytest-logging]: https://github.com/sonic-net/sonic-mgmt/blob/master/docs/tests/pytest.logging.md
[api-wiki]: https://github.com/sonic-net/sonic-mgmt/tree/master/docs/api_wiki
[dualtor]: https://github.com/sonic-net/sonic-mgmt/blob/master/docs/testbed/README.testbed.DualtorSetup.md
[snappi]: https://github.com/sonic-net/sonic-mgmt/blob/master/docs/testbed/README.testbed.SnappiTests.md
[qos-rpc]: https://github.com/sonic-net/sonic-mgmt/blob/master/docs/testbed/README.testbed.QosRpc.md
[sai-quality]: https://github.com/sonic-net/sonic-mgmt/blob/master/docs/testbed/sai_quality/README.md
[smartswitch]: https://github.com/sonic-net/sonic-mgmt/blob/master/docs/testbed/README.testbed.SmartSwitch.VsSetup.md
[spytest]: https://github.com/sonic-net/sonic-mgmt/blob/master/spytest/Doc/intro.md
[reporting]: https://github.com/sonic-net/sonic-mgmt/blob/master/test_reporting/README.md
[ansible-readme]: https://github.com/sonic-net/sonic-mgmt/blob/master/docs/ansible/README.md
[ansible-testbed]: https://github.com/sonic-net/sonic-mgmt/blob/master/docs/ansible/README.testbed.md
[ansible-test]: https://github.com/sonic-net/sonic-mgmt/blob/master/docs/ansible/README.test.md
