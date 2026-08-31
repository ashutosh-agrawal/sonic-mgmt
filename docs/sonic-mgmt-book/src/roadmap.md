# Roadmap and maintenance

The book now covers the original learning roadmap at a system level. Future
work should deepen evidence and platform examples without turning this guide
into a second, drifting copy of setup references.

## Current coverage

| Area | Chapters | Coverage delivered |
|---|---|---|
| Architecture and configuration | [Architecture](architecture.md), [topology/configuration](topology-and-configuration.md) | Physical, logical, realization, management, source ownership, mapping |
| Pytest execution | [Lifecycle](execution.md), [fixtures](fixtures-and-hosts.md), [PTF](packet-tests.md), [annotated test](annotated-test.md), [plugins](plugin-lifecycle.md) | Current runtime boundaries, auto-use, remote objects, cleanup, final outcome |
| Lab construction and scale | [Topology anatomy](topology-file-anatomy.md), [virtual](virtual-lab.md), [physical](physical-lab.md), [multi-DUT/ASIC](multi-dut-multi-asic.md) | Declaration-to-deployment gates, port identity, distributed ownership |
| Specialized environments | [CI](ci-and-reporting.md) through [SPyTest](spytest.md) | Added control services, compatibility boundaries, measurements, recovery |
| Navigation/reference | [Code tour](code-tour.md), [documentation map](documentation-map.md), [glossary](glossary.md) | Question-driven reading, existing-doc source trail, precise vocabulary |

Every substantive chapter now includes at least one of:

- an architecture/lifecycle diagram;
- a current-code trace;
- an explicit source table;
- a failure-boundary matrix;
- a state/identity checklist; or
- a worked operational sequence.

## Maintenance contract

This book synthesizes rather than owns most operational contracts. Update it
when a change alters cross-system understanding:

- testbed architecture or source-of-truth ownership;
- fixture/host-object cardinality;
- plugin lifecycle or final-result semantics;
- PTF/TGEN process or mapping boundaries;
- multi-DUT/ASIC/server identity;
- specialized service placement; or
- documentation navigation.

Update the adjacent authoritative reference first for:

- commands and prerequisites;
- YAML/inventory schemas;
- marker/option/API details;
- supported images/platforms;
- feature test cases; and
- CI/reporting credentials or deployment.

Then adjust this book's model and link.

## Definition of done for a chapter change

1. **Question** — state what confusion the chapter resolves.
2. **Sources** — link existing documentation and current implementation.
3. **Model** — separate systems, planes, phases, or ownership.
4. **Trace** — follow at least one value, call, packet, or state transition.
5. **Failures** — identify earliest observable boundaries.
6. **Cleanup** — explain restoration for mutating workflows.
7. **Versioning** — label platform/branch-specific behavior.
8. **Visual QA** — diagrams have title/description, readable text, and rendered
   inspection.
9. **Build QA** — `mdbook build` and `mdbook test` pass.
10. **Source QA** — links, code names, fixture scopes, and examples are checked
    against the current branch.

## Highest-value next additions

### Versioned failure case studies

Add small cases with immutable evidence:

- wrong PTF-to-DUT mapping;
- passing call followed by Log Analyzer failure;
- failed syncd/saiserver restoration;
- dual-ToR control/data state divergence;
- traffic-generator API success with no physical traffic; and
- multi-ASIC namespace flattening.

Each should include code/image/topology, timeline, evidence at each boundary,
root cause, fix, and prevention.

### Reproducible lab snapshots

Provide sanitized, versioned examples for:

- one small KVM T0;
- one physical T0 port from graph to capture;
- one multi-DUT/multi-server interface map; and
- one specialized API service.

Do not publish site credentials, private addresses, proprietary images, or
unreviewed lab configuration.

### Automated drift checks

Useful checks could validate:

- chapter links and referenced repository paths;
- fixture names/scopes cited in prose;
- topology example sections;
- SVG accessibility metadata and rendering;
- stale commands against `--help` snapshots where practical; and
- source chapters not included in `SUMMARY.md`.

Automation should flag review, not pretend that prose semantics are proven.

### Broader architecture tracks

Potential future chapters should be demand-driven: VOQ/chassis fabric,
WAN/OCS, NUT, Kubernetes testbed placement, cSONiC/VPP, or console/PDU
recovery. Add one only when it can explain a distinct architecture boundary
with current sources and a maintained owner.

## Known limits

- The book does not certify a lab, platform, or image.
- Commands and image details remain branch/environment specific.
- Not every feature test plan is summarized.
- Vendor-specific traffic-generator, ASIC, fanout, and DPU behavior remains in
  its owning documentation.
- Generated API pages can be incomplete; code remains the helper authority.
- CI allocation/dashboard systems outside the repository require their own
  references and access controls.

These limits are deliberate. A useful learning guide should make boundaries
clear without obscuring the maintained sources underneath.

## Contribution pattern

Start from an observed reader problem, not a desire for another topic. Link the
existing documentation, verify current code, add the smallest useful visual,
and show how to recognize failure at a boundary. Avoid copying a setup guide
into the book.
