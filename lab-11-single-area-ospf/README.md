
# Lab 11 - Single-Area OSPF Configuration and Troubleshooting

## Goal
Configure single-area OSPFv2 across three Cisco routers, verify neighbor adjacencies and dynamically learned routes, and troubleshoot an OSPF area mismatch.

## Topology
- R1 connected to R2
- R2 connected to R3
- All routers configured for OSPF Area 0
- OSPF process ID: 1

## Addressing Plan

| Device | Interface | IP Address | Subnet Mask |
|--------|-----------|------------|-------------|
| R1 | G0/0 | 10.0.12.1 | 255.255.255.252 |
| R2 | G0/0 | 10.0.12.2 | 255.255.255.252 |
| R2 | G0/1 | 10.0.23.1 | 255.255.255.252 |
| R3 | G0/0 | 10.0.23.2 | 255.255.255.252 |

## OSPF Configuration

### R1
```cisco
router ospf 1
 router-id 1.1.1.1
 network 10.0.12.0 0.0.0.3 area 0
```

### R2
```cisco
router ospf 1
 router-id 2.2.2.2
 network 10.0.12.0 0.0.0.3 area 0
 network 10.0.23.0 0.0.0.3 area 0
```

### R3
```cisco
router ospf 1
 router-id 3.3.3.3
 network 10.0.23.0 0.0.0.3 area 0
```

## Verification Commands

```cisco
show ip ospf neighbor
show ip route
show ip route ospf
show ip ospf interface brief
ping 10.0.23.2
```

### Verification Results
- OSPF neighbor adjacencies reached FULL state.
- DR/BDR roles were observed.
- R1 successfully learned the remote 10.0.23.0/30 network.
- OSPF route displayed administrative distance 110 and cost 2.
- R1 successfully pinged R3 with 5/5 packets received.

### OSPF Route Observed

```text
O 10.0.23.0 [110/2] via 10.0.12.2
```

## Troubleshooting

### Issue
Intentionally configured R3's G0/0 interface in OSPF Area 1 instead of Area 0.

```cisco
interface gigabitEthernet 0/0
 ip ospf 1 area 1
```

### Error Observed

```text
%OSPF-4-ERRRCV: Received invalid packet: mismatch area ID
```

### Resolution
Corrected R3's interface to OSPF Area 0.

```cisco
interface gigabitEthernet 0/0
 ip ospf 1 area 0
```

### Result
- OSPF adjacency successfully recovered.
- Dynamic routing was restored.
- End-to-end connectivity was verified.

## Key Commands

| Command | Purpose |
|---------|---------|
| `router ospf 1` | Start OSPF process |
| `router-id` | Assign OSPF router ID |
| `network ... area 0` | Enable OSPF on matching interfaces |
| `ip ospf 1 area 0` | Configure OSPF directly on an interface |
| `show ip ospf neighbor` | Verify neighbor adjacencies |
| `show ip route ospf` | Display OSPF-learned routes |
| `show ip ospf interface brief` | Verify OSPF interfaces |
| `ping` | Test network connectivity |

## Key Takeaways
- OSPF dynamically learns routes between routers.
- OSPF process IDs are locally significant.
- OSPF router IDs uniquely identify routers.
- OSPF neighbors must have matching area IDs.
- OSPF uses cost as its routing metric.
- OSPF-learned routes appear with the `O` route code.
- OSPF neighbor and routing verification are essential for troubleshooting.

## Skills Practiced
- Single-area OSPFv2 configuration
- Router ID configuration
- OSPF neighbor verification
- Dynamic routing verification
- Routing table interpretation
- Network connectivity testing
- OSPF area mismatch troubleshooting

## Lab Result
Successfully configured and verified single-area OSPF across three routers. Established neighbor adjacencies, confirmed dynamic route learning, and resolved an intentional OSPF area mismatch.


