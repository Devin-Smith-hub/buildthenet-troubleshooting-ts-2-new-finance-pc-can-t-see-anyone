# Troubleshooting #2: New Finance PC Can't See Anyone

_Level: Easy — BuildTheNet 3D Sandbox troubleshooting — IP addressing_

**Status: Verified.** Completed on 2026-11-23; every requirement was machine-checked against the network built in BuildTheNet and passed.

Snapshot taken 2026-11-23. Built in a simulation — no physical or production equipment was involved.

## The ticket

**Reported symptom:** PC-2 can't reach other PCs, the printer server or its gateway; everyone else is fine.

Helpdesk Ticket #102: PC-2 was set up by hand this morning in Finance, on the same switch as everyone else. It cannot reach PC-1, the gateway, or Print-Server. PC-1 and PC-3 on the same switch have no problems.

_Area: IP addressing_

## Required resolution

Correct PC-2's own network settings so it reaches PC-1 and Print-Server, without touching any other device.

## Checks

| # | Check | Status | Checked by |
|---|---|---|---|
| 1 | PC-1 to PC-2 works | Passed | Sandbox check |
| 2 | PC-2 to Print-Server works | Passed | Sandbox check |
| 3 | PC-1 to PC-3 still works | Passed | Sandbox check |
| 4 | PC-1 to Print-Server still works | Passed | Sandbox check |
| 5 | PC-1 keeps its documented addressing | Passed | Sandbox check |
| 6 | PC-3 keeps its documented addressing | Passed | Sandbox check |
| 7 | Router-Fin keeps its documented addressing | Passed | Sandbox check |
| 8 | Print-Server keeps its documented addressing | Passed | Sandbox check |
| 9 | PC-2 FastEthernet0 keeps its documented addressing | Passed | Sandbox check |
| 10 | The ticket's service requirement is met | Passed | Sandbox check |

## Root cause

PC-2 uses mask 255.255.255.240 on a /24 segment, so PC-1 and the gateway are outside its own subnet.

## The fix

What was changed to resolve the ticket, compared with the network as it was reported:

Compared with the starting topology, the learner changed the configuration of 1 device.

**PC-2**

```diff
  interface FastEthernet0
-  ip address 192.168.10.50 255.255.255.240
+  ip address 192.168.10.50 255.255.255.0
```

## Result

Every check passed on this snapshot:

> PC-2 uses the segment's mask again: it reaches PC-1 and Print-Server, and nobody else was affected.

## Topology

![Topology diagram](topology.svg)

## Addressing

| Device | Interface | Address | Mask | Mode | Gateway |
|---|---|---|---|---|---|
| PC-1 | FastEthernet0 | 192.168.10.10/24 | 255.255.255.0 | VLAN 1 | 192.168.10.1 |
| PC-2 | FastEthernet0 | 192.168.10.50/24 | 255.255.255.0 | VLAN 1 | 192.168.10.1 |
| PC-3 | FastEthernet0 | 192.168.10.12/24 | 255.255.255.0 | VLAN 1 | 192.168.10.1 |
| Router-Fin | GigabitEthernet0/0 | 192.168.10.1/24 | 255.255.255.0 | routed |  |
| Router-Fin | GigabitEthernet0/1 | 10.20.0.1/24 | 255.255.255.0 | routed |  |
| Print-Server | FastEthernet0 | 10.20.0.30/24 | 255.255.255.0 | VLAN 1 | 10.20.0.1 |

## Device configurations

Running-configs as `show running-config` prints them in the BuildTheNet sandbox. Passwords, secrets, keys and SNMP communities are replaced with `<removed>`.

- [SW-Finance](configs/SW-Finance.txt)
- [Router-Fin](configs/Router-Fin.txt)

---

Built on [BuildTheNet](https://www.buildthenet.io/sandbox)
