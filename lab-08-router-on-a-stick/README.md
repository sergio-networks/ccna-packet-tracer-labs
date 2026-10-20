# Lab 08 — Router-on-a-Stick (Inter-VLAN Routing)

## Topology
- 1 Cisco 2911 Router (R1)
- 1 Cisco 2960 Switch (S1)
- 2 PCs (PC1 and PC2)
- PC1 connected to S1 Fa0/1
- PC2 connected to S1 Fa0/2
- S1 G0/1 connected to R1 G0/0

## Network Rules

| Device | Interface | IP Address | VLAN | Default Gateway |
|---|---|---|---|---|
| R1 | G0/0.10 | 192.168.10.1/24 | 10 | N/A |
| R1 | G0/0.2 | 192.168.20.1/24 | 20 | N/A |
| PC1 | Fa0 | 192.168.10.10/24 | 10 | 192.168.10.1 |
| PC2 | Fa0 | 192.168.20.10/24 | 20 | 192.168.20.1 |

## Steps
1. Created VLAN 10 (SALES) and VLAN 20 (IT) on S1.
2. Assigned Fa0/1 to VLAN 10 and Fa0/2 to VLAN 20.
3. Configured S1 G0/1 as an 802.1Q trunk.
4. Enabled R1 G0/0 using `no shutdown`.
5. Configured R1 G0/0.10 with VLAN 10 encapsulation and IP address 192.168.10.1/24.
6. Configured R1 G0/0.2 with VLAN 20 encapsulation and IP address 192.168.20.1/24.
7. Verified trunk operation, interface status, and routing table entries.
8. Tested connectivity between VLAN 10 and VLAN 20.

## Verification
- `show vlan brief` confirmed VLAN 10 and VLAN 20 were active.
- `show interfaces trunk` confirmed S1 G0/1 was trunking with 802.1Q.
- `show ip interface brief` confirmed both router subinterfaces were up/up.
- `show ip route` confirmed connected and local routes for both VLAN subnets.
- PC1 and PC2 successfully pinged their respective gateways.
- PC1 successfully pinged PC2 across VLANs.

**Troubleshooting:** Initially, the trunk did not appear because R1 G0/0 was administratively down. Enabling the interface brought the trunk online.

**Result: PASS — Inter-VLAN routing successful.**

## Commands

### Switch
```cisco
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
interface g0/1
 switchport mode trunk
```

### Router
```cisco
interface g0/0
 no shutdown
interface g0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
interface g0/0.2
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
```

### Verification Commands
```cisco
show vlan brief
show interfaces trunk
show ip interface brief
show ip route
ping 192.168.20.10
```
