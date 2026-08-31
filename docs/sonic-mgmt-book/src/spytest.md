# SPyTest architecture

SPyTest lives in the same repository but is a separate automation framework.
Do not expect pytest fixtures such as `duthosts`, `tbinfo`, or `ptfadapter` to
appear in a SPyTest module.

## Main layers

```text
test module
  -> feature APIs and reusable utilities
  -> SPyTest framework services
  -> CLI, REST, gNMI, or UI-specific access
  -> DUTs and traffic generators
```

Test modules focus on scenarios. APIs under the SPyTest trees abstract device
configuration and verification, including UI and version differences.
Utilities hold device-independent reusable logic. CLI output can be converted
to structured data using TextFSM templates.

## Testbed model

A SPyTest testbed YAML declares devices, access and credentials, build/config
profiles, links, and traffic-generator endpoints. Included YAML files can
provide services, parameters, builds, speeds, error patterns, and
instrumentation.

This schema is not the pytest `ansible/testbed.yaml` schema. Both describe a
lab, but their parsers, fields, and lifecycle are different.

## Execution lifecycle

SPyTest loads the testbed and framework configuration, establishes device
connections, applies initialization or configuration profiles, runs module and
function hooks, executes tests, classifies results, and writes logs/reports.
Traffic-generator integration is part of the framework when the topology
declares TGEN devices.

## How to learn it

1. Read `spytest/Doc/intro.md` and inspect one sample testbed.
2. Choose a small test module and list every framework/API call.
3. Follow one feature API to the access layer and parsing template.
4. Locate module/function setup and cleanup.
5. Read the generated result and log structure.

Avoid mechanically translating a pytest test line by line. First identify the
test intent, then implement it using the lifecycle and abstractions native to
the destination framework.

Deeper references: [SPyTest introduction](https://github.com/sonic-net/sonic-mgmt/blob/master/spytest/Doc/intro.md)
and [SPyTest documentation](https://github.com/sonic-net/sonic-mgmt/tree/master/spytest/Doc).
