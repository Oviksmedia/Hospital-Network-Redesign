# MARYLAND HEALTHCARE — NETWORK INFRASTRUCTURE UPGRADE REPORT

**Project Title:** Hospital Network Infrastructure Modernization & Redesign  
**Prepared By:** IT Administrator  
**Date of Execution:** April 4–6, 2026  
**Location:** Maryland Healthcare Facility  
**Report By:** Overcomer Israel, IT Administrator  
**Status:** ✅ Core Infrastructure — Complete  

---

## 1. Executive Summary

The hospital's internet and Wi-Fi infrastructure was suffering from chronic instability caused by a Triple DHCP conflict, fragmented network architecture, and degraded physical cabling. The main consumers of the network are clinical staff, and these issues severely impacted their workstations as well as the two clinical tablets used by doctors and nurses for ward rounds. The entire network has been systematically redesigned, reconfigured, and physically rewired to deliver a **centralized, stable, and high-performance infrastructure**.

The project was completed over the course of three working days, including approximately **6 hours of physical ceiling/roof work** involving cable routing, retrieval of abandoned cabling, extension of critical network drops to new office locations, and replacement of faulty hardware connectors to restore speeds on historically slow segments.

---

## 2. Pre-Existing Problems Identified

### 2.1 Triple DHCP Conflict (Critical — Network-Wide Impact)
Three separate routers were simultaneously acting as independent DHCP servers, each racing to assign IP addresses to connecting devices. This caused:
- Frequent Wi-Fi disconnections across the hospital
- IP address conflicts resulting in devices going offline
- Printers and workstations intermittently losing network connectivity
- Need for constant manual router reboots by hospital staff

**Affected Devices:**
| Device | Model | Problem |
|--------|-------|---------|
| TP-Link Archer C80 | AC1900 | Running as DHCP Server #1 |
| TP-Link TL-WR940N | Wireless N 450Mbps | Running as DHCP Server #2 |
| TP-Link TL-MR3420 | 3G/4G Wireless N | Running as DHCP Server #3 |

### 2.2 Double/Triple NAT
Multiple routers performing Network Address Translation created layered NAT chains, causing slower speeds, failed remote access attempts to the NVR camera system, and degraded application performance.

### 2.3 Fragmented Wi-Fi Networks
Each router was broadcasting a different, unrelated SSID (e.g., `MARYLAND WIFI`, `Maryland Healthcare`, `MHC Exten`, `MD Office`, `Maryland Guest`). Clinical staff had to manually switch Wi-Fi networks as they moved between different areas of the hospital, leading to dropped connections and poor user experience.

### 2.4 Degraded Physical Cabling
Ethernet cables throughout the ceiling infrastructure were found to have:
- Faulty crimps with wires not making proper contact inside RJ45 connectors
- A defective Ubiquiti PoE coupler/brick in the ceiling that was severely degrading signal quality
- Multiple abandoned and idle cables creating clutter and confusion in the cable runs

### 2.5 Aging Equipment
The Cisco Catalyst 2960 switch (manufactured June 2007) is 19 years old and running end-of-life firmware (IOS 12.2(35)SE). It continues to function as a basic Layer 2 switch but receives no security patches.

---

## 3. Work Performed

### 3.1 Project Execution Timeline

- **Day 1 (Saturday, April 4):** Conducted extensive physical infrastructure work, including routing new drops through the ceiling and retrieving abandoned idle cables.
- **Day 2 (Sunday, April 5):** Overcame difficulties with the crimping tool, terminating all connections, and structurally wiring the Cisco switch and the entire network backbone.
- **Day 3 (Monday, April 6):** Executed the logical reconfiguration by setting downstream routers to transparent Access Points, unifying the Wi-Fi system, and finally diagnosing and resolving the 10 Mbps speed bottleneck on the TL-MR3420.

### 3.2 Network Architecture Redesign

The entire network topology was redesigned around a **single-gateway, centralized DHCP** architecture:

**Before:** Three competing routers, three DHCP servers, three separate Wi-Fi networks.  
**After:** One gateway router managing all IP assignments, with all other devices operating as transparent Access Points broadcasting a unified Wi-Fi network.

#### New Network Topology

| Device | IP Address | Role | Status |
|--------|-----------|------|--------|
| TP-Link Archer C80 | `192.168.0.1` | **Primary Gateway & Sole DHCP Server** | ✅ Complete |
| TP-Link TL-WR940N | `192.168.0.2` | Access Point (DHCP Disabled) | ✅ Complete |
| TP-Link TL-MR3420 | `192.168.0.3` | Access Point (DHCP Disabled) | ✅ Complete |
| Ubiquiti UniFi UAP-LR | `192.168.0.4` | Access Point (DHCP Disabled) | ✅ Complete |
| Cisco Catalyst 2960 | — | Layer 2 Distribution Switch | ✅ Operational |
| Hikvision NVR | DHCP / Static Pending | Camera Recorder | ✅ Operational |
| Hikvision PoE Switch | — | Camera Power & Data | ✅ Operational |

#### Unified Wi-Fi Network
All four Access Points now broadcast an identical, synchronized configuration:
- **SSID:** `MARYLAND WIFI`
- **Security:** WPA2-PSK / AES
- **Password:** Secured (documented separately for authorized personnel only)

This enables **seamless roaming** — doctors, nurses, and other staff can walk through the entire hospital with their clinical tablets during ward rounds, automatically transitioning between Access Points without dropping their internet connection or disrupting clinical care applications.

### 3.3 Gateway Router Configuration (Archer C80)

- Confirmed and secured as the sole DHCP server for the entire network
- DHCP range configured to serve connected devices
- Static IP assignments reserved for all infrastructure devices (`.1` through `.9`)
- Admin password secured
- **Firmware upgraded** from version `1.11.30 Build 220624` (2022) to latest version `1.13.15 Build 240812` (2024), applying two years of critical security patches, DNS-over-HTTPS support, HTTPS admin access, and IoT network isolation capabilities

### 3.4 Access Point Conversions

#### TP-Link TL-WR940N (192.168.0.2)
- DHCP server disabled
- LAN IP changed from default `192.168.0.1` to `192.168.0.2`
- Wi-Fi SSID changed to `MARYLAND WIFI` with matching security settings
- Internet cable moved from WAN port to LAN port to operate as a transparent bridge
- DNS settings configured to point to gateway (`192.168.0.1`)

#### TP-Link TL-MR3420 (192.168.0.3)
- DHCP server disabled
- LAN IP changed to `192.168.0.3`
- Wi-Fi SSID changed to `MARYLAND WIFI` with matching security settings
- Internet cable connected via LAN port (not WAN)

#### Ubiquiti UniFi UAP-LR (192.168.0.4)
- UniFi Controller software installed and configured on administrator workstation
- Resolved **port 8080 conflict** caused by a zombie Java process (PID 98664) blocking the controller's communication port — terminated via system process manager
- Resolved **adoption loop** where the AP was failing to connect to the controller — performed 10-second hard factory reset on the UAP-LR and cleared UniFi software cache
- Successfully adopted the AP into the UniFi Controller
- Assigned static IP `192.168.0.4`
- Configured SSID: `MARYLAND WIFI` with matching WPA2 security
- Disabled "Rogue DHCP Server Detection" to prevent false alerts against the legitimate Archer C80 gateway

### 3.5 Physical Infrastructure Work (Ceiling/Roof — ~6 Hours)

This was the most physically demanding portion of the project, involving extended work in the hospital's ceiling cavity:

#### Cable Routing & Extension
- **Extended the TL-WR940N network drop** to the new Managing Director's office location, routing new Ethernet cable through the ceiling infrastructure
- **Extended the NVR (camera recorder) connection** to accompany the WR940N at the new MD office location
- All cables terminated using the **T568B wiring standard** (White-Orange, Orange, White-Green, Blue, White-Blue, Green, White-Brown, Brown) on both ends

#### Cable Retrieval & Cleanup
- Discovered and retrieved **multiple idle/abandoned Ethernet cables** throughout the ceiling that were no longer connected to any equipment
- Removed unused cable runs to reduce clutter, prevent confusion during future maintenance, and recover reusable cable stock

#### Troubleshooting the Historically Slowest Router (10 Mbps to 153 Mbps)
- Investigated the network branch serving the TL-MR3420, which had historically been the slowest router at the facility.
- Even after converting the MR3420 to an Access Point, performance remained unusually poor. 
- Conducted a direct wired test by connecting the AP's supply cable directly into a diagnostic laptop, revealing a severe **10 Mbps speed bottleneck**.
- Traced the cable run into the ceiling and identified a **defective Ubiquiti PoE coupler/brick** that was severely degrading the signal.
- Cable tester diagnostics revealed: 2 wires completely dead (no continuity) and 1 wire with dim/weak signal (high resistance).
- This faulty coupler was forcing the Cisco switch to auto-negotiate down to 10 Mbps (from a potential 100 Mbps) as a fail-safe.
- **Replaced** the defective coupler with a clean, new RJ45 standard coupler and restored the connection.
- **Result:** Network speed on this specific cable run experienced a **15× speed increase** — jumping from **10 Mbps to 153 Mbps** directly, and restoring the MR3420 Access Point to its maximum 91+ Mbps wireless throughput.

#### Crimping Tool Challenges
- Encountered issues with the crimping tool during RJ45 connector termination
- Overcame the tooling difficulty through persistence and careful technique to achieve proper wire-to-pin contact on all terminated connectors

### 3.6 Speed Verification Results

| Test Point | Before Fix | After Fix | Improvement |
|-----------|-----------|----------|-------------|
| Direct cable (laptop via ceiling cable) | 10 Mbps | **152.94 Mbps** | **15x faster** |
| Via TL-MR3420 Wi-Fi | 10 Mbps | **91.40 Mbps** | **9x faster** |

The 91.40 Mbps result on the TL-MR3420 represents approximately **91% of its 100 Mbps port capacity**, confirming the router is performing at its hardware maximum.

---

## 4. Equipment Inventory & Disposition

### Active Infrastructure
| # | Device | Model | Role | IP Address |
|---|--------|-------|------|-----------|
| 1 | Starlink Dish | — | Internet Source (Roof-Mounted) | — |
| 2 | Starlink Router | UTR-211 | ISP Gateway / Dish Power | — |
| 3 | Starlink Ethernet Adapter | — | Converts proprietary cable to RJ45 | — |
| 4 | TP-Link Archer C80 | AC1900 MU-MIMO | Primary Gateway & DHCP Server | `192.168.0.1` |
| 5 | Cisco Catalyst 2960 | WS-C2960-8TC-L | Layer 2 Distribution Switch | — |
| 6 | TP-Link TL-WR940N | 450Mbps Wireless N | Wi-Fi Access Point | `192.168.0.2` |
| 7 | TP-Link TL-MR3420 | 3G/4G Wireless N v5.0 | Wi-Fi Access Point | `192.168.0.3` |
| 8 | Ubiquiti UniFi UAP-LR | 802.11n Long-Range | Wi-Fi Access Point | `192.168.0.4` |
| 9 | Hikvision NVR | 16-Channel PoE | Camera Recorder | DHCP (Static Recommended) |
| 10 | Hikvision PoE Switch | DS-3E0318P-E/M (16-Port) | Camera Power & Data | — |

### Reserve / Spare Equipment
| Device | Model | Status | Recommended Use |
|--------|-------|--------|----------------|
| D-Link DIR-612 | Wireless N300 | Stored | Emergency backup router |
| TP-Link TL-SF1005D | 5-Port 10/100 Switch | Stored | Expansion of wired ports if needed |
| Ubiquiti PoE Injector | 24V | Available | Spare power injector for UniFi AP |

---

## 5. Recommendations for Future Action

### 5.1 Immediate (Within 1 Week)
- **Assign a Static IP to the Hikvision NVR** (e.g., `192.168.0.20`) to prevent the remote camera viewing app from losing connectivity after power fluctuations or router reboots
- **Verify remote camera access** by testing the Hik-Connect app over cellular data (4G) to confirm the NVR is correctly routing through the new gateway

### 5.2 Short-Term (Within 1 Month)

- **Document all static IP assignments** in an internal IT reference sheet and store in a secure, accessible location for future administrators
- **Re-crimp any remaining suspect cables** identified during the ceiling work, using cable tester verification on all 8 wires before deployment

### 5.3 Long-Term (3–6 Months)
- **Plan replacement of the Cisco Catalyst 2960** — the switch is 19 years old with end-of-life firmware. A modern managed switch would provide better security, VLAN support, and monitoring capabilities
- **Consider upgrading the TL-WR940N and TL-MR3420** to Wi-Fi 5 or Wi-Fi 6 Access Points when budget permits. Both units are limited to 2.4GHz / 100Mbps ports, which restricts throughput in their coverage areas
- **Implement network segmentation** (VLANs) to isolate camera traffic, staff devices, and guest devices onto separate logical networks for improved security and performance

---

## 6. Summary of Outcomes

| Metric | Before | After |
|--------|--------|-------|
| DHCP Servers on Network | 3 (conflicting) | 1 (centralized) |
| Wi-Fi Network Names (SSIDs) | 3+ (fragmented) | 1 unified (`MARYLAND WIFI`) |
| Seamless Roaming | ❌ No | ✅ Yes — across all 4 Access Points |
| Network Speed (affected cable run) | 10 Mbps | 91–153 Mbps |
| Printer/Device Connectivity | Intermittent failures | Stable |
| Gateway Firmware | 2022 (outdated) | 2024 (current, patched) |
| Abandoned Ceiling Cables | Multiple idle runs | Retrieved and cleaned |
| Physical Cable Faults | Faulty coupler degrading speed | Replaced — full throughput restored |

The hospital's core network infrastructure is now operating on a stable, centralized, and professionally organized foundation. Clinical staff now experience reliable Wi-Fi connectivity with seamless roaming across the entire facility—greatly benefiting the doctors and nurses relying on the two clinical tablets during ward rounds. Furthermore, eliminating the 10 Mbps physical bottleneck has unlocked 15x faster speeds on previously degraded network segments, ensuring all applications run smoothly.

---

*End of Report*
