# WLAN / Wireless Design

Purpose
This file captures how I arranged wireless in the home lab: SSID naming, bands, and the design reasoning for any guest or IoT segmentation.

Design notes
- Primary SSID: "MyHome" — for personal devices (phones, laptops). Security: WPA2/WPA3 mixed-mode where supported.
- Guest SSID: "MyHome-Guest" — isolated to internet-only (mapped to Guest VLAN if router supports it).
- IoT devices: Prefer to use wired when possible; if wireless, place in an IoT VLAN or separate SSID on 2.4GHz.

Why this approach
- Centralize Wi‑Fi on the NETGEAR router to avoid multiple SSIDs and management overhead.
- Keep IoT and guest traffic logically separated to reduce lateral movement risk and accidental access to personal devices.

NETGEAR R7000 — what I configured
- Enabled both 2.4GHz and 5GHz SSIDs with WPA2-PSK[AES]
- Created unique SSIDs for each band and for guest networks
- Guest networks enabled (internet-only)
- AT&T gateway Wi‑Fi radios were disabled so only NETGEAR handles wireless

Diagram
- See the repo diagram: `diagrams/home-network.drawio.png`

Notes
- If you later add dedicated APs, record model, placement, and channel plan here (avoid co-channel interference).
