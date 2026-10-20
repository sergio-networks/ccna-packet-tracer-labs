
# Lab 09 — Layer 3 Switching with SVIs

## Topology
- 1 Cisco 3560 Multilayer Switch (MLS1)
- 2 PCs (PC1 and PC2)
- PC1 connected to MLS1 Fa0/1
- PC2 connected to MLS1 Fa0/2
- VLAN 10 (SALES) and VLAN 20 (IT)
- Inter-VLAN routing performed by MLS1 using SVIs

## Network Rules

| Device | Interface | IP Address | VLAN | Default Gateway |
|---|---|---|---|---|
| MLS1 | Vlan10 | 192.168.10.1/24 | 10 | N/A |
| MLS1 | Vlan20 | 192.168.20.1/24 | 20 | N/A |
| PC1 | Fa0 | 192.168.10.10/24 | 10 | 192.168.10.1 |
| PC2 | Fa0 | 192.168.20.10/24 | 20 | 192.168.20.1 |

## Steps
1. Created a topology using one Cisco 3560 multilayer switch and two PCs.
2. Configured PC1 and PC2 with their respective IPv4 addresses, subnet masks, and default gateways.
3. Changed the switch hostname to MLS1.
4. Created VLAN 10 (SALES) and VLAN 20 (IT).
5. Assigned Fa0/1 to VLAN 10 and Fa0/2 to VLAN 20 as access ports.
6. Configured SVI Vlan10 with IP address 192.168.10.1/24.
7. Configured SVI Vlan20 with IP address 192.168.20.1/24.
8. Enabled both SVIs using `no shutdown`.
9. Enabled Layer 3 routing using `ip routing`.
10. Verified SVI status, connected routes, and inter-VLAN connectivity.

## Verification
- `show vlan brief` confirmed VLAN 10 and VLAN 20 were active.
- `show ip interface brief` confirmed Vlan10 and Vlan20 were up/up.
- `show ip route` confirmed connected routes for both VLAN networks.
- PC1 successfully pinged its default gateway at 192.168.10.1.
- PC2 successfully pinged its default gateway at 192.168.20.1.
- PC1 successfully pinged PC2 at 192.168.20.10.

**Troubleshooting:** Practiced using `do show ip interface brief` to execute a verification command while in global configuration mode.

**Result: PASS — Successful inter-VLAN routing using a multilayer switch.*

## Commands

### Multilayer Switch Configuration
```cisco
enable
configure terminal
hostname MLS1

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

interface vlan 10
 ip address 192.168.10.1 255.255.255.0
 no shutdown

interface vlan 20
 ip address 192.168.20.1 255.255.255.0
 no shutdown

ip routing

end
copy running-config startup-config
```

### Verification Commands
```cisco
show vlan brief
show ip interface brief
show ip route
ping 192.168.10.1
ping 192.168.20.1
ping 192.168.20.10
```
