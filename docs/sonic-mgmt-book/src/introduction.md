# Understanding sonic-mgmt

`sonic-mgmt` is the integration and system-test repository for SONiC. It is
not only a collection of pytest files. The same checkout describes labs,
deploys virtual and physical topologies, operates remote devices, generates and
checks traffic, recovers unhealthy environments, and publishes test evidence.

That breadth explains why learning the repository by directory rarely works.
A reader can understand an individual assertion and still not know:

- which DUT, ASIC, PTF host, or neighbor the fixture selected;
- how PTF port 3 reaches a front-panel interface;
- why a topology marker skipped the test;
- whether a failure came from test logic, deployment, or cleanup; or
- which artifact contains the useful evidence after CI finishes.

This book supplies the missing system model. It follows the information and
traffic paths that cross repository boundaries rather than treating each
folder as an independent subsystem.

## The four pipelines

Most `sonic-mgmt` work belongs to four pipelines. They share data, but happen
at different times and have different failure modes.

| Pipeline | Primary question | Typical machinery | Output |
|---|---|---|---|
| Description | What environment is intended? | Inventory, testbed files, topology YAML, connection graphs | Named devices, roles, logical links, physical mappings |
| Deployment | How is that environment built? | Ansible playbooks and roles, Docker, KVM, OVS, fanout configuration | A realized testbed |
| Execution | How does a test use it? | pytest, plugins, fixtures, host wrappers, PTF | Assertions, logs, captures, recovery actions |
| Reporting | How is the result preserved? | JUnit, log collection, result parsers and uploaders | CI artifacts, dashboards, historical records |

A declaration is not evidence that deployment succeeded. A successful
deployment is not evidence that every runtime interface is healthy. A test
body that passed is not the entire result if teardown or post-test sanity
failed. Keeping those boundaries visible is the central habit this book tries
to teach.

## Five models you will use

The chapters build five related models:

1. **Testbed model** — management, dataplane, physical cabling, logical
   neighbors, and test-server realization.
2. **Configuration model** — how command-line selection, inventory, testbed
   entries, topology files, connection graphs, and live facts are combined.
3. **Execution model** — pytest collection, plugins, fixture setup, test call,
   teardown, and session reporting.
4. **Traffic model** — local PTF adapter calls, remote PTF tests, port
   translation, packet masking, and captures.
5. **Scale model** — multiple DUTs, ASIC namespaces, servers, traffic
   generators, mux/NIC simulators, and specialized control services.

[Testbed architecture](architecture.md) establishes the first model. Each
later chapter adds one layer without redrawing the repository as an
undifferentiated collection of boxes.

## A question the models can answer

Suppose a test sends on PTF port 3 and sees no packet at the expected DUT
interface. A directory-oriented investigation may jump among `tests/`,
`ansible/`, and the DUT CLI. A model-oriented investigation asks:

1. Which selected testbed entry and topology assigned PTF index 3?
2. Is it a direct host interface or an injected neighbor-link interface?
3. Which PTF host owns it in this deployment?
4. Which server interface, VLAN, OVS bridge, fanout port, and cable realize it?
5. Which live DUT interface does minigraph or Config DB map to that index?
6. Did pytest reach the PTF agent, and where is the last packet capture that
   proves the frame crossed a boundary?

Each question has a different source of truth. The answer is usually not in
one file.

## How this book uses sources

The existing documentation remains authoritative for environment-specific
setup and feature design. This book reads those documents as a connected set
and uses current code to verify runtime behavior. Substantive chapters include
a **Documentation basis** section so you can follow the source trail.

Three labels are important:

- **Declared** means a repository file says what should exist.
- **Deployed** means automation created or configured it.
- **Observed** means a command, fact, packet capture, or artifact showed what
  existed during this run.

When documentation and current implementation differ, the chapter identifies
the distinction instead of silently presenting a historical proposal as
current behavior. Commands, fixture scopes, plugin lists, and platform details
can evolve, so use linked source files and `--help` output when operating a
different branch.

## What you should be able to do

After the foundational chapters, you should be able to:

- draw the physical, logical, realization, and management views of one
  selected testbed;
- resolve a testbed name into concrete DUT, PTF, neighbor, and fanout objects;
- explain collection, setup, call, teardown, and post-test checks;
- map a packet from a Python object to a DUT port and back;
- distinguish a test defect from a lab, selection, or cleanup defect;
- choose the correct model for dual-ToR, traffic-generator, SAI, SmartSwitch,
  or SPyTest work; and
- find the existing design, test plan, helper implementation, and result
  artifact before changing code.

## What this book is not

This is not a replacement for every file under `docs/`, an installation
manual for every lab, or an API guarantee. It is a guided architecture and
execution reference that points back to those materials.

The examples assume basic Linux, networking, Python, and pytest familiarity.
Ansible, PTF, OVS, and SONiC internals are introduced at the level needed to
trace a test. You do not need to master all of them before starting.

Continue to [Choose a learning path](learning-path.md), or begin directly with
[Testbed architecture](architecture.md).
