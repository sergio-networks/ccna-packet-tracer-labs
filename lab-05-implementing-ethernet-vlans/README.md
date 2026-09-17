# Lab 05 — Implementing Ethernet VLANs

## Objective

The purpose of this lab was to configure and verify Ethernet VLANs across two Cisco switches.

This lab focused on creating VLANs, assigning switch access ports to VLANs, configuring an 802.1Q trunk between switches, verifying trunk operation, and testing communication between devices in the same and different VLANs.

---

## Topology

```text
              802.1Q Trunk
           VLAN 10 + VLAN 20
          Gi0/1         Gi0/1
             ═══════════
            S1          S2
           /  \        /  \
       Fa0/1  Fa0/2  Fa0/1  Fa0/2
         |      |      |      |
        PC1    PC2    PC3    PC4
         |      |      |      |
      VLAN10 VLAN20 VLAN10 VLAN20
```

---

## VLAN Configuration

| VLAN | Name | Devices |
|------|------|---------|
| 10 | SALES | PC1, PC3 |
| 20 | IT | PC2, PC4 |

---

## IP Addressing

| Device | IP Address | Subnet Mask | VLAN |
|--------|------------|-------------|------|
| PC1 | 192.168.10.10 | 255.255.255.0 | 10 |
| PC2 | 192.168.20.20 | 255.255.255.0 | 20 |
| PC3 | 192.168.10.30 | 255.255.255.0 | 10 |
| PC4 | 192.168.20.40 | 255.255.255.0 | 20 |

No default gateways were configured because this lab did not include inter-VLAN routing.

---

## Switch Port Assignments

### S1

| Interface | Mode | VLAN |
|-----------|------|------|
| Fa0/1 | Access | VLAN 10 — SALES |
| Fa0/2 | Access | VLAN 20 — IT |
| Gi0/1 | Trunk | VLAN 10 & VLAN 20 |

### S2

| Interface | Mode | VLAN |
|-----------|------|------|
| Fa0/1 | Access | VLAN 10 — SALES |
| Fa0/2 | Access | VLAN 20 — IT |
| Gi0/1 | Trunk | VLAN 10 & VLAN 20 |

---

## VLAN Creation

VLAN 10 and VLAN 20 were created on both switches.

```text
configure terminal

vlan 10
 name SALES
 exit

vlan 20
 name IT
 exit
```

The VLAN configuration was verified with:

```text
show vlan brief
```

---

## Access Port Configuration

Fa0/1 was configured as an access port for VLAN 10:

```text
interface fa0/1
 switchport mode access
 switchport access vlan 10
```

Fa0/2 was configured as an access port for VLAN 20:

```text
interface fa0/2
 switchport mode access
 switchport access vlan 20
```

This configuration was completed on both S1 and S2.

---

## 802.1Q Trunk Configuration

The GigabitEthernet0/1 interfaces connecting S1 and S2 were configured as trunk ports.

```text
interface gi0/1
 switchport mode trunk
```

This configuration allows traffic from multiple VLANs to cross the same physical link between the switches.

The trunk was verified using:

```text
show interfaces trunk
```

The output confirmed that Gi0/1 was operating as an 802.1Q trunk and that VLANs 10 and 20 were active across the trunk.

---

## Connectivity Testing

### VLAN 10

PC1:

```text
192.168.10.10
```

successfully pinged PC3:

```text
192.168.10.30
```

Result:

```text
SUCCESS
```

This confirmed that VLAN 10 traffic could travel between S1 and S2 across the trunk.

### VLAN 20

PC2:

```text
192.168.20.20
```

successfully pinged PC4:

```text
192.168.20.40
```

Result:

```text
SUCCESS
```

This confirmed that VLAN 20 traffic could also travel between the switches using the same trunk link.

---

## Inter-VLAN Connectivity Test

A ping was attempted between devices in different VLANs.

Example:

```text
PC1 — VLAN 10
192.168.10.10

        X

PC4 — VLAN 20
192.168.20.40
```

The ping failed as expected.

VLAN 10 and VLAN 20 are separate Layer 2 broadcast domains and separate IP networks.

A trunk can transport traffic for multiple VLANs, but it does not route traffic between VLANs.

Communication between VLAN 10 and VLAN 20 would require inter-VLAN routing using a router or Layer 3 switch.

---

## Important Commands

```text
vlan 10
name SALES

vlan 20
name IT

interface fa0/1
switchport mode access
switchport access vlan 10

interface fa0/2
switchport mode access
switchport access vlan 20

interface gi0/1
switchport mode trunk

show vlan brief
show interfaces trunk
```

---

## Key Concepts Learned

- VLANs logically separate Layer 2 networks.
- Access ports normally carry traffic for a single VLAN.
- Trunk ports carry traffic for multiple VLANs.
- IEEE 802.1Q identifies VLAN membership for traffic crossing a trunk.
- The same VLAN can span multiple switches through a trunk.
- Devices in the same VLAN and IP subnet can communicate across switches when trunking is configured correctly.
- Different VLANs cannot communicate through Layer 2 switching alone.
- Communication between different VLANs requires Layer 3 routing.
- A default gateway provides hosts with a path to destinations outside their local subnet.
- `show vlan brief` verifies VLANs and access-port membership.
- `show interfaces trunk` verifies trunk operation and VLAN information.

---

## Lab Result

**Successful**

VLAN 10 and VLAN 20 were successfully configured across two Cisco switches.

Both VLANs were able to communicate between switches using a single 802.1Q trunk while remaining logically separated from each other.

This lab demonstrated the difference between access links, trunk links, VLAN separation, and Layer 3 inter-VLAN routing.

---

## Packet Tracer File

`lab-05-implementing-ethernet-vlans.pkt`
