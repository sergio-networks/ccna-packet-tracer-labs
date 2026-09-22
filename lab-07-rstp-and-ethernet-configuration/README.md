# Lab 07 — RSTP and Ethernet Configuration

## Objective

The purpose of this lab was to configure and verify Rapid Spanning Tree Protocol (RSTP) features on Cisco switches.

This lab focused on Rapid PVST+, controlling the Root Bridge election, configuring PortFast and BPDU Guard, verifying spanning-tree operation, intentionally triggering BPDU Guard, and recovering an err-disabled interface.

---

## Topology

```text
                    S1
               Primary Root
                  /    \
                 /      \
                S2------S3
                |
              Fa0/1
                |
               PC1
```

Three Cisco 2960 switches were connected using redundant Layer 2 links.

PC1 was connected to S2 Fa0/1 to demonstrate PortFast and BPDU Guard behavior on an edge port.

---

## Rapid PVST+ Configuration

Rapid PVST+ was enabled on S1, S2, and S3.

```text
configure terminal
spanning-tree mode rapid-pvst
end
```

The configuration was verified using:

```text
show spanning-tree
```

The output confirmed:

```text
Spanning tree enabled protocol rstp
```

This verified that the switches were operating using RSTP.

---

## Root Bridge Configuration

Initially, the Root Bridge was selected automatically based on the STP Bridge ID.

Instead of allowing the Root Bridge to be determined by the default Bridge IDs, S1 was intentionally configured as the preferred Root Bridge for VLAN 1.

On S1:

```text
configure terminal
spanning-tree vlan 1 root primary
end
```

Verification with:

```text
show spanning-tree
```

confirmed:

```text
This bridge is the root
```

S1's STP priority was lowered, allowing it to become the Root Bridge.

---

## Secondary Root Configuration

S2 was configured as the preferred secondary Root Bridge for VLAN 1.

```text
configure terminal
spanning-tree vlan 1 root secondary
end
```

The resulting STP priorities observed during the lab were:

```text
S1 → 24577  Primary Root
S2 → 28673  Secondary
S3 → 32769  Default
```

The displayed values include VLAN 1's extended system ID.

This created an intentional STP hierarchy rather than relying on the default MAC address tiebreaker.

---

## Understanding Root ID and Bridge ID

The `show spanning-tree` output contains two important sections.

### Root ID

```text
Root ID
```

This identifies the current Root Bridge.

On non-root switches such as S2, the Root ID displayed S1's information because S1 was the Root Bridge.

### Bridge ID

```text
Bridge ID
```

This identifies the local switch.

This distinction is useful when determining which switch is currently the root and which switch's CLI is being examined.

---

## PortFast Configuration

PC1 was connected to:

```text
S2 Fa0/1
```

Because PC1 is an end device and is not expected to participate in STP, PortFast was enabled on the interface.

```text
configure terminal
interface fa0/1
spanning-tree portfast
end
```

PortFast allows an edge port to transition rapidly to the forwarding state rather than waiting through the normal spanning-tree process.

PortFast does not disable STP.

PortFast should normally be used on ports connected to end devices rather than switch-to-switch links.

---

## BPDU Guard Configuration

BPDU Guard was enabled on S2 Fa0/1.

```text
configure terminal
interface fa0/1
spanning-tree bpduguard enable
end
```

BPDU Guard protects an edge port from an unexpected switch connection.

The expected topology was:

```text
PC1
 |
 |
Fa0/1
 |
S2
```

The port was expected to connect to an end device and therefore was not expected to receive BPDUs.

---

## Testing BPDU Guard

To test the protection mechanism, PC1 was temporarily disconnected and another Cisco switch, S4, was connected to S2 Fa0/1.

```text
S4 Fa0/1
    |
    |
S2 Fa0/1
PortFast + BPDU Guard
```

S4 began participating in spanning tree and sent BPDUs.

Because BPDU Guard was enabled on S2 Fa0/1, the unexpected BPDU caused the interface to be placed into an err-disabled state.

---

## Verifying the Failure

The interface status was checked using:

```text
show interfaces status
```

S2 reported:

```text
Fa0/1    err-disabled
```

This confirmed that BPDU Guard had protected the network by disabling the interface.

---

## Recovering the Interface

The unexpected switch was disconnected and PC1 was reconnected.

The interface was manually reset using:

```text
configure terminal
interface fa0/1
shutdown
no shutdown
end
```

The interface status was then verified again:

```text
show interfaces status
```

The result showed:

```text
Fa0/1    connected
```

The interface had successfully recovered from the err-disabled state.

---

## Important Commands

```text
spanning-tree mode rapid-pvst

spanning-tree vlan 1 root primary

spanning-tree vlan 1 root secondary

interface fa0/1
spanning-tree portfast

interface fa0/1
spanning-tree bpduguard enable

show spanning-tree

show spanning-tree vlan 1

show interfaces status

shutdown
no shutdown
```

---

## Key Concepts Practiced

- Rapid Spanning Tree Protocol (RSTP)
- IEEE 802.1w concepts
- Rapid PVST+
- Root Bridge election
- Bridge priority
- Root primary configuration
- Root secondary configuration
- Root ID vs. Bridge ID
- RSTP port roles and states
- PortFast
- Edge ports
- BPDU Guard
- BPDU processing
- Err-disabled interfaces
- Layer 2 loop protection
- Interface troubleshooting and recovery

---

## Key Takeaways

- RSTP provides faster spanning-tree convergence than traditional 802.1D STP.
- Rapid PVST+ allows Cisco switches to use RSTP on a per-VLAN basis.
- The Root Bridge can be intentionally controlled instead of relying on default Bridge IDs.
- Lower STP priority values are preferred during Root Bridge election.
- `Root ID` identifies the current Root Bridge.
- `Bridge ID` identifies the local switch.
- PortFast is designed for edge ports connected to end devices.
- PortFast does not disable spanning tree.
- BPDU Guard protects edge ports from unexpected STP devices.
- Receiving a BPDU on a BPDU Guard-enabled port can place the interface into an err-disabled state.
- After removing the cause of the BPDU Guard violation, an interface can be manually recovered using `shutdown` followed by `no shutdown`.

---

## Lab Result

**Successful**

Rapid PVST+ was successfully enabled across the three-switch topology.

S1 was intentionally configured as the primary Root Bridge and S2 as the secondary Root Bridge.

PortFast and BPDU Guard were configured on an edge port connected to PC1.

When another switch was intentionally connected to the protected port, BPDU Guard detected the incoming BPDU and placed the interface into an err-disabled state.

After removing the unexpected switch and resetting the interface, normal connectivity was restored.

This lab demonstrated both RSTP configuration and practical Layer 2 protection and troubleshooting techniques.

---

## Packet Tracer File

`lab-07-rstp-and-ethernet-configuration.pkt`
