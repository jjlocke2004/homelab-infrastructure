# Router configuration (OpenWRT 25.12)

Sanitized exports from the lab's OpenWRT router (Lenovo Yoga 3 Pro, booted from USB).

| File | Contents |
|------|----------|
| `network.uci` | VLAN sub-interfaces on the trunk and per-VLAN addressing |
| `firewall.uci` | Firewall zones and inter-VLAN rules |
| `dhcp.uci` | DHCP pools and DNS settings per VLAN |
| `system.uci` | Hostname and timezone |
| `installed-packages.txt` | Installed packages |
| `system-board.json` | Hardware and OpenWRT release info |

Exported with `uci export <config>`. To restore on a fresh install, copy a file to the router and run `uci import <config> < file.uci && uci commit && reload_config`.

No secrets are included. See [docs/router-openwrt.md](../docs/router-openwrt.md) for the design and walkthrough.
