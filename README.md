[README_Version8.md](https://github.com/user-attachments/files/30836690/README_Version8.md)
# Home Networking Setup

This repository documents the home networking lab I built and configured. It doubles as a portfolio entry showing practical network setup, device configuration, troubleshooting and security considerations.

Diagram
-------
- Diagram file: `home network.drawio` (included in this repo). Open it with draw.io / diagrams.net to view the topology visually.

Project summary
---------------
A small home network built using an ISP-supplied modem (AT&T), a NETGEAR router, and a Cisco Catalyst 2960 Layer‑2 switch. The router is the primary gateway and DHCP server for the LAN; the ISP modem is configured to pass the public IP (IP passthrough) while remaining on an isolated management IP to avoid address conflicts.

Goals
- Replace ISP-provided Wi‑Fi with my NETGEAR AP/router for better control
- Put wired devices (desktop, server) on a managed L2 switch
- Eliminate double NAT by using IP passthrough on the ISP modem
- Document the process and capture configuration choices and lessons learned

Hardware inventory
------------------
- ISP modem: AT&T residential modem
  - Changed its LAN address to: `192.168.2.254` (so it does not conflict with router LAN)
  - Role: modem + management (IP passthrough enabled)
- Router: NETGEAR (model: user-owned)
  - LAN/Default gateway: `192.168.1.1`
  - Role: DHCP server, wireless (disabled on AT&T modem), primary gateway for the LAN
- Switch: Cisco Catalyst 2960 (L2)
  - Management IP assigned during setup: `192.168.1.2`
  - Role: Layer 2 switching for wired hosts
- Desktop PC (wired)
- Laptop / home server (wired)
- Wireless devices (phones, laptops) connecting to NETGEAR SSID

IP addressing and roles
-----------------------
- NETGEAR router LAN/gateway: 192.168.1.1 (DHCP server)
- Cisco switch management: 192.168.1.2
- Desktop and server: usually DHCP-assigned by router (or static where noted)
- AT&T modem LAN: 192.168.2.254 (changed to avoid conflict with the NETGEAR router subnet of `192.168.1.0/24`)

High-level topology
-------------------
Internet -> ISP modem (AT&T) -> NETGEAR WAN (internet port) -> NETGEAR LAN -> Cisco switch -> wired hosts

Key steps performed (what I did)
--------------------------------
1. Factory reset the Cisco Catalyst 2960 switch to clear any previous config.
2. Put the switch into an initial setup mode and connected with an Ethernet cable to my PC for management.
3. Assigned the switch a management IP: `192.168.1.2`.
4. Configured the NETGEAR router with LAN IP `192.168.1.1` and enabled DHCP for the desired LAN range.
5. Connected the AT&T modem's Ethernet port to the NETGEAR router WAN (internet) port.
6. Connected one LAN port of the NETGEAR router to the Cisco switch uplink (switch port).
7. From the switch, connected wired devices:
   - Desktop PC -> switch port
   - Laptop / server -> switch port
8. Disabled Wi‑Fi on the AT&T modem so only the NETGEAR SSID is available to home devices.
9. Enabled IP Passthrough (AT&T modem) in the modem's web UI so the external/public IP is assigned/forwarded to the router.
10. Changed the AT&T modem LAN IP to `192.168.2.254` so it does not conflict with the NETGEAR router subnet of `192.168.1.0/24`.

Why I changed the AT&T modem IP to 192.168.2.254
- The modem's default LAN was in the same subnet as the router (192.168.1.0/24). To avoid address conflicts and routing issues when enabling IP passthrough or when accessing the modem management UI from the LAN, I changed the modem to a different subnet (192.168.2.0/24). This prevents the modem and router from competing for the same LAN addresses.

Why enable IP passthrough
- Prevents double NAT. With IP passthrough the router receives the public IP (or a public-routable IP) from the modem and handles NAT/firewall duties for the LAN. This avoids having the modem and router both doing NAT which can complicate port forwarding, VPNs, and some applications.

Notes about the Cisco Catalyst 2960
- The 2960 is an L2 switch. In this setup I assigned it a management IP so I can SSH or Telnet to the switch for monitoring and occasional configuration. I did not implement L3 routing on the switch—routing is handled by the router.

Security & housekeeping recommendations
--------------------------------------
- Change default admin passwords on the AT&T modem, NETGEAR router, and Cisco switch.
- Update firmware / IOS on devices if newer stable versions are available.
- Disable remote administration (remote web/SSH) on the router unless needed; if needed, lock it to specific IPs and use key-based auth where possible.
- Use WPA2/WPA3 with a strong passphrase on the NETGEAR Wi‑Fi.
- Consider setting static DHCP reservations for servers and important devices (desktop, server) so they keep the same IP.
- Consider separate VLANs for IoT / guest networks.

Troubleshooting notes (what to check if something breaks)
--------------------------------------------------------
- If a device cannot access the internet:
  - Check the device's IP configuration (ipconfig/ifconfig): correct gateway (192.168.1.1) and DNS?
  - Ping the router (192.168.1.1) then ping an external IP (8.8.8.8). If ping external by IP works but not by name, it's a DNS issue.
  - Check the router WAN status—does it have the public IP from the modem?
- If the router cannot get a WAN IP:
  - Verify IP passthrough is enabled on the AT&T modem and that there is no modem-level firewall preventing passthrough.
  - Check for MAC binding or device restrictions on the modem.
- If you have IP conflicts: verify that only one device is assigning DHCP for the LAN. Keep the AT&T modem on a different subnet (e.g. 192.168.2.254) to avoid accidental overlap.

Follow-ups and improvements to consider
--------------------------------------
- Move static/important devices (NAS, server) into a documented DHCP reservation block or give static IPs and record them in this README.
- Put monitoring in place (e.g., Prometheus + node_exporter on server, or a simple Nagios/PRTG/Datadog check) for uptime/latency.
- Implement VLANs on the switch and router for network segregation (e.g., IoT, Guest, LAN).
- Configure SNMP on the switch and add polling to a network monitor.
- Regularly backup the router and switch configs to the repo (sanitizing passwords and secrets).

Questions a reviewer or interviewer might ask (and suggested answers you should be ready to give)
---------------------------------------------------------------------------------------------
Q: Why did you choose 192.168.1.1 and 192.168.1.2 for router and switch?
A: I chose a common private range 192.168.1.0/24 for the LAN. Router at .1 is conventional; the switch management interface at .2 keeps it accessible for management but outside DHCP assignments (or you should create a DHCP reservation/exclude that IP from the DHCP pool).

Q: Why change the AT&T modem IP to 192.168.2.254?
A: To avoid conflicts with the router’s LAN (192.168.1.0/24) and to keep the modem reachable on a separate management subnet after enabling IP passthrough.

Q: What is IP passthrough and why did you enable it?
A: IP passthrough forwards the external/public IP address (or otherwise provides an external routable address) from the modem to a single downstream device (the router). This avoids having both the modem and the router perform NAT (double NAT), which can complicate inbound port forwarding and some VPN setups.

Q: Is the Cisco switch routing or just switching?
A: It's a layer‑2 switch. All IP routing is handled by the NETGEAR router. The Cisco device has a management IP for console/SSH access and monitoring.

Q: How did you secure the network?
A: At minimum I disabled Wi‑Fi on the ISP modem, used the NETGEAR Wi‑Fi with WPA2/WPA3 and a strong passphrase, changed default admin passwords and disabled remote administration on the router. Next steps include firmware updates, SNMP hardening, VLAN separation, and periodic config backups.

Q: How would you handle guests or IoT devices?
A: Create VLANs on the router and switch (or use the router's guest network feature) to isolate IoT/guest traffic from the main LAN and limit access to internal resources.

Q: What would you document next?
A: Device models/firmware versions, exact commands or screenshots used for the Cisco switch initial setup, a copy of the router configuration (sanitized) and the draw.io diagram exported to PNG/SVG for easy viewing.
- Add a sanitized copy of the Cisco switch startup-config and the Netgear router config to the repo (you must provide the config or allow me to fetch it).
- Export the draw.io diagram to a PNG and add it to the README for quick viewing.
- Add a troubleshooting playbook with exact commands (ping/traceroute/nslookup/arp/ssh steps) and sample outputs.

If you want me to commit the README as-is, or add/adjust anything in the README (tone, detail level, include exact commands or screenshots), tell me now and I'll update the file accordingly.
