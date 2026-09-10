# Lab 03 – Static Routing

## Objective

Build and troubleshoot a multi-router IPv4 network in Cisco Packet Tracer using static routing.

The goal of this lab was to expand the previous single-router topology by adding a second router and a third LAN. Static routes were then configured so devices on separate remote networks could communicate successfully.

This lab focused on:

- Configuring router interfaces and IPv4 addresses
- Creating a `/30` transit network between two routers
- Understanding directly connected vs. remote networks
- Configuring next-hop static routes
- Reading and interpreting the IPv4 routing table
- Troubleshooting `Destination host unreachable` messages
- Verifying local, router-to-router, and end-to-end connectivity
- Understanding why successful communication requires a valid route in both directions
- Saving router configurations to NVRAM

---

## Topology

This lab expands the previous topology by introducing a second router, R2, and a new LAN for PC5.

R1 acts as the router for the existing `192.168.1.0/24` and `192.168.2.0/24` LANs. R2 acts as the router for the new `192.168.3.0/24` LAN.

R1 and R2 are connected directly using the `10.0.0.0/30` transit network.

```text
192.168.1.0/24
PC1 ─┐
PC2 ─┼── S1 ── R1 ───── 10.0.0.0/30 ───── R2 ── S3 ── PC5
PC3 ─┘          │       .1             .2          192.168.3.0/24
                │
                S2
                │
               PC4
          192.168.2.0/24
```

### Network Roles

- **S1** provides Layer 2 connectivity for PC1, PC2, and PC3.
- **S2** provides Layer 2 connectivity for PC4.
- **S3** provides Layer 2 connectivity for PC5.
- **R1** routes traffic for the `192.168.1.0/24` and `192.168.2.0/24` LANs.
- **R2** routes traffic for the `192.168.3.0/24` LAN.
- **R1 and R2** communicate over the `10.0.0.0/30` transit network.
- Static routes allow each router to learn about networks that are not directly connected.

---

## IP Addressing

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|---|
| PC1 | Fa0 | 192.168.1.10 | 255.255.255.0 | 192.168.1.1 |
| PC2 | Fa0 | 192.168.1.20 | 255.255.255.0 | 192.168.1.1 |
| PC3 | Fa0 | 192.168.1.30 | 255.255.255.0 | 192.168.1.1 |
| R1 | G0/0 | 192.168.1.1 | 255.255.255.0 | N/A |
| R1 | G0/1 | 192.168.2.1 | 255.255.255.0 | N/A |
| PC4 | Fa0 | 192.168.2.10 | 255.255.255.0 | 192.168.2.1 |
| R1 | G0/2 | 10.0.0.1 | 255.255.255.252 | N/A |
| R2 | G0/0 | 10.0.0.2 | 255.255.255.252 | N/A |
| R2 | G0/1 | 192.168.3.1 | 255.255.255.0 | N/A |
| PC5 | Fa0 | 192.168.3.10 | 255.255.255.0 | 192.168.3.1 |

### Addressing Design

Three `/24` networks are used for the LANs:

- `192.168.1.0/24` – PC1, PC2, and PC3 LAN
- `192.168.2.0/24` – PC4 LAN
- `192.168.3.0/24` – PC5 LAN

Each PC uses the router interface on its local network as its default gateway.

For example, PC5 is configured as:

- IP address: `192.168.3.10`
- Subnet mask: `255.255.255.0`
- Default gateway: `192.168.3.1`

The default gateway is R2's G0/1 interface because it provides PC5 with a path to networks outside its local LAN.

---

## R1–R2 Transit Network

R1 and R2 use a separate point-to-point transit network:

```text
10.0.0.0/30
```

Router addressing:

```text
R1 G0/2 = 10.0.0.1/30
R2 G0/0 = 10.0.0.2/30
```

A `/30` subnet uses the mask:

```text
255.255.255.252
```

A `/30` provides two usable host addresses, which works well for this lab because only the two router interfaces need IP addresses.

The transit network also gives each router a reachable **next-hop IP address** for static routing.

---

## Router Interface Configuration

### R1 Transit Interface

```text
enable
configure terminal
interface g0/2
ip address 10.0.0.1 255.255.255.252
no shutdown
```

### R2 Configuration

```text
enable
configure terminal
hostname R2

interface g0/0
ip address 10.0.0.2 255.255.255.252
no shutdown

interface g0/1
ip address 192.168.3.1 255.255.255.0
no shutdown
```

### Why `no shutdown` Was Required

Router Ethernet interfaces are commonly administratively disabled until they are manually enabled.

The command:

```text
no shutdown
```

was used to bring the router interfaces into an operational state.

Once R2 G0/1 was enabled, the connection between R2 and S3 changed from down to up.

---

## PC5 Configuration

PC5 was configured with:

```text
IP Address:      192.168.3.10
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.3.1
```

The default gateway is R2's G0/1 interface because R2 is the router connected to PC5's local network.

A host sends traffic to its default gateway when the destination is outside its own local subnet.

---

## Initial Connectivity Testing

Testing was performed from the closest device outward instead of immediately testing the entire network.

### Test 1 – PC5 to Its Default Gateway

From PC5:

```text
ping 192.168.3.1
```

Result:

```text
4/4 replies
```

This confirmed that PC5 could communicate successfully with R2 on the local LAN.

### Test 2 – R2 to R1

From R2:

```text
ping 10.0.0.1
```

The first test returned:

```text
4/5 replies
```

A second test returned:

```text
5/5 replies
```

The first packet can fail while ARP resolves the MAC address required for local Ethernet delivery.

This confirmed that the R1-to-R2 transit network was working correctly.

---

## Routing Table Verification

The Cisco routing table can be viewed with:

```text
show ip route
```

From configuration mode, the same command can be run using:

```text
do show ip route
```

### R2 Before Static Routes

R2 initially knew only its directly connected networks:

```text
C 10.0.0.0/30
C 192.168.3.0/24
```

R2 also had local `/32` routes for its own interface addresses:

```text
L 10.0.0.2/32
L 192.168.3.1/32
```

### Routing Table Codes

- `C` = Connected
- `L` = Local
- `S` = Static

A router automatically learns directly connected networks when the interface is configured and operational.

Remote networks require another source of routing information, such as static routes or a dynamic routing protocol.

---

## Static Route Syntax

The basic Cisco IOS static route command is:

```text
ip route [destination network] [subnet mask] [next-hop IP]
```

Example:

```text
ip route 192.168.3.0 255.255.255.0 10.0.0.2
```

This tells the router:

> To reach the `192.168.3.0/24` network, send the packet to the next-hop router at `10.0.0.2`.

---

## Static Route on R1

R1 already knew these networks because they were directly connected:

```text
192.168.1.0/24
192.168.2.0/24
10.0.0.0/30
```

However, R1 did not originally know how to reach:

```text
192.168.3.0/24
```

The following route was configured:

```text
ip route 192.168.3.0 255.255.255.0 10.0.0.2
```

After configuration, R1's routing table showed:

```text
S 192.168.3.0/24 [1/0] via 10.0.0.2
```

The `S` indicates that the route was manually configured as a static route.

---

## Understanding the Return Path

Before the static route was added to R1, PC5 could not successfully ping R1's `10.0.0.1` interface.

PC5 could send traffic toward R1 through R2, but R1 did not yet know how to return traffic to the `192.168.3.0/24` network.

This demonstrated an important routing concept:

**Successful communication requires a valid path to the destination and a valid return path back to the source.**

After the R1 static route was configured, PC5 successfully pinged:

```text
10.0.0.1
```

with:

```text
4/4 replies
```

---

## Troubleshooting PC5 to PC1

PC5 then attempted to reach PC1:

```text
ping 192.168.1.10
```

The ping failed with:

```text
Reply from 192.168.3.1: Destination host unreachable.
```

The address `192.168.3.1` belongs to R2.

This error was useful because it showed that:

1. PC5 successfully reached its default gateway.
2. R2 received the packet.
3. R2 did not have a route to the `192.168.1.0/24` network.

R2 was then given the following static route:

```text
ip route 192.168.1.0 255.255.255.0 10.0.0.1
```

This told R2 to send traffic for the `192.168.1.0/24` network to R1.

After the route was added, PC5 successfully pinged PC1.

The first test returned:

```text
4/5 replies
```

A second test returned:

```text
5/5 replies
```

The initial missed packet was consistent with ARP resolution occurring before forwarding could complete.

---

## Static Route to PC4's Network

PC5 then attempted to reach PC4:

```text
ping 192.168.2.10
```

This initially failed because R2 did not have a route for:

```text
192.168.2.0/24
```

The following route was added to R2:

```text
ip route 192.168.2.0 255.255.255.0 10.0.0.1
```

R2 now knew that both LANs behind R1 could be reached through the next-hop IP address `10.0.0.1`.

After the route was added, PC5 successfully reached PC4.

---

## Final Static Routes

### R1

```text
ip route 192.168.3.0 255.255.255.0 10.0.0.2
```

### R2

```text
ip route 192.168.1.0 255.255.255.0 10.0.0.1
ip route 192.168.2.0 255.255.255.0 10.0.0.1
```

These routes provide connectivity between all three LANs.

---

## Packet Path Example

When PC5 sends traffic to PC1:

```text
PC5
192.168.3.10
   |
   v
S3
   |
   v
R2
192.168.3.1
   |
   | Static route:
   | 192.168.1.0/24 → 10.0.0.1
   v
R1
10.0.0.1
   |
   v
S1
   |
   v
PC1
192.168.1.10
```

The return traffic follows the opposite direction.

R1 uses its static route for `192.168.3.0/24` to send the reply back toward R2.

---

## Troubleshooting Method

A major focus of this lab was troubleshooting connectivity progressively.

Instead of immediately testing the farthest destination, connectivity was verified in stages:

```text
1. PC5 → R2 default gateway
2. R2 → R1 transit interface
3. PC5 → R1 transit interface
4. PC5 → PC1
5. PC5 → PC4
```

This approach helps identify exactly where connectivity stops.

A useful troubleshooting rule from this lab is:

**Start with the closest known device and work outward one hop at a time.**

---

## Commands Practiced

```text
enable
configure terminal
hostname
interface g0/0
interface g0/1
interface g0/2
ip address
no shutdown
exit
end
ip route
show ip route
do show ip route
ping
copy running-config startup-config
```

---

## Saving the Configuration

The router configurations were saved using:

```text
copy running-config startup-config
```

The command was run on both R1 and R2.

Both routers returned:

```text
[OK]
```

This copied the active running configuration from RAM into startup configuration in NVRAM so the configuration will survive a reboot.

---

## Key Takeaways

- Routers automatically know their directly connected networks.
- Routers need routing information for remote networks.
- Static routes are manually configured by an administrator.
- A static route identifies a destination network and a next-hop router.
- The next-hop address is usually an interface on a neighboring router.
- Routing must work in both directions for successful communication.
- A host uses its default gateway to reach destinations outside its local subnet.
- Switches forward Ethernet frames at Layer 2 but do not perform the routing function in this topology.
- `show ip route` displays the IPv4 routing table.
- `C` represents a connected route.
- `L` represents a local router-interface route.
- `S` represents a static route.
- `do` can be used to run certain EXEC commands while remaining in configuration mode.
- `/30` networks are useful for point-to-point router transit links.
- `Destination host unreachable` can help identify the device that cannot forward traffic toward the destination.
- Troubleshooting should move from the local network outward one hop at a time.
- ARP resolution can cause an initial ping packet to fail while MAC address information is learned.
- Router configurations should be saved with `copy running-config startup-config`.

---

## Result

Successfully expanded an existing network into a multi-router topology, configured a `/30` router transit link, created static routes for multiple remote LANs, diagnosed missing-route failures, verified return-path routing, and confirmed end-to-end IPv4 connectivity across the network.
