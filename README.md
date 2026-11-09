# Financial Institution Enterprise Network Design & Implementation

A hands on enterprise network project simulated in Cisco Packet Tracer. The topology simulates a secure multi department headquarters connecting to an off site Data Center across two service providers.

## Topology Overview

The setup includes:
- **Headquarters (HQ):** Segmented into 5 departments across two floors using 802.1Q VLANs:
  - HR (`192.168.20.0/26`)
  - Customer Service (`192.168.20.64/26`)
  - Marketing (`192.168.20.128/26`)
  - Legal (`192.168.20.192/27`)
  - IT (`192.168.20.224/27`)
  - Voice VLAN 120 (`10.10.10.0/24`) handling Cisco CME telephony
- **Data Center (DC):** Houses core services (DHCP, DNS, Web, and Mail) on subnet `192.168.21.0/28`.
- **WAN Edge:** Dual-homed serial connections across two simulated ISPs (Safaricom & JTL).

## What Was Implemented

- **Site-to-Site IPsec VPN:** Established an encrypted tunnel between the HQ edge and Data Center edge routers.
  - Phase 1 (ISAKMP): AES-256, Diffie-Hellman Group 5, Pre-Shared Key.
  - Phase 2 (IPsec): ESP-AES with HMAC-SHA integrity check.
  - Interesting traffic matched via extended ACLs.
- **Routing:** OSPF Area 0 configured across core multilayer switches and edge routers. Aggregated HQ departments into a single summary route (`192.168.20.0/24`) to keep routing tables lean.
- **NAT Bypass:** Configured route-exemption policies so internal VPN packets bypass PAT overload when heading across the WAN.
- **Access Control & Device Hardening:**
  - Standard ACL on line vty limiting SSH management strictly to the IT subnet.
  - Switchport security (sticky MAC, violation shutdown) enabled on server-facing switch interfaces.
- **VoIP Integration:** Cisco 2811 Voice Gateway configured with telephony services to assign directory numbers (401–420) over VLAN 120.

## Verification

Key checks run during testing:
- `show crypto isakmp sa` (confirmed Phase 1 state is `QM_IDLE`)
- `show crypto ipsec sa` (verified encaps/decaps packet counters incrementing with zero drops)
- `show ip ospf neighbor` (checked full adjacency across core devices)
- Cross-subnet ping tests from host PCs to Data Center servers.

## Repo Contents

- `topology/` - Packet Tracer `.pkt` file
- `configs/` - Running configurations exported directly from HQ and DC routers
- `screenshots/` - Topology view and CLI verification outputs
