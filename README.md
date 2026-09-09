# CCNA Packet Tracer Labs

This repository documents my hands-on Cisco Packet Tracer labs as I study for the Cisco CCNA 200-301 certification.

My goal is to use these labs to reinforce networking concepts, practice Cisco IOS commands, troubleshoot configurations, and track my progress as I prepare for the CCNA exam.

## Lab Progress

| Lab | Topic | Status |
|---|---|---|
| Lab 01 | Basic Cisco IOS & Switch Configuration | ✅ Completed |
| Lab 02 | Ethernet Switching, ARP & Basic Routing | ✅ Completed |
| Lab 03 | Static Routing | 🔜 Next |

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

## Current Lab Network

```text
PC1 ─┐
PC2 ─┼── S1 ─── R1 ─── S2 ─── PC4
PC3 ─┘

LAN 1: 192.168.1.0/24
LAN 2: 192.168.2.0/24

R1 G0/0: 192.168.1.1
R1 G0/1: 192.168.2.1
