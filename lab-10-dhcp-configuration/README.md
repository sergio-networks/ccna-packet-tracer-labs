# Lab 10 — DHCP Configuration & Relay

## Objective
Configure and verify DHCP services using a Cisco router and a dedicated DHCP server. Implement DHCP relay to allow clients on one subnet to receive IPv4 configuration from a server on another subnet.

**CCNA Reference:** Wendell Odom's CCNA 200-301 Official Cert Guide, Volume 1 — Chapter 19: IP Addressing on Hosts

## Network Topology

### Devices Used
- 1 Cisco 2911 Router (R1)
- 2 Cisco 2960 Switches (S1 and S2)
- 2 PCs (PC1 and PC2)
- 1 Server-PT (DHCP-SERVER)

### Connections
- R1 G0/0 → S1 G0/1
- R1 G0/1 → S2 G0/1
- PC1 Fa0 → S1 Fa0/1
- PC2 Fa0 → S1 Fa0/2
- DHCP-SERVER Fa0 → S2 Fa0/1

## IP Addressing Table

| Device | Interface | IPv4 Address | Default Gateway |
|---|---|---|---|
| R1 | G0/0 | 192.168.50.1/24 | N/A |
| R1 | G0/1 | 192.168.60.1/24 | N/A |
| DHCP-SERVER | Fa0 | 192.168.60.10/24 | 192.168.60.1 |
| PC1 | Fa0 | 192.168.50.21/24 | 192.168.50.1 |
| PC2 | Fa0 | 192.168.50.22/24 | 192.168.50.1 |

## Part 1 — Router-Based DHCP

Configured R1 as a DHCP server for the 192.168.50.0/24 network.

### DHCP Configuration
```cisco
ip dhcp excluded-address 192.168.50.1 192.168.50.20

ip dhcp pool LAN50
 network 192.168.50.0 255.255.255.0
 default-router 192.168.50.1
 dns-server 8.8.8.8
```

### Verification
```cisco
show ip dhcp binding
show ip dhcp pool
```

**Result:** PC1 and PC2 successfully obtained DHCP leases and communicated across the local network.

## Part 2 — DHCP Relay

Added a dedicated DHCP server on the 192.168.60.0/24 network.

### DHCP Server Configuration
- Static IP: 192.168.60.10
- Subnet Mask: 255.255.255.0
- Default Gateway: 192.168.60.1
- DNS Server: 8.8.8.8

### DHCP Pool Configuration
- Pool Name: LAN50
- Client Network: 192.168.50.0/24
- Starting IP: 192.168.50.21
- Default Gateway: 192.168.50.1
- DNS Server: 8.8.8.8
- Maximum Users: 50

### Remove the Original Router DHCP Pool
```cisco
no ip dhcp pool LAN50
```

### Configure DHCP Relay on R1
```cisco
interface GigabitEthernet0/0
 ip helper-address 192.168.60.10
```

The helper address was configured on the client-facing interface because DHCP Discover messages originate from the client network.

## Verification and Testing

| Test | Result | Status |
|---|---|---|
| R1 G0/0 | 192.168.50.1 — up/up | PASS |
| R1 G0/1 | 192.168.60.1 — up/up | PASS |
| DHCP Server → R1 | 4/4 successful pings | PASS |
| PC1 DHCP Lease | 192.168.50.21 | PASS |
| PC2 DHCP Lease | 192.168.50.22 | PASS |
| PC1 → PC2 | 4/4 successful pings | PASS |
| PC2 → DHCP Server | 4/4 successful pings | PASS |

## Troubleshooting and Lessons Learned

- Corrected a Cisco IOS command syntax error while configuring an interface.
- Verified both router interfaces were operational using `show ip interface brief`.
- Learned the difference between `show ip dhcp pool` and `show ip dhcp binding`.
- Removed the original DHCP pool to prevent competing DHCP services.
- Configured DHCP relay on the interface facing the clients.
- Confirmed DHCP address assignment and inter-subnet connectivity.

## Key Cisco IOS Commands
```cisco
show ip interface brief
show ip dhcp binding
show ip dhcp pool
show running-config
ip dhcp excluded-address
ip dhcp pool LAN50
no ip dhcp pool LAN50
ip helper-address 192.168.60.10
copy running-config startup-config
```

## CCNA Concepts Reinforced

- DHCP Discover, Offer, Request, Acknowledge (DORA)
- DHCP server and client operation
- DHCP relay agents
- IPv4 addressing and subnet masks
- Default gateway configuration
- DHCP lease verification
- Layer 3 routing between subnets
- Network connectivity troubleshooting
