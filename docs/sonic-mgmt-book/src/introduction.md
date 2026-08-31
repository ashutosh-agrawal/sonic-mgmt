# Understanding sonic-mgmt

`sonic-mgmt` is the integration and system-test repository for SONiC. It does
more than hold tests: it describes testbeds, deploys physical and virtual
topologies, configures devices, drives traffic, runs pytest, and processes
results.

That breadth makes the repository difficult to learn by browsing folders. A
reader can understand an individual test and still not know where its fixtures
came from, why it runs on one topology but not another, or how PTF port `3`
maps to a physical DUT interface.

This book builds that missing mental model.

## What you will learn

After the foundational chapters, you should be able to explain:

1. The roles of the test runner, DUT, PTF host, neighbors, fanouts, and test
   server.
2. The difference between physical topology, logical topology, and a testbed
   instance.
3. How inventory, testbed, topology, connection graph, and runtime facts
   combine.
4. How pytest creates `duthosts`, `ptfhost`, `nbrhosts`, and `tbinfo`.
5. How a packet created in a test reaches a DUT port and is verified.
6. Where to look when a test is skipped, fails setup, fails an assertion, or
   leaves the testbed unhealthy.

## What this book is not

It does not replace every file under `docs/`. Those files contain setup
instructions, designs, test plans, and API reference. This guide provides an
ordered route through them and explains how the pieces relate.

It assumes basic familiarity with Linux, networking, Python, and pytest.
Ansible knowledge helps, but its role is introduced here.

## A useful first principle

Most confusion disappears once you keep these concerns separate:

- **Description:** files declare devices, topology intent, and physical links.
- **Deployment:** Ansible playbooks and roles create and configure a topology.
- **Testing:** pytest loads the descriptions and exposes deployed components as
  fixtures and device objects.
- **Reporting:** plugins and tools collect logs, artifacts, and results.

The same data connects these stages, but they happen at different times.

