# 🔬 CCNA Lab – Cisco Catalyst 2960-C Home Lab

> Hands-on CCNA exam prep using a physical Cisco Catalyst 2960-C switch, a Kali Linux machine, and a Windows machine. This repository documents configurations, topology, and lessons learned across all major CCNA domains.

---

## 🖥️ Lab Hardware

| Device | Role |
|--------|------|
| Cisco Catalyst 2960-C | Layer 2 Switch |
| PC #1 – Windows | Simulated Sales workstation (VLAN 20) |
| PC #2 – Kali Linux | Simulated IT/Security workstation (VLAN 30) |

---

## 🗺️ Network Topology

```
                        [ Cisco Catalyst 2960-C ]
                       /           |              \
              Fa0/1               Fa0/2            Fa0/3 (Trunk/Uplink)
              |                   |
        [Windows PC]         [Kali Linux]
         VLAN 20               VLAN 30
        192.168.20.x          192.168.30.x
```

### VLAN Design

| VLAN | Name       | Subnet           | Purpose                  |
|------|------------|------------------|--------------------------|
| 10   | MANAGEMENT | 192.168.10.0/24  | Switch management (SVI)  |
| 20   | SALES      | 192.168.20.0/24  | Windows PC               |
| 30   | IT         | 192.168.30.0/24  | Kali Linux PC            |
| 99   | NATIVE     | —                | Trunk native VLAN        |
| 999  | BLACKHOLE  | —                | Unused/disabled ports    |

---

## 📁 Repository Structure

```
.
├── README.md
├── topology/
│   └── diagram.png              # Network diagram
├── configs/
│   ├── phase1-vlans.txt         # VLAN & trunking config
│   ├── phase2-dhcp-mgmt.txt     # DHCP & SSH management config
│   ├── phase3-routing.txt       # Static routes / Router-on-a-Stick
│   ├── phase4-security.txt      # ACLs & Port Security config
│   └── phase5-stp-ether.txt     # Spanning Tree & EtherChannel config
└── notes/
    ├── lessons-learned.md        # Troubleshooting notes & gotchas
    └── verification-commands.md  # Key show commands & expected outputs
```

---

## 🚀 Project Phases

### Phase 1 — VLANs & Trunking
- Created VLANs 10, 20, 30, 99, 999
- Assigned access ports to appropriate VLANs
- Configured trunk port with VLAN 99 as native VLAN
- Verified with `show vlan brief` and `show interfaces trunk`


---

### Phase 2 — DHCP & Management
- Configured SVI on VLAN 10 for switch management
- Set up DHCP pools for VLAN 20 and VLAN 30 directly on the switch
- Configured DHCP exclusions for reserved addresses
- Disabled Telnet; enforced SSHv2 with RSA 2048-bit keys
- Verified: both PCs receive addresses, SSH access works from both VLANs


---

### Phase 3 — Routing
- Practiced **Router-on-a-Stick** using Kali Linux as a simulated router with subinterfaces (`ip link`, `ip route`)
- Configured static routes manually
- Simulated OSPF concepts in Cisco Packet Tracer alongside the physical lab
- Verified inter-VLAN reachability with `traceroute`

> ⚠️ Note: The Catalyst 2960-C does not support full L3 inter-VLAN routing natively. A router or L3 switch is required for production inter-VLAN routing. Packet Tracer was used to supplement this phase.


---

### Phase 4 — Security (ACLs & Port Security)
- **Port Security** on PC-facing ports:
  - Max 1 MAC address per port
  - Violation mode: `shutdown` (err-disable)
  - Tested by spoofing MAC with `macchanger` on Kali — port shut down as expected
- **Named Extended ACL:**
  - Blocks ICMP from VLAN 30 (IT) → VLAN 20 (Sales)
  - Permits all other IP traffic
  - Applied inbound on the correct interface
- **DHCP Snooping:** Enabled and tested using Kali as a rogue DHCP server
- Disabled all unused ports and assigned them to VLAN 999


---

### Phase 5 — Spanning Tree & EtherChannel
- Verified STP operation: `show spanning-tree`
- Set switch as **root bridge** for all VLANs via priority manipulation
- Configured **PortFast** on all access ports
- Enabled **BPDU Guard** on access ports to prevent unauthorized switches
- Configured **EtherChannel (LACP)** by bundling two ports, verified with `show etherchannel summary`


---

## 🔧 Kali Linux Usage

Kali was used actively as a security testing tool throughout the lab:

| Tool | Purpose |
|------|---------|
| `nmap` | Network scanning to validate ACL rules |
| `hping3` | Custom packet crafting to test ACL filtering |
| `macchanger` | MAC spoofing to trigger Port Security |
| `isc-dhcp-server` | Rogue DHCP server to test DHCP Snooping |
| `ip link` / `ip route` | Subinterface config for Router-on-a-Stick simulation |

---

## ✅ Key Verification Commands

```bash
# VLANs & Trunking
show vlan brief
show interfaces trunk

# DHCP
show ip dhcp binding
show ip dhcp pool
show ip dhcp conflict

# Security
show port-security interface fa0/1
show access-lists
show ip dhcp snooping binding

# Spanning Tree & EtherChannel
show spanning-tree vlan 10
show spanning-tree detail
show etherchannel summary

# Management
show ip ssh
show users
show running-config
```

---

## 📚 Resources

- [Cisco IOS Command Reference](https://www.cisco.com/c/en/us/support/ios-nx-os-software/ios-15-4m-t/products-command-reference-list.html)
- [Cisco Catalyst 2960-C Datasheet](https://www.cisco.com/c/en/us/products/switches/catalyst-2960-c-series-switches/index.html)
- [Cisco Packet Tracer](https://www.netacad.com/courses/packet-tracer)
- [CCNA Exam Topics (200-301)](https://learningnetwork.cisco.com/s/ccna-exam-topics)

---

## 📝 License

This project is for educational purposes only. All configurations are lab-grade and not intended for production use.
