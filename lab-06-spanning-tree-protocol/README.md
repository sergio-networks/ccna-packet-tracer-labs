# Lab 06 — Spanning Tree Protocol

## Objective

The purpose of this lab was to observe how Spanning Tree Protocol (STP) prevents Layer 2 switching loops while maintaining redundant network paths.

This lab focused on root bridge election, STP port roles and states, path cost, redundant links, and STP reconvergence after a link failure.

---

## Topology

```text
                  S1
             Root Bridge
               /      \
              /        \
             S2 ------- S3
```

Three Cisco 2960 switches were connected in a triangle, creating redundant Layer 2 paths.

Without STP, this topology could create a switching loop.

---

## Initial STP Topology

STP automatically elected **S1 as the Root Bridge**.

```text
                    S1
                 ROOT BRIDGE
                 /         \
                /           \
          Desg/FWD         Desg/FWD
              /               \
             /                 \
        Root/FWD             Root/FWD
           S2                   S3
            \                   /
             \                 /
          Desg/FWD -------- Altn/BLK
```

### S1 — Root Bridge

```text
Gi0/1   Designated   Forwarding
Gi0/2   Designated   Forwarding
```

Because S1 is the Root Bridge, it does not have a Root Port.

---

## Root Bridge Election

All switches initially used the default STP priority.

S1 and S2 both displayed:

```text
Priority: 32769
```

This represents the default bridge priority of 32768 plus the VLAN 1 extended system ID.

Because the priorities were equal, STP used the MAC address as the tiebreaker.

```text
S1 MAC: 0001.4334.5821
S2 MAC: 00E0.B0DC.3C8A
```

S1 had the lower Bridge ID and was therefore elected the Root Bridge.

---

## S2 Port Roles

The STP output on S2 showed:

```text
Gi0/1   Root   FWD
Gi0/2   Desg   FWD
```

Gi0/1 became the **Root Port** because it provided S2's best path toward the Root Bridge.

Gi0/2 remained a **Designated Port** and continued forwarding.

---

## S3 Port Roles

The initial STP output on S3 showed:

```text
Gi0/1   Root   FWD
Gi0/2   Altn   BLK
```

Gi0/1 provided S3 with the lowest-cost path directly to the Root Bridge.

Gi0/2 provided a redundant path through S2, so STP placed it into the **Alternate / Blocking** state to prevent a Layer 2 loop.

---

## Testing Link Failure

To simulate a network failure, the direct connection between S1 and S3 was administratively disabled.

On S1:

```text
enable
configure terminal
interface gi0/2
shutdown
```

This caused S3 to lose its direct path to the Root Bridge.

---

## STP Reconvergence

After the S1-to-S3 link failed, STP recalculated the topology.

S3's Gi0/2 changed from:

```text
Altn   BLK
```

to a new **Root Port**.

During convergence, the port was observed in the Learning state:

```text
Gi0/2   Root   LRN
```

After STP completed convergence:

```text
Gi0/2   Root   FWD
```

S3 could now reach the Root Bridge using the redundant path:

```text
S3 → S2 → S1
```

The root path cost increased from:

```text
4 → 8
```

because S3 now had to traverse two Gigabit Ethernet links instead of one.

---

## Restoring the Original Link

The failed link was restored with:

```text
configure terminal
interface gi0/2
no shutdown
end
```

After STP reconverged, S3 returned to its original port roles:

```text
Gi0/1   Root   FWD
Gi0/2   Altn   BLK
```

The direct S3-to-S1 connection once again became the preferred path because it had the lower root path cost.

---

## Verification Commands

Commands used during the lab included:

```text
show spanning-tree
```

Interface configuration commands used to simulate and restore the link failure:

```text
configure terminal
interface gi0/2
shutdown
no shutdown
```

---

## Key Concepts Practiced

- Spanning Tree Protocol (STP)
- IEEE 802.1D concepts
- Layer 2 loop prevention
- Redundant network paths
- Root Bridge election
- Bridge ID
- Bridge priority
- MAC address tiebreakers
- Root Ports
- Designated Ports
- Alternate Ports
- Forwarding and Blocking states
- STP path cost
- STP convergence and reconvergence
- Link failure recovery

---

## Lab Result

**Successful**

STP successfully prevented a Layer 2 switching loop by placing a redundant port into a blocking state.

When the active path between S1 and S3 was disabled, STP recalculated the topology and transitioned the redundant path into a forwarding state.

When the original connection was restored, STP reconverged and returned the redundant link to an alternate/blocking role.

This demonstrated how STP allows physical redundancy while maintaining a loop-free Layer 2 topology.

---

## Packet Tracer File

`lab-06-spanning-tree-protocol.pkt`
