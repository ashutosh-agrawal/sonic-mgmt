# Hands-on virtual lab

This exercise answers a practical question: how do the deployment and test
lifecycles join together on a small KVM testbed?

## Before you begin

Use a Linux test server that meets the KVM testbed prerequisites. You need a
SONiC virtual-switch image, a supported neighbor image (cEOS is the current
recommended option in the setup guide), a management inventory, and a
password file. The commands below create and remove VMs, containers, bridges,
and DUT configuration. Replace every example name with values from your lab.

Read `docs/testbed/README.testbed.VsSetup.md` completely before provisioning.
Its image, host-package, and networking requirements are authoritative.

## The deployment model

```text
prepare host and images
        |
        v
start neighbor VMs/containers
        |
        v
add-topo: create PTF and connect logical links
        |
        v
deploy-mg: configure the virtual DUT
        |
        v
run one read-only pytest
        |
        v
remove-topo and, when appropriate, stop VMs
```

Keep the deployment and test commands separate. A pytest failure does not
necessarily mean deployment failed, and a successful `add-topo` does not prove
that the DUT is ready for testing.

## 1. Confirm the selected testbed

From `ansible/`, inspect the `vms-kvm-t0` entry in `vtestbed.yaml`, the DUT name
in `veos_vtb`, and `vars/topo_t0.yml`. Record the testbed name, DUT hostname,
server, PTF container, VM base, and topology type.

Checkpoint: every hostname resolves from the management container, and the
selected VM range does not overlap another active topology.

## 2. Start the neighbors

For a four-neighbor cEOS topology, the setup guide uses:

```bash
cd ansible
./testbed-cli.sh -m veos_vtb -n 4 start-vms server_1 password.txt
```

Use `-k veos` or `-k vsonic` only when those are the images you prepared.

Checkpoint: the neighbor instances exist on the test server and their
management endpoints are reachable. If this fails, debug the image and test
server before touching the DUT.

## 3. Realize the topology

```bash
./testbed-cli.sh -t vtestbed.yaml -m veos_vtb add-topo vms-kvm-t0 password.txt
```

`add-topo` creates the PTF environment and virtual connectivity described by
the testbed entry and topology file. It does not merely start four routers.

Checkpoint: the PTF container is running, expected PTF interfaces exist, and
the virtual bridges or links connect the intended PTF and neighbor endpoints.

## 4. Configure the DUT

```bash
./testbed-cli.sh -t vtestbed.yaml -m veos_vtb \
  deploy-mg vms-kvm-t0 veos_vtb password.txt
```

Checkpoint: the DUT is reachable, its minigraph-derived interfaces are up,
and expected BGP sessions converge. A management connection alone is not a
dataplane checkpoint.

## 5. Run one read-only test

From `tests/`, start with the same small fact test used by the maintained setup
guide:

```bash
./run_tests.sh -n vms-kvm-t0 -d vlab-01 \
  -c bgp/test_bgp_fact.py -f vtestbed.yaml \
  -i ../ansible/veos_vtb
```

Checkpoint: classify any failure as collection, fixture setup, test call,
teardown, Log Analyzer, or sanity. Save the generated command, logs, and JUnit
XML before changing the environment.

## 6. Clean up

When the environment is no longer needed:

```bash
cd ../ansible
./testbed-cli.sh -t vtestbed.yaml -m veos_vtb \
  remove-topo vms-kvm-t0 password.txt
```

Stop shared neighbor VMs only if no other topology uses them. Cleanup is an
operational decision; do not make that assumption from the example command.

## Common failures

| Symptom | Likely layer |
|---|---|
| VM will not start | image format, KVM support, memory, VM range |
| `add-topo` fails | testbed entry, bridge state, PTF image, stale topology |
| DUT unreachable | inventory, management network, credentials, DUT image |
| BGP does not converge | topology configuration, neighbor type, port mapping |
| pytest option is unknown | test path ordering or wrong working directory |
| packet test later fails | PTF indices, interface state, virtual link mapping |

Deeper reference: [KVM Testbed Setup](https://github.com/sonic-net/sonic-mgmt/blob/master/docs/testbed/README.testbed.VsSetup.md).
