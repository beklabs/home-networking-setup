# Operations & Troubleshooting

A short, practical folder I use to remind myself how to check the network, where backups live, and how to perform common recovery tasks.

Quick health checks
- From a LAN client:
  - `ping 192.168.1.1` — router
  - `ping 192.168.1.2` — switch mgmt
  - `ping 192.168.1.4` — server
  - `curl -s ifconfig.me` — check public IP (if passthrough)
- If DNS fails: try `dig @1.1.1.1 example.com` to verify external DNS

Backups & config storage
- Router/switch configs: save sanitized text into `configs/` before committing
- Export the draw.io source and PNG to `diagrams/` as the canonical topology

Change log (short)
- 2026-08-07 — Created condensed 5-chapter docs and populated wifi, core, and switch notes
- (Add subsequent entries here with date and short note)

Recovery notes
- Router not reachable: power-cycle modem → router → switch (in that order)
- Switch management lost: connect console, check VLAN and IP on VLAN interface
- If internet is down: verify modem status, check whether router WAN shows public IP (if passthrough enabled)

Where to add longer runbooks
- For any long troubleshooting scripts or playbooks, add files under `scripts/` or `chapters/` as needed and link from this file.
