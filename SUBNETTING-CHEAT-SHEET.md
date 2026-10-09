
# CCNA 200-301 | Subnetting Cheat Sheet

**Study Notes: Wendell Odom CCNA Official Cert Guide, Volume 1 — Chapters 11–15**

This cheat sheet covers IPv4 subnetting formulas, subnet design, subnet IDs, broadcast addresses, and usable host ranges.

---

## 1. Essential Subnetting Formulas

| What You Need | Formula |
|---|---|
| Subnet bits (S) | New prefix - Original prefix |
| Host bits (H) | 32 - New prefix |
| Number of subnets | 2^S |
| Usable hosts per subnet | 2^H - 2 |
| Block size | 256 - Interesting mask octet |
| Check subnet design | S + H must fit available bits |

**Remember:**
- S = Borrowed subnet bits
- H = Remaining host bits
- The number of subnet bits is NOT the number of subnets.
- When given an assigned network, use its assigned prefix as the starting point.

### Example

Network: `192.168.40.0/24`

New prefix: `/27`

S = 27 - 24 = 3

Number of subnets = 2^3 = **8**

H = 32 - 27 = 5

Usable hosts = 2^5 - 2 = **30**

**Answer: 8 subnets with 30 usable hosts each.**

---

## 2. Convert Prefix Lengths to Subnet Masks

A prefix tells us how many bits are set to 1 in the subnet mask.

### Example: /27

Binary mask:

`11111111.11111111.11111111.11100000`

Dotted-decimal mask:

`255.255.255.224`

**Answer: /27 = 255.255.255.224**

### Reverse Example

Mask: `255.255.248.0`

- First octet: 8 network bits
- Second octet: 8 network bits
- Third octet: 5 network bits

8 + 8 + 5 = 21

**Answer: /21**

---

## 3. Calculate the Number of Subnets

**Formula: Number of subnets = 2^S**

### Example

Assigned network: `192.168.10.0/24`

New prefix: `/27`

S = 27 - 24 = 3

2^3 = 8

**Answer: 8 subnets**

---

## 4. Calculate Usable Hosts

**Formula: Usable hosts = 2^H - 2**

### Example

Prefix: `/26`

H = 32 - 26 = 6

2^6 - 2 = 62

**Answer: 62 usable hosts per subnet**

We subtract 2 for the subnet ID and broadcast address in conventional IPv4 subnets.

---

## 5. Choose a Mask Based on Required Subnets

### Example

Assigned network: `192.168.50.0/24`

Requirement: At least **6 subnets**

Step 1: Find S.

- 2^2 = 4 (not enough)
- 2^3 = 8 (enough)

S = 3

Step 2: Calculate the new prefix.

24 + 3 = 27

Step 3: Find the mask.

`/27 = 255.255.255.224`

**Answer: /27, providing 8 subnets**

---

## 6. Choose a Mask Based on Required Hosts

### Example

Assigned network: `172.20.0.0/16`

Requirement: At least **500 usable hosts per subnet**

Step 1: Find H.

- 2^8 - 2 = 254 (not enough)
- 2^9 - 2 = 510 (enough)

H = 9

Step 2: Find the new prefix.

32 - 9 = 23

Step 3: Calculate the number of subnets.

S = 23 - 16 = 7

2^7 = 128

**Answer: /23, providing 128 subnets and 510 usable hosts each**

---

## 7. Design a Network Using BOTH Requirements

### Example

Assigned network: `192.168.40.0/24`

Requirements:
- At least 6 subnets
- At least 25 usable hosts per subnet

Step 1: Calculate S.

2^3 = 8

S = 3

Step 2: Calculate H.

2^5 - 2 = 30

H = 5

Step 3: Check available bits.

A /24 has 8 bits available.

S + H = 3 + 5 = 8

Both requirements fit!

Step 4: Find the new prefix.

24 + 3 = 27

**Answer: /27 — 8 subnets, 30 usable hosts each**

### What If the Requirements Don't Fit?

Assigned network: `192.168.100.0/24`

Requirements:
- 12 subnets
- 25 usable hosts each

Subnet bits required:

2^4 = 16

S = 4

Host bits required:

2^5 - 2 = 30

H = 5

Total bits needed:

4 + 5 = 9

Available bits in /24:

32 - 24 = 8

**Answer: IMPOSSIBLE with the assigned /24 using equal-sized subnets.**

A larger assigned network would be required.

---

## 8. Find the Subnet ID and Broadcast Address

### Steps

1. Identify the interesting octet.
2. Calculate the block size.
3. Find the subnet boundaries.
4. Identify the subnet ID.
5. Find the broadcast address.
6. Calculate the first and last usable hosts.

**Formula: Block size = 256 - interesting mask octet**

### Example: Fourth-Octet Subnetting

IP address: `192.168.10.70/26`

Mask: `255.255.255.192`

Step 1: Find block size.

256 - 192 = 64

Step 2: Find boundaries.

0, 64, 128, 192

70 falls between 64 and 128.

Step 3: Find addresses.

| Address Type | Address |
|---|---|
| Subnet ID | 192.168.10.64 |
| First usable | 192.168.10.65 |
| Last usable | 192.168.10.126 |
| Broadcast | 192.168.10.127 |

**Remember:**
- Subnet ID = First address in the subnet
- Broadcast = Last address in the subnet
- First usable = Subnet ID + 1
- Last usable = Broadcast - 1

---

## 9. Third-Octet Subnetting

### Example

IP address: `172.25.186.42/20`

Mask: `255.255.240.0`

Step 1: Find block size.

256 - 240 = 16

Step 2: Find third-octet boundaries.

144, 160, 176, 192, 208

186 falls between 176 and 192.

Step 3: Find addresses.

| Address Type | Address |
|---|---|
| Subnet ID | 172.25.176.0 |
| First usable | 172.25.176.1 |
| Last usable | 172.25.191.254 |
| Broadcast | 172.25.191.255 |

**Important:** When subnetting in the third octet, the subnet ID ends in `.0`, and the broadcast address ends in `.255`.

---

## 10. Find ALL Subnet IDs

### Example

Assigned network: `192.168.40.0/24`

New prefix: `/27`

Mask: `255.255.255.224`

Block size:

256 - 224 = 32

Start at zero and increase by 32.

```text
192.168.40.0/27
192.168.40.32/27
192.168.40.64/27
192.168.40.96/27
192.168.40.128/27
192.168.40.160/27
192.168.40.192/27
192.168.40.224/27
```

**Answer: 8 total subnets**

---

## 11. Quick Subnet Mask Reference

| Prefix | Subnet Mask | Block Size |
|---|---|---|
| /20 | 255.255.240.0 | 16 (3rd octet) |
| /21 | 255.255.248.0 | 8 (3rd octet) |
| /22 | 255.255.252.0 | 4 (3rd octet) |
| /23 | 255.255.254.0 | 2 (3rd octet) |
| /24 | 255.255.255.0 | 256 addresses |
| /25 | 255.255.255.128 | 128 |
| /26 | 255.255.255.192 | 64 |
| /27 | 255.255.255.224 | 32 |
| /28 | 255.255.255.240 | 16 |
| /29 | 255.255.255.248 | 8 |
| /30 | 255.255.255.252 | 4 |

---

## 12. Common Subnetting Mistakes

Before submitting an answer, check the following:

- [ ] Did I copy all four octets of the IP address correctly?
- [ ] Did I calculate 2^S instead of confusing S with the total number of subnets?
- [ ] Is my subnet boundary a multiple of the block size?
- [ ] Did I distinguish the subnet ID from the first usable host?
- [ ] Did I distinguish the last usable host from the broadcast address?
- [ ] Did I verify BOTH subnet and host requirements?
- [ ] Did I double-check the interesting octet?

---

## 13. My CCNA Subnetting Strategy

When solving a subnetting problem:

1. Identify what the question is asking.
2. Write down the known IP address, prefix, and requirements.
3. Calculate S, H, or block size as needed.
4. Solve the problem.
5. Verify all four octets.
6. Double-check the final answer.

**Practice goal:** Solve subnetting problems accurately without relying on notes.

---

*Created as part of my CCNA 200-301 certification studies and hands-on networking portfolio.*

*Note: The standard 2^H - 2 formula applies to conventional IPv4 subnets. /31 point-to-point links and /32 host routes are special cases.*
