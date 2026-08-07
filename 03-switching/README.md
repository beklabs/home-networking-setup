# 03 — Switching

This document records the switch used in the lab (Cisco Catalyst 2960), the management IP, and the physical port map I used. Keep the running-config here (sanitized) so you can restore the known-good state.

Switch summary
- Model: Cisco Catalyst 2960 (L2)
- Management IP: 192.168.1.2
- Role: Layer‑2 switching for wired endpoints; management on VLAN 1 (for now)

Cisco 2960 — Express Setup steps (what I did)
1. Ensure no devices are connected to the switch before beginning (the switch acts as DHCP server during express setup).
2. Make sure your PC is set to DHCP so it can receive an IP from the switch during the wizard.
3. Connect power to the switch and wait for POST to complete (SYST LED will blink green).
4. Press and hold the MODE button until LEDs above it turn green, then release — the switch enters Express Setup mode.
5. Connect a Cat5e cable from Fa0/1 on the switch to your PC. Verify port LEDs are green.
6. Wait ~30s, then open a browser and go to `http://10.0.0.1` to access Express Setup.

NETWORK SETTINGS entered
- Management Interface (VLAN ID): 1
- IP Address: 192.168.1.2
- Subnet Mask: 255.255.255.0
- Default Gateway: 192.168.1.1
- Switch Password: created a secure admin password

OPTIONAL SETTINGS
- Hostname: tinfoil
- Telnet: disabled
- SNMP: disabled

Rationale
- Disabled telnet for security (use SSH when available).
- SNMP on this older switch has security concerns; left disabled until I purchase a console cable and evaluate further.

Minimal sanitized config (example)
```
! Cisco 2960 - minimal example (sanitized)
hostname tinfoil
no ip domain-lookup
interface Vlan1
 ip address 192.168.1.2 255.255.255.0
 no shutdown
!
interface FastEthernet0/1
 description Uplink-to-Router
 switchport mode trunk
!
interface FastEthernet0/2
 description Server
 switchport mode access
 switchport access vlan 1
!
ip default-gateway 192.168.1.1
username admin privilege 15 secret <redacted>
line vty 0 4
 transport input ssh
 login local
!
end
```

Notes & recovery
- If the switch becomes unreachable, connect via console and `erase startup-config` then reload to return to factory defaults.
- Save `show running-config` output to `configs/switch/` and redact secrets before committing.

VLANs
- No production VLAN segmentation is currently in use. If VLANs are added later, document VLAN IDs and port assignments here.
