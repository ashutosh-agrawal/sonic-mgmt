# Glossary

Terms are defined in the context of `sonic-mgmt`. Some names have broader
meanings elsewhere.

**API server (traffic generator)**

The service that accepts Snappi/OTG configuration and returns state/metrics.
It controls a traffic-generator backend or chassis but is not itself the
physical chassis or dataplane port.

**ASIC**

The forwarding silicon, or its software/virtual equivalent, programmed through
SAI. A DUT can contain one or several ASICs.

**ASIC namespace**

The Linux/network namespace and SONiC service context associated with one ASIC
in a multi-ASIC device. The default ASIC may use no explicit namespace string;
that does not mean “all ASICs.”

**auto-used fixture**

A pytest fixture with `autouse=True` that joins the dependency graph without
appearing in the test function signature. Sanity, log, and feature setup can
therefore be invisible at the call site.

**backplane network (testbed)**

A test-server network connecting route-injection services such as ExaBGP to
neighbor VMs/containers. It is separate from the neighbor-to-DUT dataplane
link and from lab management.

**collection**

The pytest phase that imports tests, creates concrete items, expands
parameters, and applies selection/skip/xfail policy. A collected item has not
yet run fixture setup.

**connection graph**

Site data describing physical links and infrastructure relationships,
commonly DUT-to-fanout ports plus console and power data. It declares the
expected physical map; it does not prove live cables.

**control plane**

Protocols and state that decide forwarding, such as BGP, routes, neighbors,
and feature configuration. Do not confuse it with the lab management plane
used to operate devices.

**dataplane**

The path taken by packets under test: PTF or a traffic-generator port, server
interfaces/OVS/fanouts, DUT ports/ASICs, and destination endpoints.

**declared / deployed / observed**

Declared: a file states what should exist. Deployed: automation attempted to
create/configure it. Observed: commands, facts, counters, captures, or
artifacts showed what existed during a run.

**DPU**

Data Processing Unit in a SmartSwitch. It has separate management, image,
services, interfaces, programmable/offload pipeline, and logs from the
NPU-side switch.

**DUT**

Device under test, usually a physical or virtual SONiC switch. In a
SmartSwitch or chassis, state the concrete node/role instead of using DUT
ambiguously.

**`DutHosts` / `duthosts`**

The multi-DUT collection and fixture for selected SONiC nodes. It can expose
all nodes, frontend nodes, and supervisor nodes and can fan operations out.

**fanout switch**

A lab Layer-2 switch that maps test-server VLAN tunnels to physical DUT
front-panel cables. A leaf fanout faces DUT ports; an optional root fanout
aggregates server/leaf trunks. It is not a routed topology neighbor.

**fixture**

A pytest-managed dependency with a scope and optional setup/teardown
lifecycle. A yield fixture resumes after `yield` to restore state.

**host object**

A local Python wrapper such as `SonicHost`, `SonicAsic`, or `PTFHost`
that invokes Ansible modules and adds device-specific helpers. Method calls can
have remote effects.

**`host_interfaces`**

Topology indices for links modeled as hosts/servers directly attached to the
DUT and exposed in PTF without a routed neighbor VM. “Direct” describes
test-server realization, not necessarily physical cabling.

**injected PTF interface**

A PTF interface attached through veth/OVS to a routed neighbor-to-DUT link so
PTF can inject and observe packets while the neighbor remains an endpoint.

**inventory**

Ansible data describing host names, groups, management addresses, connection
types, and variables. Inventory makes a name reachable; it does not select a
testbed or logical topology.

**logical topology**

The network roles and relationships modeled around the DUT, such as T0, T1,
T2, PTF, or dual-ToR. It is independent of the exact installed cable plant.

**Log Analyzer**

An auto-used plugin that marks a DUT log interval and checks match, ignore, and
expected regex sets. It can fail a node after the test function passes.

**management plane (testbed)**

The network and credentials used by the management host/container to operate
DUTs, servers, fanouts, neighbors, console servers, and PDUs. It is separate
from traffic under test.

**minigraph / extended minigraph facts**

Topology-derived DUT configuration and its structured runtime facts,
including interfaces, port-channels, neighbors, BGP data, VLANs, and
DUT-interface-to-PTF-index mapping.

**neighbor**

A physical or emulated routed device peering with the DUT. vEOS, cEOS, or
another NOS can implement it. It is not a fanout switch.

**NPU**

Network Processing Unit or NPU-side SONiC switch in a SmartSwitch. It owns
external switch behavior and communicates with separate DPUs.

**OTG**

Open Traffic Generator API model used by Snappi-compatible clients and
backends for ports, protocols, flows, state, and metrics.

**OVS**

Open vSwitch. Test-server OVS bridges join neighbor, PTF injection, DUT-facing
VLAN, mux/NIC, or SmartSwitch endpoints in virtual realizations.

**physical topology**

Installed test servers, trunks, fanouts, cables, DUTs, console lines, and PDU
outlets. A connection graph describes it.

**PTF**

Packet Test Framework. By convention the term can also mean the PTF
container/host and its `ethN` dataplane interfaces, so clarify whether you
mean the software framework, process, or environment.

**`ptfadapter`**

A current module-scoped pytest fixture that controls persistent remote
`ptf_nn_agent` processes and lets pytest construct/send/verify packets
locally.

**PTF port index**

A topology/live-mapping integer used by PTF. It must be joined with the owning
PTF host/device and mapped to a DUT/ASIC interface; it is not a universal
physical port number.

**`ptf_runner`**

A helper that launches a self-contained PTF test remotely, passes serialized
parameters, and fetches logs/pcaps. Its process boundary differs from
`ptfadapter`.

**SAI**

Switch Abstraction Interface between SONiC/vendor components and forwarding
silicon. Direct SAI qualification through saiserver bypasses parts of normal
SONiC orchestration.

**sanity check**

Shared pre/post-test health checks and optional recovery. Sanity can reject an
invalid baseline or detect that a module damaged the environment.

**Snappi**

A Python client/API ecosystem for OTG traffic-generator configuration and
measurement. In `sonic-mgmt`, the API server, backend/chassis, physical port
map, and pytest fixtures are separate components.

**SPyTest**

A separate automation framework in the repository with its own testbed YAML,
feature APIs, lifecycle, traffic-generator integration, batch scheduling, and
reports. It is not the main `duthosts` pytest model.

**syncd**

The SONiC service/container that communicates with vendor SAI and programs the
ASIC. Some QoS/SAI workflows replace it with an RPC-capable image; restoration
is required before normal SONiC testing.

**test server**

The compute host running PTF, neighbor VMs/containers, OVS, VLAN interfaces,
and sometimes a virtual DUT. It is distinct from the management
container/process running pytest, though both can share a machine.

**testbed instance**

A named binding (`conf-name`) of a topology variant to concrete DUTs, server,
PTF/API environment, VM range, and inventory data.

**topology family / variant**

A family such as T0 describes a role pattern. A variant such as `t0-8`
provides concrete link/neighbor/VLAN shape. A selected testbed binds the
variant to resources.

**topology marker**

A pytest compatibility declaration such as
`pytest.mark.topology("t0", "t1")`. It filters execution; it does not deploy
or transform a topology.

**`vm_base` / `vm_offset`**

`vm_base` identifies the first allocated neighbor instance for a testbed;
each topology neighbor's `vm_offset` selects an instance relative to it.

**xdist / worker**

Pytest parallel execution mechanism. Fixture caching, logs, recovery, and
shared DUT/PTF state may be per worker or require explicit coordination.
