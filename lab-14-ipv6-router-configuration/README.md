# Lab 14 — IPv6 Router Configuration

## Overview

In this lab, I configured IPv6 addressing on two Cisco 2911 routers using Cisco Packet Tracer.

The purpose of this lab was to practice IPv6 global unicast addressing, link-local addressing, IPv6 routing configuration, Neighbor Discovery Protocol (NDP), and modified EUI-64 addressing.

This lab reinforces concepts covered in Chapter 27 of the CCNA 200-301 Official Cert Guide Volume 1.

## Objectives

- Enable IPv6 routing on Cisco routers
- Configure IPv6 global unicast addresses
- Configure manual IPv6 link-local addresses
- Verify IPv6 interface status
- Test IPv6 connectivity between routers
- Understand IPv6 Neighbor Discovery Protocol (NDP)
- Test connectivity using link-local addresses
- Configure an IPv6 address using EUI-64
- Examine the relationship between MAC addresses and IPv6 interface identifiers
- Troubleshoot IPv6 configuration and connectivity

## Network Topology

```text
                IPv6 Transit Network
                 2001:DB8:12::/64

          R1                         R2
      Cisco 2911                 Cisco 2911
          |                          |
        G0/0 ---------------------- G0/0
          |                          |
   2001:DB8:12::1              2001:DB8:12::2
      FE80::1                     FE80::2
```

Two Cisco 2911 routers were connected directly through their GigabitEthernet0/0 interfaces.

Both routers were configured with IPv6 global unicast and link-local addresses.

## IPv6 Addressing Table

| Device | Interface | Global Unicast Address | Link-Local Address |
|---|---|---|---|
| R1 | G0/0 | 2001:DB8:12::1/64 | FE80::1 |
| R2 | G0/0 | 2001:DB8:12::2/64 | FE80::2 |

Additional EUI-64 configuration on R1:

| Device | Interface | Configured IPv6 Prefix | Method |
|---|---|---|---|
| R1 | G0/0 | 2001:DB8:100:1::/64 | EUI-64 |

## Router Configuration

### R1 — IPv6 Configuration

```cisco
enable
configure terminal

ipv6 unicast-routing

interface gigabitEthernet0/0
 ipv6 address 2001:DB8:12::1/64
 ipv6 address FE80::1 link-local
 no shutdown

end
```

The `ipv6 unicast-routing` command enables IPv6 packet forwarding on the router.

The global unicast address provides IPv6 addressing within the configured network.

The link-local address is used for communication with other IPv6 devices on the same local link.

### R2 — IPv6 Configuration

```cisco
enable
configure terminal

ipv6 unicast-routing

interface gigabitEthernet0/0
 ipv6 address 2001:DB8:12::2/64
 ipv6 address FE80::2 link-local
 no shutdown

end
```

R2 was configured with an IPv6 address belonging to the same `/64` network as R1.

Both interfaces were brought online using `no shutdown`.

## IPv6 Interface Verification

The following command was used to verify the IPv6 configuration:

```cisco
show ipv6 interface gigabitEthernet0/0
```

R1 displayed:

```text
GigabitEthernet0/0 is up, line protocol is up

IPv6 is enabled, link-local address is FE80::1

Global unicast address(es):
  2001:DB8:12::1
```

The output confirmed that IPv6 was enabled and that the interface was operational.

Both routers eventually reached an `up/up` interface state.

## IPv6 Connectivity Testing

### Test 1 — Global Unicast Connectivity

From R1:

```cisco
ping 2001:DB8:12::2
```

Result:

```text
Success rate is 100 percent (5/5)
```

This confirmed successful IPv6 connectivity between R1 and R2.

### Troubleshooting Observation

An initial ping failed because an incorrect IPv6 address was entered:

```cisco
ping 2001:DB8::12:2
```

The correct destination was:

```cisco
ping 2001:DB8:12::2
```

IPv6 zero compression must preserve the correct placement of address hextets.

After correcting the address, the ping succeeded.

## IPv6 Neighbor Discovery Protocol

IPv6 uses Neighbor Discovery Protocol (NDP) instead of ARP to discover neighboring devices and resolve IPv6 addresses to Layer 2 addresses.

The following command was used on R1:

```cisco
show ipv6 neighbors
```

The router displayed an entry for R2:

```text
IPv6 Address: 2001:DB8:12::2
Link-Layer Address: 0090.211E.C901
State: REACH
Interface: GigabitEthernet0/0
```

The `REACH` state indicated that R2 was considered reachable.

This demonstrated successful IPv6 neighbor discovery on the directly connected network.

## Link-Local Connectivity Testing

Link-local IPv6 addresses use the `FE80::/10` range and are valid only on the local link.

From R2, the following command was used:

```cisco
ping FE80::1
```

The router prompted for an outgoing interface because link-local addresses are scoped to a specific link.

The full interface name was entered:

```text
gigabitethernet0/0
```

Result:

```text
Success rate is 100 percent (5/5)
```

This verified successful communication using IPv6 link-local addresses.

## EUI-64 Configuration

Modified EUI-64 allows an IPv6 interface identifier to be derived from a 48-bit MAC address.

The following configuration was applied to R1's GigabitEthernet0/0 interface:

```cisco
configure terminal

interface gigabitEthernet0/0
 ipv6 address 2001:DB8:100:1::/64 eui-64

end
```

The running configuration confirmed:

```cisco
interface GigabitEthernet0/0
 no ip address
 duplex auto
 speed auto
 ipv6 address FE80::1 link-local
 ipv6 address 2001:DB8:12::1/64
 ipv6 address 2001:DB8:100:1::/64 eui-64
```

This demonstrated that multiple IPv6 global unicast addresses can be configured on a single router interface.

### EUI-64 Verification

The following command was used:

```cisco
show ipv6 interface gigabitEthernet0/0
```

Packet Tracer displayed:

```text
Global unicast address(es):

  2001:DB8:12::1
  2001:DB8:100:1::1 [EUI]
```

The `[EUI]` marker identified the address as EUI-64 configured.

However, the displayed address did not match the interface identifier expected from the router's MAC address.

This discrepancy was documented rather than treated as a verified MAC-derived address.

## Modified EUI-64 Calculation

The following command was used to identify R1's interface MAC address:

```cisco
show interfaces gigabitEthernet0/0
```

The router reported:

```text
Hardware is CN Gigabit Ethernet
address is 0000.0c0b.7201
```

### Step 1 — Identify the MAC Address

```text
00:00:0C:0B:72:01
```

### Step 2 — Insert FF:FE

```text
00:00:0C:FF:FE:0B:72:01
```

### Step 3 — Flip the Universal/Local Bit

The seventh bit of the first octet is inverted.

```text
00 -> 02
```

Modified interface identifier:

```text
02:00:0C:FF:FE:0B:72:01
```

### Step 4 — Convert to IPv6 Hextets

```text
0200:0CFF:FE0B:7201
```

### Expected EUI-64 IPv6 Address

Using the configured prefix:

```text
2001:DB8:100:1::/64
```

The expected MAC-derived IPv6 address would be:

```text
2001:DB8:100:1:200:CFF:FE0B:7201
```

Packet Tracer instead displayed:

```text
2001:DB8:100:1::1 [EUI]
```

The difference may reflect a Packet Tracer simulation anomaly or another implementation behavior that was not resolved during this lab.

The EUI-64 calculation was verified mathematically using the reported MAC address.

## Verification Commands

The following commands were practiced throughout the lab:

```cisco
show ipv6 interface brief
show ipv6 interface gigabitEthernet0/0
show ipv6 neighbors
show ipv6 route
show interfaces gigabitEthernet0/0
show running-config
ping 2001:DB8:12::2
ping FE80::1
```

These commands are useful for checking IPv6 addressing, interface status, routing information, neighbor discovery, and connectivity.

## Troubleshooting Performed

### Issue 1 — Interface Not Fully Operational

R1 initially displayed an `up/down` interface state.

R2's connected interface had not yet been enabled.

After configuring R2 and entering `no shutdown`, the link became operational.

### Issue 2 — Incorrect IPv6 Destination Address

An IPv6 ping initially failed because the destination address was entered incorrectly.

Correcting the IPv6 address resulted in successful connectivity.

### Issue 3 — Link-Local Ping Interface Selection

A link-local ping required the outgoing interface to be specified.

The abbreviated interface name was not accepted at the prompt.

Entering `gigabitethernet0/0` allowed the ping to complete successfully.

### Issue 4 — EUI-64 Address Discrepancy

The router accepted the EUI-64 configuration command and displayed an `[EUI]` address.

However, the displayed interface identifier did not match the value calculated from the physical MAC address.

The discrepancy was recorded for future troubleshooting.

## Lab Results

| Test | Result |
|---|---|
| IPv6 forwarding enabled | Successful |
| R1 global unicast configuration | Successful |
| R2 global unicast configuration | Successful |
| Manual link-local addressing | Successful |
| Router interfaces operational | Successful |
| R1-to-R2 global unicast ping | 100% (5/5) |
| IPv6 neighbor discovery | Successful |
| R2-to-R1 link-local ping | 100% (5/5) |
| EUI-64 command configuration | Successful |
| EUI-64 MAC-derived address verification | Discrepancy observed |

## Key Takeaways

- IPv6 forwarding on Cisco routers is enabled using `ipv6 unicast-routing`.
- IPv6 global unicast addresses can be configured manually.
- Link-local addresses support communication between devices on the same local link.
- IPv6 Neighbor Discovery replaces ARP functionality for IPv6 networks.
- Link-local pings may require the outgoing interface to be specified.
- Multiple IPv6 addresses can exist on the same router interface.
- Modified EUI-64 uses a MAC-derived interface identifier.
- IPv6 zero compression must be interpreted carefully when troubleshooting addresses.
- Cisco IOS verification commands are essential for confirming configurations and diagnosing problems.
- Simulator output should be compared against expected protocol behavior when unexpected results occur.

## Lab Status

**Completed**

Successfully configured IPv6 addressing on two Cisco routers, verified global unicast and link-local connectivity, examined IPv6 Neighbor Discovery, and practiced EUI-64 configuration and calculation.

**CCNA 200-301 — Chapter 27: Implementing IPv6 Addressing on Routers**
