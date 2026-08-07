# Home Networking Setup — Lab & Portfolio

Purpose
- Lab documentation for my home network: a clear, maintainable reference and a portfolio piece demonstrating practical networking skills.

High-level summary
- Single-site home network built around an AT&T BGW320-500 gateway, NETGEAR Nighthawk R7000 router, and a Cisco Catalyst 2960 managed switch. The AT&T gateway is set to IP Passthrough so the NETGEAR receives the public WAN IP and performs routing/NAT. The switch is managed with a static management IP and wired hosts run on a single flat LAN (no VLANs at this time).

Equipment & firmware
- AT&T Gateway: BGW320-500
- NETGEAR Router: Nighthawk R7000 — HW R7000, Firmware V1.0.12.216_10.2.122
- Cisco Switch: Catalyst 2960 — WS-C2960-24PC-S (Layer 2 switch)
- Desktop PC (DHCP)
- Laptop / server (static IP: 192.168.1.4)
- Patch cables, power equipment

Logical topology
- Internet <-> AT&T BGW320-500 (IP Passthrough) <-> NETGEAR R7000 (WAN) -> NETGEAR LAN 192.168.1.1 <-> Cisco 2960 (mgmt 192.168.1.2) -> wired clients (DHCP for PCs; server static 192.168.1.4)

IP addressing used
- NETGEAR LAN / DHCP: 192.168.1.1/24
- Cisco switch (management): 192.168.1.2/24
- AT&T gateway LAN (changed): 192.168.2.254
- Static server: 192.168.1.4
- Clients: DHCP from NETGEAR

What I did — step-by-step
1. Factory reset Cisco Catalyst 2960 via hardware reset button (no console). While isolated from other networks, connected switch Fa0/1 to a PC so the switch provided a DHCP address for its express setup and web device manager. Assigned management IP 192.168.1.2.
2. Configured NETGEAR R7000: set LAN IP 192.168.1.1, configured DHCP range, set admin password.
3. Wired connections:
   - AT&T modem Ethernet -> NETGEAR WAN (Internet) port
   - NETGEAR LAN -> Cisco switch (uplink)
   - Switch -> desktop and server
4. AT&T BGW320-500:
   - Disabled AT&T Wi‑Fi
   - Enabled IP Passthrough for the NETGEAR router
   - Changed gateway LAN IP to 192.168.2.254 to avoid subnet conflict
5. Verified connectivity:
   - Ping NETGEAR (192.168.1.1), Cisco (192.168.1.2), and an external IP (8.8.8.8).
   - Confirmed NETGEAR WAN shows the public IP (IP passthrough working).
   - Confirmed DHCP leases for wired clients.

Why this design
- IP passthrough avoids double NAT and gives the NETGEAR the public IP for easier port-forwarding and external reachability.
- Using a managed switch provides flexibility to add VLANs later without replacing hardware.
- Kept network flat now to avoid flashing third-party firmware or using console access until ready.

Security & best-practices applied / recommended
- Change default admin passwords on NETGEAR and switch.
- Disable unused services on devices (remote mgmt, telnet).
- Enable SSH for switch management (avoid telnet).
- Keep config backups in this repo (configs/).
- When ready to add VLANs, use a router/firewall that supports 802.1Q or dedicated firmware.

Repository artifacts to add
- diagrams/network-diagram.drawio and network-diagram.png
- configs/cisco-switch-config.txt (running/startup config)
- configs/netgear-config-export.txt or screenshots (WAN/DHCP)
- configs/att-gateway-settings.txt or screenshots (IP Passthrough)
- ip-plan.md
- lab-notes.md (timeline, troubleshooting)
- screenshots/ (NETGEAR WAN page, DHCP table, switch GUI)

Suggested repo structure
- README.md
- diagrams/
- configs/
- screenshots/
- ip-plan.md
- lab-notes.md
- tests/ (optional)

How to verify the NETGEAR got the public WAN IP
1. On NETGEAR status page, note the WAN IP.
2. From any device behind the NETGEAR, visit https://ifconfig.me or https://whatismyip.akamai.com — if the browser-shown IP matches the NETGEAR WAN IP, the router has the public IP.
3. If the NETGEAR WAN IP is a non-private address (not 10.x.x.x, 172.16–172.31.x.x, or 192.168.x.x), it’s a public IP.

Notes about switch config export (no console)
- Some Catalyst 2960 web GUIs include a “Backup/Export config” option. Check Administration -> Backup/Export or System.
- If not present, you’ll need SSH/console to run: enable -> show running-config -> copy/paste the output.
- If SSH is not enabled, enabling it might be possible via web UI; otherwise you’ll need a console cable.

Next actions (you can pick and I’ll do them)
- I can produce the remaining placeholder files (diagram PNG, configs templates, ip-plan.md) for you to paste into the repo.
- I can commit these files to your GitHub repo if you grant access / add me or give me permission.
- I can add a link placeholder to your server/container repo once you create it.

A couple of quick helpful tips since you don’t have a console cable yet
- To export the Cisco config later, pick up a "USB-to-RJ45 Cisco console cable" or "USB-to-Serial (FTDI) + RJ45 rollover cable". Many sellers list "Cisco console cable" (RJ45-to-USB).
- Keep an offline copy of config files in configs/ and store them in another backup location.

Which option do you want (1, 2, or 3)? If 1, I’ll generate the diagram + scaffolding now and show them here for your approval.