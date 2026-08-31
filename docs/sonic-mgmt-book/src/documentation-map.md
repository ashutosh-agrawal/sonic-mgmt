# Map of existing documentation

The existing documents are most useful when grouped by reader intent rather
than filename.

## Understand the testbed

Read in this order:

1. [`README.testbed.Overview.md`](https://github.com/sonic-net/sonic-mgmt/blob/master/docs/testbed/README.testbed.Overview.md) — physical and logical topology;
2. [`README.testbed.Minigraph.md`](https://github.com/sonic-net/sonic-mgmt/blob/master/docs/testbed/README.testbed.Minigraph.md) — topology-derived DUT config;
3. [`README.testbed.Setup.md`](https://github.com/sonic-net/sonic-mgmt/blob/master/docs/testbed/README.testbed.Setup.md) — physical testbed setup;
4. [`README.testbed.VsSetup.md`](https://github.com/sonic-net/sonic-mgmt/blob/master/docs/testbed/README.testbed.VsSetup.md) — virtual setup, when applicable;
5. [`README.testbed.Cli.md`](https://github.com/sonic-net/sonic-mgmt/blob/master/docs/testbed/README.testbed.Cli.md) — operational command reference.

Treat specialized files for dual-ToR, WAN, OCS, Snappi, NUT, VPP,
SmartSwitch, and SAI as branches to read only when needed.

## Run, write, and review pytest

Read [`docs/tests/README.md`](https://github.com/sonic-net/sonic-mgmt/blob/master/docs/tests/README.md),
[`pytest.run.md`](https://github.com/sonic-net/sonic-mgmt/blob/master/docs/tests/pytest.run.md),
[`guidelines.md`](https://github.com/sonic-net/sonic-mgmt/blob/master/docs/tests/guidelines.md),
[`styleguide.md`](https://github.com/sonic-net/sonic-mgmt/blob/master/docs/tests/styleguide.md),
and [`pytest.logging.md`](https://github.com/sonic-net/sonic-mgmt/blob/master/docs/tests/pytest.logging.md),
in that order.

[`pytest.org.md`](https://github.com/sonic-net/sonic-mgmt/blob/master/docs/tests/pytest.org.md)
is historically useful, but it is a proposal and some examples describe older
behavior. Verify details against current code.

## Look up host APIs

[`docs/api_wiki/`](https://github.com/sonic-net/sonic-mgmt/tree/master/docs/api_wiki)
is reference-style documentation for preconfigured fixtures, Ansible modules,
`SonicHost`, `SonicAsic`, and multi-ASIC methods. Use it after learning the
host-object model; do not read it linearly.

## Deploy with Ansible

Start with [`docs/ansible/README.md`](https://github.com/sonic-net/sonic-mgmt/blob/master/docs/ansible/README.md),
then select a deployment guide for your task. The authoritative behavior
remains current playbooks, roles, inventory variables, and
`ansible/testbed-cli.sh`.

## Understand feature coverage

Use `docs/testplan/` as a searchable catalog. Test plans explain intent,
topology, and cases for individual features. They are not framework tutorials
and may precede later implementation changes.

## Reporting and alternative frameworks

- [`test_reporting/README.md`](https://github.com/sonic-net/sonic-mgmt/blob/master/test_reporting/README.md)
  covers result processing.
- [`spytest/Doc/intro.md`](https://github.com/sonic-net/sonic-mgmt/blob/master/spytest/Doc/intro.md)
  introduces SPyTest, a separate framework.

## Judge freshness

Before trusting a command or API, check whether the document is a proposal,
HLD, or test plan; compare flags with current `--help`; search for the fixture
or function; and prefer current code when narrative and implementation differ.
