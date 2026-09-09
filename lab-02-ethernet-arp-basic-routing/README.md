# Lab 02 - Ethernet Switching, ARP & Basic Routing

## Objective

Build a small Ethernet LAN, observe how switches learn MAC addresses, examine ARP behavior, configure a Cisco router, and successfully route traffic between two IPv4 subnets.

## Final Topology

```text
PC1 ─┐
PC2 ─┼── S1 ─── R1 ─── S2 ─── PC4
PC3 ─┘
```

## Devices Used

- Cisco 2960 Switch - S1
- Cisco 2960 Switch - S2
- Cisco 2911 Router - R1
- PC1
- PC2
- PC3
- PC4

## IPv4 Addressing

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|---|
| PC1 | Fa0 | 192.168.1.10 | 255.255.255.0 | 192.168.1.1 |
| PC2 | Fa0 | 192.168.1.20 | 255.255.255.0 | 192.168.1.1 |
| PC3 | Fa0 | 192.168.1.30 | 255.255.255.0 | 192.168.1.1 |
| R1 | G0/0 | 192.168.1.1 | 255.255.255.0 | N/A |
| R1 | G0/1 | 192.168.2.1 | 255.255.255.0 | N/A |
| PC4 | Fa0 | 192.168.2.10 | 255.255.255.0 | 192.168.2.1 |

## Networks

```text
192.168.1.0/24 → R1 G0/0
192.168.2.0/24 → R1 G0/1
```

## Cabling

Copper straight-through cables were used between unlike Ethernet devices.

```text
PC ↔ Switch
Switch ↔ Router
Router ↔ Switch
Switch ↔ PC
```

## Part 1 - Ethernet Switching

PC1, PC2, and PC3 were connected to S1.

```text
PC1 Fa0 → S1 Fa0/1
PC2 Fa0 → S1 Fa0/2
PC3 Fa0 → S1 Fa0/3
```

I used the following switch command to examine learned MAC addresses:

```text
show mac address-table
```

The switch dynamically learned the source MAC address of each device and associated it with the port where the frame entered.

Example:

```text
PC1 MAC → Fa0/1
PC2 MAC → Fa0/2
PC3 MAC → Fa0/3
```

### MAC Address Table Concepts

A switch:

```text
Learns using:   SOURCE MAC
Forwards using: DESTINATION MAC
```

If the destination MAC is known, the switch forwards the frame only through the correct port.

If the destination MAC is unknown, the switch floods the frame out all other ports in the same VLAN except the incoming port.

Dynamic MAC address entries can age out if the switch does not receive traffic from the device for a period of time.

## Part 2 - ARP

I used:

```text
arp -a
```

on the PCs to view their ARP tables.

An ARP table stores:

```text
IP Address → MAC Address
```

A switch MAC address table stores:

```text
MAC Address → Switch Port
```

If a device knows an IPv4 address but does not know the corresponding MAC address, it sends an ARP Request.

The ARP Request uses the Ethernet broadcast destination:

```text
FFFF.FFFF.FFFF
```

The device owning the requested IPv4 address normally responds with a unicast ARP Reply.

## Same Subnet vs Different Subnet

One of the major concepts practiced in this lab was deciding whether traffic is local or remote.

### Same Subnet

If the destination is on the same subnet:

```text
ARP for the destination device
Destination MAC = destination host's MAC
```

Example:

```text
PC1:        192.168.1.10/24
Destination: 192.168.1.20/24
```

PC1 communicates directly with PC2.

### Different Subnet

If the destination is on another subnet:

```text
ARP for the default gateway
Destination MAC = router's MAC
Destination IP  = final remote host's IP
```

Example:

```text
PC1:        192.168.1.10/24
Destination: 192.168.2.10/24
Gateway:     192.168.1.1
```

PC1 sends the Ethernet frame to R1 while keeping PC4's IP address as the destination IP.

A useful rule:

```text
IP address  = final destination
MAC address = next hop on the local link
```

## Part 3 - Router Configuration

R1 was configured with one interface in each subnet.

### G0/0

```text
enable
configure terminal
hostname R1
interface gigabitethernet0/0
ip address 192.168.1.1 255.255.255.0
no shutdown
```

### G0/1

```text
interface gigabitethernet0/1
ip address 192.168.2.1 255.255.255.0
no shutdown
```

The `no shutdown` command enables the router interface.

```text
shutdown     → administratively disables interface
no shutdown  → enables interface
```

## Part 4 - Routing Between Subnets

PC1 successfully pinged PC4 across R1:

```text
PC1 192.168.1.10
        ↓
       S1
        ↓
       R1
        ↓
       S2
        ↓
PC4 192.168.2.10
```

The ping completed successfully with:

```text
4 packets sent
4 packets received
```

No static routes were required because both networks were directly connected to R1.

## Router Verification Commands

### Routing Table

```text
show ip route
```

The router showed connected and local routes.

```text
C = Connected
L = Local
S = Static
```

Example:

```text
C 192.168.1.0/24 → GigabitEthernet0/0
L 192.168.1.1/32 → R1's exact G0/0 address

C 192.168.2.0/24 → GigabitEthernet0/1
L 192.168.2.1/32 → R1's exact G0/1 address
```

A `/32` local route represents one exact IPv4 address belonging to the router.

### ARP Table

```text
show ip arp
```

This displayed IPv4-to-MAC mappings learned by R1.

Example:

```text
192.168.1.10 → PC1 MAC → G0/0
192.168.2.10 → PC4 MAC → G0/1
```

If the router does not know the MAC address of a local destination, it sends an ARP Request out the appropriate interface.

### Interface Summary

```text
show ip interface brief
```

The active interfaces showed:

```text
GigabitEthernet0/0   192.168.1.1   up   up
GigabitEthernet0/1   192.168.2.1   up   up
```

`up/up` indicates that both the physical interface and line protocol are operational.

## Routing Table Logic

A router examines the destination IP address and searches its routing table for the best matching route.

```text
Destination IP
      ↓
Routing Table
      ↓
Best Matching Route
      ↓
Outgoing Interface / Next Hop
```

If there is no matching route and no default route, the router drops the packet.

## Longest Prefix Match

I also practiced basic longest prefix match.

If multiple routes match a destination, the router chooses the most specific matching route.

Example:

```text
10.0.0.0/8
10.1.0.0/16
10.1.5.0/24

Destination: 10.1.5.200
```

All three routes match, but `/24` is the longest and most specific prefix, so the router selects it.

The rule is:

```text
1. Find every route that matches.
2. Choose the matching route with the longest prefix.
```

## Saving the Router Configuration

The configuration was saved using:

```text
copy running-config startup-config
```

The saved configuration was verified with:

```text
show startup-config
```

Important distinction:

```text
running-config → current configuration stored in RAM
startup-config → saved configuration stored in NVRAM
```

## Commands Practiced

```text
ping
arp -a
show mac address-table
enable
configure terminal
hostname R1
interface gigabitethernet0/0
interface gigabitethernet0/1
ip address
no shutdown
show ip route
show ip arp
show ip interface brief
show running-config
show startup-config
copy running-config startup-config
```

## Key Takeaways

This lab helped connect several important networking concepts together:

- Switches learn from source MAC addresses.
- Switches forward based on destination MAC addresses.
- ARP resolves IPv4 addresses to MAC addresses.
- Same-subnet traffic is sent directly to the destination host.
- Different-subnet traffic is sent to the default gateway.
- IP addresses identify the final destination.
- MAC addresses identify the next local hop.
- Routers make forwarding decisions using destination IP addresses and their routing tables.
- MAC addresses change as traffic crosses routers, while the original source and destination IP addresses normally remain the same.
- Connected networks automatically appear in a router's routing table.
- `show` commands are essential for verifying and troubleshooting a network.

## Lab Result

✅ Successfully built two IPv4 networks and routed traffic between them using a Cisco router.

## Next Lab

**Lab 03 - Static Routing**
