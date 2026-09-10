# Lab 03 – Static Routing

## Objective

Build a multi-router network in Cisco Packet Tracer and configure static routes to allow communication between remote LANs.

This lab builds on the Ethernet switching, ARP, default gateway, and basic routing concepts practiced in previous labs.

---

## Topology

PC1, PC2, and PC3 connect to S1 and use R1 as their default gateway.

PC4 connects to S2 and uses R1 as its default gateway.

PC5 connects to S3 and uses R2 as its default gateway.

R1 and R2 communicate across a /30 transit network.

```text
PC1 ─┐
PC2 ─┼── S1 ── R1 ────── R2 ── S3 ── PC5
PC3 ─┘          │
                S2
                │
               PC4
