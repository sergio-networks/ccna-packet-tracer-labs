
# Lab 12 — Implementing Optional OSPF Features

**CCNA 200-301 | Chapter 23 | Cisco Packet Tracer**

## Lab Overview

In this lab, I configured and verified optional OSPF features in a three-router network using Cisco Packet Tracer.

The objective was to strengthen my understanding of OSPF route advertisement, interface costs, reference bandwidth, and routing behavior.

## Lab Objectives

- Configure passive OSPF interfaces
- Advertise a default route into OSPF
- Verify OSPF external Type 2 routes
- Manually modify OSPF interface costs
- Configure OSPF reference bandwidth
- Verify OSPF neighbor relationships
- Test end-to-end network connectivity

## Network Topology

The topology consisted of three routers running OSPF in area 0, with R2 connected to an ISP.

```text
R1 -------- R2 -------- R3
             |
            ISP
```

R1 and R3 provided connectivity to their respective LAN networks.

## Configuration Steps

### 1. Configure Passive Interfaces

Configured LAN-facing interfaces as passive to prevent unnecessary OSPF Hello packets and neighbor formation.

```cisco
router ospf 1
 passive-interface g0/1
```

Passive interfaces continue advertising their connected networks through OSPF without forming neighbor adjacencies.

### 2. Configure Default Route Advertisement

Configured a static default route on R2 toward the ISP.

```cisco
ip route 0.0.0.0 0.0.0.0 203.0.113.2
```

Advertised the default route through OSPF.

```cisco
router ospf 1
 default-information originate
```

R1 and R3 successfully learned the default route as an OSPF external Type 2 route.

```text
O*E2 0.0.0.0/0
```

### 3. Configure Manual OSPF Interface Cost

Modified the OSPF interface cost on R1.

```cisco
interface g0/0
 ip ospf cost 25
```

After the configuration change, the OSPF route metric from R1 to R3's LAN increased from 3 to 27.

This demonstrated how interface costs influence OSPF routing metrics.

### 4. Configure OSPF Reference Bandwidth

Updated the OSPF reference bandwidth on all three routers.

```cisco
router ospf 1
 auto-cost reference-bandwidth 10000
```

This configured a reference bandwidth of 10,000 Mbps.

The same reference bandwidth was applied to R1, R2, and R3 to maintain consistent OSPF cost calculations.

## Verification Commands

### Verify OSPF Neighbors

```cisco
show ip ospf neighbor
```

**Result:** OSPF neighbor relationships reached the FULL state.

### Verify Routing Table

```cisco
show ip route
```

**Result:**
- Internal OSPF routes were successfully learned.
- R1 and R3 learned the default route advertised by R2.
- The manually configured interface cost affected the OSPF route metric.

### Verify OSPF Interface Costs

```cisco
show ip ospf interface
```

| Router | Interface | OSPF Cost |
|--------|-----------|-----------|
| R1 | G0/0 | 25 |
| R1 | G0/1 | 10 |
| R2 | G0/0 | 10 |
| R2 | G0/1 | 10 |
| R3 | G0/0 | 10 |
| R3 | G0/1 | 10 |

R1 G0/0 retained its manually configured cost of 25 despite the updated reference bandwidth.

### Verify Connectivity

Used `ping` to verify connectivity between the LAN networks.

**Result:** End-to-end LAN connectivity was successful.

## Troubleshooting and Observations

**Manual OSPF Cost Override**

A manually configured OSPF interface cost overrides the automatically calculated cost.

For example:

```cisco
ip ospf cost 25
```

Even if the reference bandwidth calculation produces a cost of 10, OSPF uses the manually configured cost of 25.

**Default Route Advertisement**

The `default-information originate` command advertises a default route into OSPF when the advertising router has an existing default route.

**Reference Bandwidth Consistency**

Reference bandwidth should be configured consistently across OSPF routers to avoid undesirable routing decisions.

## Key Commands

| Command | Purpose |
|---------|---------|
| `passive-interface` | Prevents OSPF neighbor formation |
| `default-information originate` | Advertises a default route |
| `ip ospf cost` | Manually changes interface cost |
| `auto-cost reference-bandwidth` | Adjusts automatic OSPF cost calculations |
| `show ip ospf neighbor` | Verifies OSPF adjacencies |
| `show ip ospf interface` | Displays OSPF interface settings |
| `show ip route` | Displays the routing table |
| `ping` | Tests network connectivity |

## Key Takeaways

- Passive interfaces prevent OSPF neighbor formation while continuing to advertise connected networks.
- Default routes can be distributed through OSPF using `default-information originate`.
- OSPF external Type 2 routes appear as `O E2` in the routing table.
- Manually configured OSPF costs override automatic cost calculations.
- Reference bandwidth should remain consistent across routers.
- OSPF interface costs influence route metrics and path selection.

## Lab Completion

- [x] Configured OSPF passive interfaces
- [x] Configured a static default route toward the ISP
- [x] Advertised the default route through OSPF
- [x] Verified OSPF external routes
- [x] Modified OSPF interface costs
- [x] Configured reference bandwidth on all routers
- [x] Verified OSPF neighbor adjacencies
- [x] Verified routing table entries
- [x] Tested LAN connectivity
- [x] Saved router configurations

**Lab Status: Successfully Completed**
