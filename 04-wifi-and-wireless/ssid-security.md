# SSID Security & Settings

This file is a short record of the SSID choices and security settings I actually used in my home lab.

Current settings (as of this commit)
- MyHome
  - Security: WPA2/WPA3 mixed-mode (router supports it)
  - Password: stored in my password manager (not in this repo)
- MyHome-Guest
  - Guest isolation: enabled (internet-only)
  - No LAN access for guest clients

Other security notes
- WPS: disabled on ISP modem and on NETGEAR
- Remote admin: disabled on WAN side for all devices
- Firmware: check router/modem firmware versions and update periodically; add versions here when you update

If you change a password or SSID, update this file to record the date (do not store the actual passphrase in the repo).
