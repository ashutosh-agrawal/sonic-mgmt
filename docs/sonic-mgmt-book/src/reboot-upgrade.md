# Reboot and upgrade testing

Reboot tests validate a transition, not just an end state. The important
questions are what traffic and control state existed before the reboot, what
happened during the outage, and whether the device returned to a trustworthy
state.

## Two levels of reboot testing

`tests/platform_tests/test_reboot.py` performs cold, soft, fast, warm, and
related reboot types, then checks reboot cause, critical processes,
interfaces, transceivers, and platform services.

`test_advanced_reboot.py` uses `get_advanced_reboot` and remote PTF support to
measure dataplane disruption. It can inject SAD-path conditions such as route,
neighbor, or link changes during reboot. That is a distributed scenario with
more setup and cleanup than the basic platform check.

## Lifecycle

```text
capture baseline
  -> prepare responders and continuous traffic
  -> trigger reboot or install
  -> observe disconnect and dataplane disruption
  -> wait for management and critical services
  -> verify interfaces, routes, neighbors, and traffic
  -> verify reboot cause and cleanup
```

“SSH is back” is only an intermediate checkpoint. Services may be restarting,
ports may still be down, and routing may not have converged.

## Upgrade adds another state dimension

Upgrade tests must record both source and target image, boot choice, available
disk space, migration expectations, and rollback plan. Secure-upgrade tests
also validate trust and image-verification behavior. Never infer success from
the installer command alone; verify the running version after reboot.

## Operational safeguards

- Reserve the testbed for the full recovery window.
- Confirm console or power access before disruptive testing.
- Save the current image and configuration.
- Use platform-specific timeout overrides when documented.
- Preserve continuous-traffic artifacts and reboot timing.
- Do not run a new case until post-reboot health is clean.

## Failure classification

Distinguish trigger failure, expected disconnect, management return timeout,
service readiness failure, dataplane loss-budget failure, state restoration
failure, and teardown failure. They have different owners and should not be
collapsed into “reboot failed.”

## Exercise

Read `test_cold_reboot` through `reboot_and_check`, then list every readiness
condition checked after SSH returns. Repeat for one advanced reboot and mark
which observations come from PTF rather than the DUT.

Deeper references: [basic reboot tests](https://github.com/sonic-net/sonic-mgmt/blob/master/tests/platform_tests/test_reboot.py),
[advanced reboot tests](https://github.com/sonic-net/sonic-mgmt/blob/master/tests/platform_tests/test_advanced_reboot.py),
and [advanced reboot fixture](https://github.com/sonic-net/sonic-mgmt/blob/master/tests/common/fixtures/advanced_reboot.py).
