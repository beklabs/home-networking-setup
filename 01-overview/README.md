# 01 — Overview & Purpose

This folder is a concise, personal record of my home-networking lab. It is intended as a memory aid and portfolio artifact (what I built, why, and where to find things). It is not a step‑by‑step tutorial for others.

What this repo is for
- Personal reference: capture configuration choices, diagrams, and notes so I can re-run or update the lab in future.
- Portfolio: high‑level documentation that demonstrates my networking knowledge.
- Change history: short record of important changes and where to find backups/configs.

Quick snapshot
- Diagram (source): `diagrams/home-network.drawio`
- Diagram (export): `diagrams/home-network.drawio.png`

Device inventory
- AT&T modem (ISP) — management IP: 192.168.2.254 — IP Passthrough enabled
- NETGEAR router — LAN: 192.168.1.1 — DHCP: 192.168.1.100–199
- Cisco Catalyst 2960 (switch) — management IP: 192.168.1.2
- Ubuntu laptop/server — 192.168.1.4 (placeholder; full server docs live in a separate repo when ready)

Where to look next
- Core network details: `../02-core-network/README.md`
- Switch ports & map: `../03-switching/README.md`
- Wi‑Fi design & SSID notes: `../04-wifi-and-wireless/`
- Operations & troubleshooting: `../05-ops-and-troubleshooting/README.md`

Notes
- This overview intentionally avoids deep server internals. Server configuration and application-level details will be documented in a separate repo when ready.
