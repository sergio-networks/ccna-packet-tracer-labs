# Lab 13 — OSPF Neighbors and DR/BDR Elections

## Overview
This lab focuses on OSPF neighbor formation, Designated Router (DR) and Backup Designated Router (BDR) elections, and OSPF recovery following a router failure.

**CCNA 200-301 | Chapter 24 | Cisco Packet Tracer**

## Objectives
- Configure OSPF on three routers in Area 0.
- Establish and verify OSPF neighbor adjacencies.
- Identify DR, BDR, and DROTHER roles.
- Configure OSPF interface priorities.
- Simulate DR failure and observe BDR promotion.
- Verify non-preemptive election behavior.

## Network Topology

```text
          R1              R2              R3
     192.168.10.1    192.168.10.2    192.168.10.3
           |               |               |
           |               |               |
           +---------------+---------------+
                           |
                          S1

              Network: 192.168.10.0/24
                    OSPF Area 0
```

### Devices
- 3 Cisco 2911 Routers
- 1 Cisco 2960 Switch
- Copper Straight-Through Cables

## IP Addressing Table

| Device | Interface | IP Address | Router ID | OSPF Priority |
|--------|-----------|------------|-----------|---------------|
| R1 | G0/0 | 192.168.10.1/24 | 1.1.1.1 | 1 |
| R2 | G0/0 | 192.168.10.2/24 | 2.2.2.2 | 1 |
| R3 | G0/0 | 192.168.10.3/24 | 3.3.3.3 | 200 |

## Router Configurations

### R1
```cisco
enable
configure terminal
hostname R1
interface gigabitEthernet 0/0
 ip address 192.168.10.1 255.255.255.0
 no shutdown
exit
router ospf 1
 router-id 1.1.1.1
 network 192.168.10.0 0.0.0.255 area 0
end
```

### R2
```cisco
enable
configure terminal
hostname R2
interface gigabitEthernet 0/0
 ip address 192.168.10.2 255.255.255.0
 no shutdown
exit
router ospf 1
 router-id 2.2.2.2
 network 192.168.10.0 0.0.0.255 area 0
end
```

### R3
```cisco
enable
configure terminal
hostname R3
interface gigabitEthernet 0/0
 ip address 192.168.10.3 255.255.255.0
 ip ospf priority 200
 no shutdown
exit
router ospf 1
 router-id 3.3.3.3
 network 192.168.10.0 0.0.0.255 area 0
end
```

## Verification Commands

```cisco
show ip interface brief
show ip ospf neighbor
show ip ospf interface gigabitEthernet 0/0
show ip route
```

## Lab Testing and Results

### Test 1 — Initial DR/BDR Election

OSPF was enabled on R1 first, followed by R2 and R3.

| Router | OSPF Role |
|--------|-----------|
| R1 | DR |
| R2 | BDR |
| R3 | DROTHER |

R3 was configured with OSPF priority 200 but did not replace the existing DR or BDR.

**Result: PASS**

### Test 2 — DR Failure

R1's G0/0 interface was shut down to simulate a network failure.

```cisco
configure terminal
interface gigabitEthernet 0/0
 shutdown
end
```

After OSPF detected the failure:
- R2 was promoted from BDR to DR.
- R3 became the new BDR.
- R2 and R3 maintained their OSPF adjacency.

**Result: PASS**

### Test 3 — Router Recovery

R1's G0/0 interface was restored.

```cisco
configure terminal
interface gigabitEthernet 0/0
 no shutdown
end
```

Final OSPF election results:

| Router | OSPF Role |
|--------|-----------|
| R1 | DROTHER |
| R2 | DR |
| R3 | BDR |

R1 successfully reestablished FULL adjacencies with R2 and R3 without reclaiming its previous DR role.

**Result: PASS**

## Routing Verification

All three routers were configured within the same directly connected network, 192.168.10.0/24.

The routers successfully communicated using directly connected routes without requiring OSPF-learned routes.

## Key Takeaways

- OSPF routers establish neighbor relationships using Hello packets.
- Broadcast Ethernet networks elect a DR and BDR.
- OSPF interface priority takes precedence over router ID during elections.
- DR/BDR elections are non-preemptive.
- When a DR fails, the BDR takes over.
- A returning router does not automatically reclaim its previous DR role.
- OSPF neighbor and interface verification commands are essential for troubleshooting.

## Lab Status

**Completed Successfully — All Tests Passed**
