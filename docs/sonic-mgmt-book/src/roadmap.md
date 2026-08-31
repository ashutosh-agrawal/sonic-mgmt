# Roadmap and status

The learner-first foundation and the original expansion roadmap are now
represented in the book.

## Completed expansion chapters

| Roadmap item | Chapter |
|---|---|
| hands-on virtual lab | [Hands-on virtual lab](virtual-lab.md) |
| end-to-end annotated test | [End-to-end annotated test](annotated-test.md) |
| topology file anatomy | [Anatomy of a topology file](topology-file-anatomy.md) |
| plugin lifecycle | [Plugin lifecycle](plugin-lifecycle.md) |
| multi-DUT and multi-ASIC | [Multi-DUT and multi-ASIC tests](multi-dut-multi-asic.md) |
| CI and reporting | [CI and reporting](ci-and-reporting.md) |
| dual-ToR and mux simulation | [Dual-ToR and mux simulation](dualtor.md) |
| traffic generators and Snappi | [Traffic generators and Snappi](traffic-generators.md) |
| reboot and upgrade testing | [Reboot and upgrade testing](reboot-upgrade.md) |
| QoS and syncd swap | [QoS and syncd swap](qos-syncd.md) |
| SAI/PTF testing | [SAI and PTF testing](sai-ptf.md) |
| SmartSwitch/DPU topologies | [SmartSwitch and DPU topologies](smartswitch-dpu.md) |
| SPyTest architecture | [SPyTest architecture](spytest.md) |
| physical lab bring-up | [Physical lab bring-up and operations](physical-lab.md) |

These chapters are orientation and guided-reading material. Maintained setup
guides and current code remain authoritative for commands, supported hardware,
images, and environment-specific values.

## Future contribution themes

Further work should deepen chapters with reproducible, versioned lab captures
rather than introduce more top-level topics. Good candidates include annotated
CI artifact bundles, vendor-neutral virtual-lab fixtures, and worked failure
case studies.

## Contribution pattern

Each new chapter should include a question it answers, prerequisites, a
conceptual model, one current-code trace, a reproducible exercise when
practical, expected observations, common failures, and deeper references.

Avoid copying a setup guide into the book. Explain why its inputs exist, then
link to the maintained how-to guide.
