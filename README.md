# CCNA Packet Tracer Labs

This repository documents my hands-on Cisco Packet Tracer labs as I study for the Cisco CCNA 200-301 certification.

My goal is to use these labs to reinforce networking concepts, practice Cisco IOS commands, troubleshoot configurations, and track my progress as I prepare for the CCNA exam.

## Lab Progress

| Lab | Topic | Status |
|---|---|---|
| Lab 01 | Basic Cisco IOS & Switch Configuration | ✅ Completed |
| Lab 02 | Ethernet Switching, ARP & Basic Routing | ✅ Completed |
| Lab 03 | Static Routing | ✅ Completed |
| Lab 04 | Configuring and Verifying Switch Interfaces | ✅ Completed |
| Lab 05 | Implementing Ethernet VLANs | ✅ Completed |
| Lab 06 | Spanning Tree Protocol | ✅ Completed |
| Lab 07 | RSTP and Ethernet Configuration | ✅ Completed |

## Skills Practiced

- Cisco IOS command-line navigation
- User EXEC, Privileged EXEC, and Configuration modes
- Switch MAC address tables
- ARP and ARP tables
- Ethernet frame forwarding
- Unknown unicast flooding
- IPv4 addressing
- Default gateways
- Router interface configuration
- `shutdown` and `no shutdown`
- Connected and local routes
- Basic routing between different subnets
- Saving running configurations to NVRAM
- Troubleshooting with `ping`
- `show ip route`
- `show ip arp`
- `show ip interface brief`
- `show mac address-table`
- Static IPv4 routing
- Next-hop IP addressing
- Multi-router connectivity and troubleshooting
- Switch interface configuration and verification
- Ethernet speed and duplex configuration
- Ethernet autonegotiation
- Physical Layer troubleshooting
- Interface status and error analysis
- Troubleshooting `notconnect` vs `administratively down`
- Using `show interfaces status` and `show interfaces`
- Spanning Tree Protocol (STP)
- Layer 2 loop prevention
- Root bridge election
- Bridge IDs and STP priority
- Root, designated, and alternate port roles
- STP path cost
- STP port states
- STP convergence and reconvergence
- Redundant Layer 2 network design
- Rapid Spanning Tree Protocol (RSTP)
- Rapid PVST+
- Root bridge primary and secondary configuration
- RSTP verification and troubleshooting
- PortFast and edge ports
- BPDU Guard
- Err-disabled interface troubleshooting and recovery

## Lab Topologies

### Lab 01 — Basic IOS & Switch Configuration

```text
PC ─── S1
```

Cisco IOS navigation, basic switch configuration, passwords, and saving configurations.

### Lab 02 — Ethernet, ARP & Basic Routing

```text
PC1 ─┐
PC2 ─┼── S1 ── R1 ── S2 ── PC4
PC3 ─┘
```

Ethernet switching, MAC address learning, ARP, default gateways, and routing between two LANs.

### Lab 03 — Static Routing

```text
PC1 ─┐
PC2 ─┼── S1 ── R1 ───── R2 ── S3 ── PC5
PC3 ─┘          │
                S2
                │
               PC4
```

Multi-router topology using a `/30` transit network and static IPv4 routes to provide connectivity between remote networks.

### Lab 04 — Switch Interface Troubleshooting

```text
             S1
          /   |   \
        PC1  PC2  PC3
```

Switch interface configuration and troubleshooting involving interface states, physical connectivity, speed, duplex, autonegotiation, and error verification.

### Lab 05 — Implementing Ethernet VLANs

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

Two-switch VLAN topology using access ports and an 802.1Q trunk to extend VLAN 10 and VLAN 20 across both switches while maintaining Layer 2 separation between the VLANs.

### Lab 06 — Spanning Tree Protocol

```text
                 S1
            Root Bridge
              /    \
             /      \
            S2------S3
```

Spanning Tree Protocol, root bridge election, STP port roles and states, redundant Layer 2 paths, and STP reconvergence after a link failure.

### Lab 07 — RSTP and Ethernet Configuration

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

Three-switch Rapid PVST+ topology used to configure an intentional primary and secondary Root Bridge, verify RSTP operation, and demonstrate PortFast and BPDU Guard protection on an edge port. BPDU Guard was tested by connecting an unexpected switch and recovering the resulting err-disabled interface.
