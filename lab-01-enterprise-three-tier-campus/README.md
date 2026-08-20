# Lab 01 – Enterprise Three-Tier Campus Architecture

## Senior Network Engineering Portfolio Project

This project demonstrates the design, implementation, verification, and troubleshooting of a production-style three-tier enterprise campus network using Cisco Modeling Labs (CML).

The architecture separates the network into Core, Distribution, and Access layers and implements dynamic routing, Layer 2 segmentation, redundant default gateways, Spanning Tree optimization, and redundant uplinks.

---

## 30–60 Second Project Summary

### Problem

Design an enterprise campus network that provides scalable Layer 2 and Layer 3 connectivity while eliminating single points of failure between the Core, Distribution, and Access layers.

### Solution

Built a three-tier hierarchical campus architecture in Cisco Modeling Labs consisting of redundant Core routers, redundant multilayer Distribution switches, four Access switches, and multiple end hosts.

The design uses OSPF Area 0 for Layer 3 routing, HSRPv2 for redundant default gateways, Rapid-PVST+ for Layer 2 loop prevention and deterministic root placement, and 802.1Q trunks for VLAN transport.

### Technologies

- Cisco Modeling Labs (CML)
- Cisco IOS / IOS-XE concepts
- Cisco ISR4321
- Cisco Catalyst 3650
- Cisco Catalyst 2960
- OSPF Area 0
- HSRP Version 2
- Rapid-PVST+
- IEEE 802.1Q
- VLANs
- Layer 3 SVIs
- Inter-VLAN Routing
- PortFast
- BPDU Guard
- Port Security
- SSH
- IPv4
- High Availability / Redundancy

### What I Demonstrated

- Enterprise campus architecture and design
- Layer 2 and Layer 3 network integration
- Dynamic routing
- First-hop redundancy
- Spanning Tree design
- VLAN segmentation
- Redundant network paths
- Configuration validation
- End-to-end verification
- Failure and failover testing
- Structured network troubleshooting
- Cisco CML lab development
- Engineering documentation

---

# Project Overview

This lab builds a complete three-tier enterprise campus network from the ground up inside Cisco Modeling Labs.

The network follows the traditional Cisco hierarchical campus model:

1. **Core Layer** – High-speed Layer 3 backbone
2. **Distribution Layer** – Inter-VLAN routing, gateway redundancy, and Layer 2 control
3. **Access Layer** – End-user and endpoint connectivity

The completed environment provides redundant Layer 2 and Layer 3 paths, dynamic routing, inter-VLAN connectivity, gateway failover, and controlled Spanning Tree behavior.

The lab is designed not simply as a configuration exercise, but as a repeatable engineering environment for architecture validation, troubleshooting, failure testing, and future automation development.

---

# Engineering Objectives

The primary objectives of this project are to:

- Design and deploy a hierarchical three-tier enterprise campus.
- Configure VLAN segmentation across the Distribution and Access layers.
- Implement Layer 3 SVIs for inter-VLAN routing.
- Deploy OSPF Area 0 throughout the routed infrastructure.
- Implement HSRPv2 for redundant VLAN default gateways.
- Align HSRP active gateways with Spanning Tree root placement.
- Implement Rapid-PVST+ for Layer 2 loop prevention.
- Configure redundant 802.1Q uplinks between Access and Distribution.
- Apply PortFast and BPDU Guard to appropriate host-facing interfaces.
- Apply access-layer port security.
- Verify Layer 1, Layer 2, and Layer 3 operation.
- Perform HSRP, OSPF, and Spanning Tree failover testing.
- Practice structured network troubleshooting.

---

# Architecture

## Core Layer

The Core layer consists of two Cisco ISR4321 routers:

- `CORE-R1`
- `CORE-R2`

The Core operates as a pure Layer 3 routing domain.

The routers provide redundant routed connectivity to the Distribution layer and communicate using point-to-point IPv4 networks running OSPF Area 0.

Loopback interfaces provide stable router identifiers:

| Device | Loopback |
|---|---|
| CORE-R1 | 10.0.0.1/32 |
| CORE-R2 | 10.0.0.2/32 |

The Core does not provide VLAN switching or end-user services. Its primary responsibility is reliable Layer 3 packet forwarding between Distribution-layer paths.

---

## Distribution Layer

The Distribution layer consists of:

- `DIST-SW1`
- `DIST-SW2`

These multilayer switches provide the primary Layer 2/Layer 3 boundary for the campus.

Responsibilities include:

- Layer 3 SVIs
- Inter-VLAN routing
- HSRPv2 default gateway redundancy
- Rapid-PVST+ root bridge placement
- 802.1Q connectivity to the Access layer
- Routed connectivity to the Core
- OSPF participation

Gateway and Spanning Tree responsibilities are intentionally distributed between the two switches.

### VLAN 10 and VLAN 20

`DIST-SW1` is:

- HSRP Active
- Spanning Tree Root

`DIST-SW2` provides the redundant path.

### VLAN 30, VLAN 40, and VLAN 100

`DIST-SW2` is:

- HSRP Active
- Spanning Tree Root

`DIST-SW1` provides the redundant path.

This alignment helps keep Layer 2 forwarding paths consistent with the active Layer 3 default gateway.

---

# Access Layer

The Access layer consists of four Cisco Catalyst switches:

- `ACC-SW1`
- `ACC-SW2`
- `ACC-SW3`
- `ACC-SW4`

Each Access switch connects redundantly to both Distribution switches using 802.1Q trunks.

The Access layer provides:

- VLAN-based endpoint connectivity
- Redundant Distribution uplinks
- 802.1Q VLAN transport
- Rapid-PVST+ participation
- PortFast on appropriate edge ports
- BPDU Guard
- Port security

Rapid-PVST+ prevents Layer 2 loops while maintaining redundant physical paths to the Distribution layer.

---

# VLAN Design

The campus uses multiple VLANs to demonstrate segmentation, inter-VLAN routing, gateway redundancy, and Spanning Tree path control.

Primary user/service VLANs include:

| VLAN | Function |
|---:|---|
| 10 | User / Access Network |
| 20 | User / Access Network |
| 30 | User / Access Network |
| 40 | User / Access Network |
| 100 | Additional User / Service Network |
| 99 | Management / Infrastructure VLAN |

VLANs are transported across selected 802.1Q trunks rather than allowing unnecessary VLANs across every trunk.

This provides a more controlled Layer 2 topology and demonstrates explicit trunk VLAN policy.

---

# First-Hop Redundancy – HSRPv2

HSRP Version 2 provides redundant default gateways for the campus VLANs.

The design distributes active gateway responsibility across the Distribution switches.

| VLAN | Active HSRP Device | Active Priority |
|---:|---|---:|
| 10 | DIST-SW1 | 110 |
| 20 | DIST-SW1 | 110 |
| 30 | DIST-SW2 | 110 |
| 40 | DIST-SW2 | 110 |
| 100 | DIST-SW2 | 110 |

The standby Distribution switch uses a lower priority.

HSRP preemption allows the preferred Distribution switch to resume the active role after recovering from a failure.

---

# Spanning Tree Design

Rapid-PVST+ provides Layer 2 loop prevention and deterministic forwarding paths.

Spanning Tree root placement is aligned with HSRP gateway placement.

| VLAN | STP Root | STP Secondary |
|---:|---|---|
| 10 | DIST-SW1 | DIST-SW2 |
| 20 | DIST-SW1 | DIST-SW2 |
| 30 | DIST-SW2 | DIST-SW1 |
| 40 | DIST-SW2 | DIST-SW1 |
| 100 | DIST-SW2 | DIST-SW1 |

This avoids unnecessarily forwarding traffic toward one Distribution switch at Layer 2 only to send it back toward the other Distribution switch for Layer 3 gateway processing.

---

# Dynamic Routing

OSPF Area 0 provides dynamic Layer 3 routing throughout the routed campus infrastructure.

OSPF is used between the Core and Distribution devices to provide:

- Dynamic route advertisement
- Automatic route convergence
- Redundant Layer 3 paths
- Loopback reachability
- Failure recovery
- Scalable routed infrastructure

Point-to-point addressing is used for routed infrastructure links.

---

# High Availability and Redundancy

Redundancy is implemented throughout the topology.

## Core

- Two Core routers
- Multiple Layer 3 point-to-point paths
- OSPF dynamic routing

## Distribution

- Two multilayer Distribution switches
- Redundant Core connectivity
- HSRPv2
- Redundant SVIs/default gateways
- Rapid-PVST+ root/secondary placement

## Access

- Dual Distribution uplinks
- 802.1Q trunks
- Rapid-PVST+ loop prevention
- Alternate Layer 2 paths

The objective is to allow individual link or device failures to occur without unnecessarily disrupting end-to-end connectivity.

---

# Verification and Testing

The lab includes structured verification at multiple layers.

## Layer 1 / Interface Verification

Examples:

```text
show ip interface brief
show interfaces status
show interfaces counters
