# Enterprise Network Design & Optimization Lab

## Overview
Multi-site enterprise network (HQ, Branch-A, Branch-B) with VLAN segmentation, inter-VLAN routing, OSPF, HSRP redundancy, ACLs, and basic QoS. Built in Cisco Packet Tracer.

## Topology
![Topology](Topology.png)

## IP Plan
- VLANs per site: 10 (HR), 20 (Finance), 30 (IT), 99 (Mgmt)
- HQ subnets: 10.10.1.0/24, 10.20.1.0/24, 10.30.1.0/24, 10.99.1.0/24
- Branch-A: X=2; Branch-B: X=3
- WAN: 192.168.12.0/30 (HQ-BA), 192.168.13.0/30 (HQ-BB)

## Key Config Highlights
- Router-on-a-stick for inter-VLAN routing
- OSPF area 0 across all routers
- HSRP on HQ mgmt VLAN (virtual GW 10.99.1.1)
- ACL blocking HR→IT
- Basic QoS on WAN with class-map for VOIP (rtp)

## Validation
- OSPF neighbors up: `show ip ospf neighbor`
- HSRP status: `show standby`
- ACL works: HR→IT ping blocked, HR→Finance allowed
- Branch-to-branch reachability confirmed

## Files
- `Enterprise Network Lab.pkt` – device running-configs
- `/Screens` – validation screenshots

## Notes / Lessons Learned
- Routing adjacencies and network design validation: Initially, OSPF neighbors didn’t form because the WAN interfaces were mismatched in subnet masks. Fixing that taught me how OSPF depends on exact interface configurations and that a single mismatch can isolate entire sites.
- Trunk and VLAN consistency: The first inter-VLAN pings failed because of a native VLAN mismatch between routers and switches. This made me pay attention to dot1Q tagging and the importance of consistent VLAN-to-subinterface mapping for router-on-a-stick setups.
- HSRP priority tuning and failover timing: Early in testing, both routers tried to become active. Adjusting priority values and enabling preemption stabilized the virtual gateway handover, showing how HSRP timers and priorities directly affect failover performance.
- Access control verification: When implementing ACLs, I over-restricted traffic and broke management access. Testing ACLs directionally (inbound/outbound) and sequencing denies before permits clarified how ACL order determines final packet behavior.
- QoS classification and monitoring: Packet Tracer doesn’t fully emulate real QoS behavior, but setting up class-maps and policy-maps helped reinforce the logic of matching protocols (like RTP) to priority queues, and understanding how service-policies are bound to interfaces.
