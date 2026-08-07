# 02 — Core Network

This file captures the core WAN/LAN decisions I made: modem passthrough, router LAN, DHCP ranges and basic hardening notes.

WAN / Modem
- ISP modem: AT&T BGW320-500 residential gateway
- Management IP changed to 192.168.2.254 to avoid conflict with the router's LAN
- IP Passthrough enabled so the NETGEAR router receives the public IP (avoids double NAT)

AT&T BGW320-500 — IP Passthrough steps (what I did)
1. In a browser navigate to: `http://192.168.1.254`
2. Login with the modem access code.
3. Go to `Firewall` → `IP Passthrough`.
4. Allocation Mode: `Passthrough`.
5. Passthrough Mode: `DHCPS-fixed`.
6. Passthrough Fixed MAC Address: select the MAC address of the NETGEAR router from the list (or enter it).
7. Save and apply changes.

Change modem LAN management IP to avoid overlap
1. Go to `Home Network` → `Subnets & DHCP`.
2. Under the Device IPv4 Address field, set: `192.168.2.254`.
3. Save and reboot the modem so changes apply.

Disable AT&T Wi‑Fi radios (2.4GHz & 5GHz)
1. Go to `Home Network` → `Wi-Fi`.
2. Under `Home SSID Enable` turn it OFF.
3. Under `Guest SSID Enable` turn it OFF.
4. Save settings.

Router (NETGEAR Nighthawk R7000)
- LAN / Gateway: 192.168.1.1/24
- DHCP pool: 192.168.1.100–192.168.1.199
- DNS: set to automatic from ISP initially, or use 1.1.1.1 / 8.8.8.8

NETGEAR R7000 — key steps I performed
- Connected modem Ethernet -> Router WAN (yellow port) and powered the router.
- Connected a LAN port to my PC and logged into `http://www.routerlogin.net`.
- Changed admin password at `Advanced` → `Administration` → `Set Password` and enabled password recovery.
- Set Internet IP to `Get Dynamically from ISP`.
- Left Router MAC as `Use Default Address`.
- Enabled wireless (2.4GHz & 5GHz) with WPA2-PSK[AES] and unique SSIDs.
- Enabled guest networks for both bands with WPA2-PSK[AES] and separate SSIDs.

Basic hardening notes (what I did)
- Changed admin passwords from defaults.
- Disabled remote/WAN-side admin access.
- Disabled AT&T Wi‑Fi so NETGEAR SSIDs are the only Wi‑Fi in use.
- Firmware versions: record here after you update.

Quick verification
- From a client:
  - `ping 192.168.1.1` (router)
  - Check router WAN page for public IP (if passthrough worked)
  - If router WAN is still private, re-check IP Passthrough settings on the AT&T modem

Notes
- IP Passthrough is AT&T's approach for avoiding double NAT; it forwards the external IP to your chosen downstream device (router) while keeping modem management accessible on its separate management IP.
