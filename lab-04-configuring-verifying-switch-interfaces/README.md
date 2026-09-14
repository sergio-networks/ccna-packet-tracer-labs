# Lab 04 – Configuring and Verifying Switch Interfaces

## Objective

The purpose of this lab was to practice configuring, verifying, and troubleshooting Cisco switch interfaces using Cisco IOS.

The lab focused on identifying interface states, configuring duplex settings, understanding autonegotiation, troubleshooting physical connectivity problems, and using IOS verification commands to diagnose switch port issues.

---

## Topology

```text
             S1
          /   |   \
        PC1  PC2  PC3
```

### Devices

- 1 Cisco 2960 Switch
- 3 PCs

---

## IP Addressing

| Device | IP Address | Subnet Mask |
|---|---|---|
| PC1 | 192.168.10.10 | 255.255.255.0 |
| PC2 | 192.168.10.20 | 255.255.255.0 |
| PC3 | 192.168.10.30 | 255.255.255.0 |

No default gateway was required because all three hosts were located on the same subnet and no router was used in this lab.

---

## Initial Interface Verification

The switch interfaces were first checked using:

```text
show interfaces status
```

This provided a quick summary of each switch port, including:

- Interface status
- VLAN
- Duplex
- Speed
- Interface type

The connected interfaces initially showed successful autonegotiation.

Example:

```text
Fa0/1    connected    1    a-full    a-100
Fa0/2    connected    1    a-full    a-100
Fa0/3    connected    1    a-full    a-100
```

The `a-` prefix indicates that the duplex and speed were determined through autonegotiation.

---

## Troubleshooting an Administratively Disabled Interface

Fa0/2 was intentionally disabled to simulate a configuration-related interface failure.

```text
configure terminal
interface fa0/2
shutdown
```

The switch reported that the interface had changed state.

Detailed interface information was checked with:

```text
show interfaces fa0/2
```

The interface displayed:

```text
FastEthernet0/2 is administratively down, line protocol is down
```

This indicates that the interface was intentionally disabled through configuration.

The interface was restored using:

```text
configure terminal
interface fa0/2
no shutdown
```

After verification, the interface returned to:

```text
FastEthernet0/2 is up, line protocol is up
```

---

## Running Show Commands From Configuration Mode

During the lab, I practiced the difference between IOS configuration modes and privileged EXEC mode.

Normal `show` commands are typically executed from:

```text
Switch#
```

When working inside configuration mode, the `do` command can be used to execute an EXEC command without leaving configuration mode.

Example:

```text
Switch(config-if)# do show interfaces fa0/3
```

This was useful for verifying interface changes immediately after configuration.

---

## Configuring Duplex

Fa0/3 was manually configured for half-duplex operation:

```text
interface fa0/3
duplex half
```

The operational settings were verified using:

```text
do show interfaces fa0/3
```

The interface reported:

```text
Half-duplex, 100Mb/s
```

The interface was then returned to automatic duplex negotiation:

```text
duplex auto
```

After verification, the interface returned to full-duplex operation.

---

## Autonegotiation

The command:

```text
show interfaces status
```

displayed values such as:

```text
a-full
a-100
```

These indicate that the switch automatically negotiated:

- Full-duplex operation
- 100 Mbps speed

Autonegotiation allows connected Ethernet devices to determine compatible operational settings without requiring manual configuration.

---

## Physical Connectivity Troubleshooting

A simulated troubleshooting scenario was used where PC2 could no longer communicate with the LAN.

Fa0/2 showed:

```text
notconnect
```

This is different from an administratively disabled interface.

### `notconnect`

The interface is enabled, but the switch does not detect a working physical connection.

Possible causes include:

- Loose cable
- Damaged cable
- Disconnected device
- Disabled NIC
- Powered-off device

### `administratively down`

The interface has been intentionally disabled using the IOS `shutdown` command.

This distinction is important when deciding whether to investigate the physical connection or the switch configuration.

---

## Duplex Mismatch Troubleshooting

Another troubleshooting scenario involved a link that remained connected but experienced poor performance.

A potential duplex mismatch can occur when the two sides of an Ethernet connection operate with incompatible duplex settings.

Useful verification commands include:

```text
show interfaces status
```

and:

```text
show interfaces fa0/3
```

Detailed interface output can be inspected for indicators such as:

- Input errors
- CRC errors
- Collisions
- Late collisions
- Operational duplex
- Operational speed

In Packet Tracer, some physical error counters may not reproduce real-world duplex mismatch behavior exactly, so operational speed and duplex settings were also used during troubleshooting.

---

## Final Connectivity Verification

After restoring the switch interfaces, connectivity was tested between the PCs.

From PC1:

```text
ping 192.168.10.20
ping 192.168.10.30
```

Both hosts were reachable, confirming successful LAN connectivity.

---

## Key Commands

```text
show interfaces status
show interfaces fa0/2
show interfaces fa0/3

configure terminal
interface fa0/2
shutdown
no shutdown

interface fa0/3
duplex half
duplex auto

do show interfaces fa0/3
```

---

## Troubleshooting Workflow

A useful troubleshooting process reinforced during this lab was:

```text
Check Interface Status
        ↓
Identify Suspicious Port
        ↓
Check Physical Connection
        ↓
Verify Shutdown State
        ↓
Check Speed and Duplex
        ↓
Inspect Error Counters
        ↓
Correct the Problem
        ↓
Verify Connectivity
```

---

## Key Takeaways

- `show interfaces status` provides a quick overview of switch port status, duplex, and speed.
- `show interfaces <interface>` provides detailed information and error counters for a specific interface.
- `administratively down` indicates an interface has been disabled through configuration.
- `notconnect` indicates an enabled interface that is not detecting a working physical link.
- `shutdown` disables an interface.
- `no shutdown` enables an interface.
- `duplex auto` allows duplex autonegotiation.
- A link being up does not necessarily mean the link is healthy.
- Speed and duplex should be verified when troubleshooting poor Ethernet performance.
- Physical Layer problems should be investigated before moving higher in the networking stack.
- `do` allows EXEC commands such as `show` to be executed while remaining in configuration mode.

---

## Skills Practiced

- Cisco IOS navigation
- Switch interface configuration
- Interface status verification
- Physical Layer troubleshooting
- Speed and duplex verification
- Ethernet autonegotiation
- Interface error analysis
- Cisco IOS troubleshooting workflow
- End-to-end connectivity testing

---

## Lab Result

**Successful**

All three PCs were able to communicate after the troubleshooting scenarios were completed and the interfaces were restored to their correct operational state.
