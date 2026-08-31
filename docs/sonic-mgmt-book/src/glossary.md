# Glossary

**ASIC namespace**  
A Linux/network namespace and SONiC service context representing one ASIC in a
multi-ASIC device.

**connection graph**  
Site data describing physical links and infrastructure relationships, commonly
between DUT and fanout ports.

**DUT**  
Device under test; usually a physical or virtual SONiC switch.

**fanout**  
A lab switch between DUT front-panel ports and test infrastructure.

**fixture**  
A pytest-managed dependency with a scope and optional setup/teardown lifecycle.

**host object**  
A Python wrapper such as `SonicHost` or `PTFHost` that invokes Ansible modules
and adds device-specific helpers.

**inventory**  
Ansible data describing hosts, groups, connection parameters, and variables.

**logical topology**  
The emulated network role and relationships around the DUT, such as `t0` or
`t1`.

**minigraph facts**  
Structured DUT topology/configuration data, including interfaces, neighbors,
port channels, BGP peers, and PTF index mappings.

**neighbor**  
A physical or emulated device peering with the DUT.

**PTF**  
Packet Test Framework, plus by convention the Linux host/container and
interfaces used to inject and verify packets.

**PTF port index**  
An integer interface identifier used by PTF. Runtime facts associate it with a
DUT interface; it is not a universal physical port number.

**testbed instance**  
A named binding of a logical topology to DUTs, PTF host, server, VM range,
inventory, and policies.

**test server**  
Compute host running topology VMs/containers, PTF, and virtual switching.

**topology marker**  
A pytest compatibility declaration used to select or skip tests by topology
family.

