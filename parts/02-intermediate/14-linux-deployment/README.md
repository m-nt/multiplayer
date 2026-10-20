# I14 — Deployment Fundamentals

[Part overview](../README.md) · [Previous](../13-multiple-server-instances/README.md) · [Next](../15-security-and-abuse/README.md)

## Practicality and scope

Keep deployment required but allow an instructor-managed Linux host; do not make buying cloud infrastructure a prerequisite.

**Time:** 3–4 hours. **Prerequisite:** I13 and its passing lab.

**By the end:** Deploy the server securely enough for the controlled lab and diagnose cross-network reachability.

**Boundary:** One supervised Linux deployment; full automated fleet release is advanced.

## Teaching notes

Trace the path: client name resolution → route → firewall → listening interface → TLS endpoint → application admission. Binding only to loopback prevents remote access. Binding to all interfaces does not open an external firewall. NAT or carrier-grade NAT may prevent unsolicited inbound connectivity; use the prepared host or approved tunnel rather than assuming port forwarding is available.

Run under a dedicated service identity, configure restart policy and logs, and separate configuration from secrets. For the WebSocket path use WSS with certificate validation; a proxy must support the upgrade path and suitable connection timeouts. Never fix certificate errors by globally disabling validation.

Readiness follows successful configuration and initialization. On stop, withdraw new admission and apply the active-match policy. A restart can restore a process without restoring in-memory gameplay.

## Worked example

A health HTTP request succeeds but WSS fails because the proxy does not forward the upgrade correctly. This is distinct from a valid WebSocket connection rejected by game admission.

## Guided lab

1. Deploy a versioned binary to the prepared Linux host and document startup.
2. Configure TLS, restricted exposed ports, logs, and supervised lifecycle.
3. Connect two clients from a different network or managed remote runner.
4. Break a port, certificate hostname, and admission ticket separately; diagnose each layer.

## Evidence required to pass

- Remote clients complete a match without bypassing TLS verification.
- The runbook reproduces startup and log inspection.
- Each injected failure is attributed to the correct layer.
- No secrets appear in repository files or submitted logs.

## Explain independently

Why can a public HTTP health endpoint work while gameplay remains unreachable?

## Reading and preparation

GO-NET and WEBSOCKET; official documentation for the instructor-selected Linux supervisor and TLS proxy. See the [source guide](../../../SOURCES.md) for assigned sections and the [teaching guide](../../../TEACHING-GUIDE.md) for lab setup.
