# SAI and PTF testing

“PTF test” names an execution mechanism; “SAI test” names the interface under
test. They overlap, but they are not synonyms.

## Three useful layers

1. A normal sonic-mgmt pytest may use PTF to validate end-to-end SONiC
   behavior.
2. `tests/saitests/` contains PTF-side SAI-oriented tests and probing support.
3. SAI qualification workflows replace normal syncd with `saiserver`, install
   matching thrift bindings, and exercise SAI through RPC.

The third mode intentionally bypasses part of the normal SONiC orchestration
path. Its result should not be interpreted as an end-to-end SONiC feature test.

## SAI-server boundary

```text
pytest / test harness
  -> PTF SAI test and thrift client
  -> saiserver or RPC-capable syncd on DUT
  -> vendor SAI
  -> ASIC or virtual dataplane
```

Client bindings, SAI headers, server image, SONiC branch, and vendor SDK must be
compatible. A transport connection can succeed while method or attribute
versions are incompatible.

## Deployment and restoration

The repository includes `ansible/roles/test/tasks/saiserver.yml`, SAI-quality
scripts, and `ansible/swap_syncd.yml`. Before swapping containers, record the
default syncd image and service state. After testing, restore normal syncd and
run a health check; leaving saiserver active contaminates later SONiC tests.

## Evidence to preserve

- exact SONiC image and SAI header version;
- saiserver/syncd image identity;
- thrift package or client revision;
- PTF test case and parameters;
- RPC error or SAI status code;
- ASIC and sairedis logs;
- restoration and post-test health results.

## Exercise

Take one test under `tests/saitests/`. Identify whether it expects a normal
syncd, RPC syncd, or saiserver environment. Trace how it is copied to PTF, how
its client reaches the DUT, and which script restores the DUT afterward.

Deeper references: [SAI quality guide](https://github.com/sonic-net/sonic-mgmt/tree/master/docs/testbed/sai_quality),
[PTF SAIv2 testing guide](https://github.com/sonic-net/sonic-mgmt/blob/master/docs/testbed/sai_quality/PTF-SAIv2TestingGuide.md),
and [SAI tests](https://github.com/sonic-net/sonic-mgmt/tree/master/tests/saitests).
