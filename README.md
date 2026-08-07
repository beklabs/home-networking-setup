# Home Networking Setup

This repository documents my personal home network: the topology, core network choices, switch setup, and operational notes. It's a memory aid and portfolio artifact rather than a public how-to guide.

## Table of Contents

- [Quick Snapshot](#quick-snapshot)
- [Overview & Purpose](#overview--purpose)
- [Device Inventory](#device-inventory)
- [Core Network (WAN & Router)](#core-network-wan--router)
- [Switching (Cisco Catalyst 2960)](#switching-cisco-catalyst-2960)
- [WiFi & Wireless](#wifi--wireless)
- [Operations & Troubleshooting](#operations--troubleshooting)
- [Diagrams & Assets](#diagrams--assets)

---

## Quick Snapshot

- **Diagram (source):** `diagrams/home-network.drawio`
- **Diagram (export):** `diagrams/home-network.drawio.png`
- **ISP modem:** AT&T BGW320-500 (IP Passthrough enabled)
- **Router:** NETGEAR Nighthawk R7000 (LAN: 192.168.1.0/24)
- **Switch:** Cisco Catalyst 2960 (L2, management at 192.168.1.2)
- **Configs:** Sanitized device exports in `configs/`

---

## Overview & Purpose

This repo serves three purposes:
- **Personal reference:** Capture configuration choices, diagrams, and notes so I can re-run or update the lab in the future.
- **Portfolio:** Demonstrate networking knowledge through high-level documentation.
- **Change history:** Record important changes and where to find backups/configs.

**Note:** Server/application-level details are intentionally minimal here. Full server documentation will live in a separate repo when ready.

---

## Device Inventory

| Device | Model | Role | IP Address | Notes |
|--------|-------|------|------------|-------|
| ISP Modem | AT&T BGW320-500 | Gateway | 192.168.2.254 | Management only; IP Passthrough enabled |
| Router | NETGEAR Nighthawk R7000 | WAN/LAN gateway | 192.168.1.1 | DHCP pool: 192.168.1.100–199 |
| Switch | Cisco Catalyst 2960 | L2 switching | 192.168.1.2 | Management IP on VLAN 1 |
| Server/Laptop | Ubuntu | Wired client | 192.168.1.4 | Placeholder; full docs in separate repo |

---

## Core Network (WAN & Router)

### WAN / Modem Setup

**Device:** AT&T BGW320-500  
**Management IP:** 192.168.2.254 (changed to avoid conflict with router LAN)  
**Key setting:** IP Passthrough enabled so the NETGEAR router receives the public IP (avoids double NAT)

#### AT&T BGW320-500 — IP Passthrough Steps

1. Navigate to `http://192.168.1.254` and log in with the modem access code.
2. Go to **Firewall** → **IP Passthrough**.
3. Set **Allocation Mode:** `Passthrough`
4. Set **Passthrough Mode:** `DHCPS-fixed`
5. Select or enter the MAC address of the NETGEAR router.
6. Save and apply changes.

#### Change Modem LAN Management IP

1. Go to **Home Network** → **Subnets & DHCP**.
2. Set **Device IPv4 Address** to `192.168.2.254`.
3. Save and reboot the modem.

#### Disable AT&T Wi-Fi Radios

1. Go to **Home Network** → **Wi-Fi**.
2. Turn OFF **Home SSID Enable** and **Guest SSID Enable**.
3. Save settings.

### Router (NETGEAR R7000)

**Gateway:** 192.168.1.1/24  
**DHCP pool:** 192.168.1.100–192.168.1.199  
**DNS:** Automatic from ISP (or 1.1.1.1 / 8.8.8.8)

#### NETGEAR R7000 — Setup Steps

1. Connect modem Ethernet → Router WAN (yellow port) and power on.
2. Connect a LAN port to your PC and log into `http://www.routerlogin.net`.
3. Change admin password at **Advanced** → **Administration** → **Set Password**; enable password recovery.
4. Set **Internet IP** to `Get Dynamically from ISP`.
5. Leave **Router MAC** as `Use Default Address`.
6. Enable wireless (2.4GHz & 5GHz) with WPA2-PSK[AES] and unique SSIDs.
7. Enable guest networks for both bands with WPA2-PSK[AES] and separate SSIDs.

### Basic Hardening Notes

- Changed admin passwords from defaults.
- Disabled remote/WAN-side admin access.
- Disabled AT&T Wi-Fi so only NETGEAR SSIDs are in use.
- Record firmware versions here after updates.

### Quick Verification

From a LAN client:
```bash
ping 192.168.1.1              # Router
curl -s ifconfig.me           # Check public IP (if passthrough worked)
```

If router WAN is still private, re-check IP Passthrough settings on the AT&T modem.

---

## Switching (Cisco Catalyst 2960)

**Model:** Cisco Catalyst 2960 (L2)  
**Management IP:** 192.168.1.2  
**Role:** Layer 2 switching for wired endpoints; management on VLAN 1 (for now)

### Cisco 2960 — Express Setup Steps

1. Ensure no devices are connected to the switch before beginning.
2. Ensure your PC is set to DHCP (the switch acts as a DHCP server during setup).
3. Connect power to the switch and wait for POST to complete (SYST LED will blink green).
4. Press and hold the MODE button until LEDs above it turn green, then release (Express Setup mode).
5. Connect a Cat5e cable from Fa0/1 on the switch to your PC. Verify port LEDs are green.
6. Wait ~30s, then open a browser and go to `http://10.0.0.1` to access Express Setup.

### Express Setup — Network Settings

- **Management Interface (VLAN ID):** 1
- **IP Address:** 192.168.1.2
- **Subnet Mask:** 255.255.255.0
- **Default Gateway:** 192.168.1.1
- **Switch Password:** [secure admin password]

### Express Setup — Optional Settings

- **Hostname:** tinfoil
- **Telnet:** disabled (use SSH when available)
- **SNMP:** disabled (security concerns on older switch; evaluate after obtaining console cable)

### Recovery Notes

If the switch becomes unreachable, connect via console and run:
```bash
erase startup-config
reload
```

Then re-run Express Setup to return to factory defaults.

### Configuration Storage

Save sanitized `show running-config` output to `configs/switch/` and redact secrets before committing.

### VLANs

Currently, no production VLAN segmentation is in use. If VLANs are added later, document VLAN IDs and port assignments here.

---

## WiFi & Wireless

### Design Approach

- **Primary SSID:** "MyHome" — for personal devices (phones, laptops). Security: WPA2/WPA3 mixed-mode where supported.
- **Guest SSID:** "MyHome-Guest" — isolated to internet-only (mapped to Guest VLAN if router supports it).
- **IoT devices:** Prefer wired when possible. If wireless, place in an IoT VLAN or separate SSID on 2.4GHz.

**Rationale:** Centralize Wi-Fi on the NETGEAR router to reduce management overhead. Keep IoT and guest traffic logically separated to reduce lateral movement risk.

### NETGEAR R7000 — Wireless Configuration

- Enabled both 2.4GHz and 5GHz SSIDs with WPA2-PSK[AES]
- Created unique SSIDs for each band and for guest networks
- Guest networks enabled (internet-only, no LAN access)
- AT&T gateway Wi-Fi radios disabled so only NETGEAR handles wireless

### SSID Security & Settings

| SSID | Security | Mode | Notes |
|------|----------|------|-------|
| MyHome | WPA2/WPA3 mixed | Primary | Personal devices only |
| MyHome-Guest | WPA2-PSK[AES] | Guest | Internet-only; guest isolation enabled |

**Additional security notes:**
- WPS: disabled on ISP modem and NETGEAR
- Remote admin: disabled on WAN side for all devices
- Firmware: check router/modem firmware versions periodically and update; record versions here when updated

**Passphrases:** Stored in password manager (not in this repo). Update this file with the date if you change a password or SSID; do not store passphrases in version control.

### Future: Dedicated APs

If you later add dedicated access points, record model, placement, and channel plan here to avoid co-channel interference.

---

## Operations & Troubleshooting

### Quick Health Checks

From a LAN client:
```bash
ping 192.168.1.1              # Router
ping 192.168.1.2              # Switch mgmt
ping 192.168.1.4              # Server
curl -s ifconfig.me           # Check public IP (if passthrough enabled)
dig @1.1.1.1 example.com      # Verify external DNS
```

### Common Recovery Steps

| Issue | Steps |
|-------|-------|
| Router not reachable | Power-cycle in order: modem → router → switch |
| Switch management lost | Connect via console; check VLAN and IP on VLAN interface |
| Internet is down | Verify modem status; check whether router WAN shows public IP (if passthrough enabled) |

### Backups & Config Storage

- Router/switch configs: save sanitized text to `configs/` before committing
- Export draw.io source and PNG to `diagrams/` as the canonical topology
- Do not store secrets or passphrases in version control

### Change Log

- **2026-08-07** — Consolidated 5-chapter docs into single README with table of contents; includes core network, switch setup, and WiFi details.
- *(Add subsequent entries here with date and short note)*

---

## Diagrams & Assets

- **Draw.io source:** `diagrams/home-network.drawio`
- **Exported PNG:** `diagrams/home-network.drawio.png`
- **Device configs (sanitized):** `configs/`
