# macOS Audit Guide

SysGuard runs in the macOS terminal. See the [README](../README.md) for installation and the [CLI reference](cli-reference.md) for commands and keyboard controls.

## Security checks

The active rule engine evaluates these nine checks using native macOS data:

| Check | Data source | Severity |
|---|---|---|
| System Integrity Protection | `csrutil status` | CRITICAL |
| FileVault encryption | `fdesetup status` | HIGH |
| Gatekeeper assessment | `spctl --status` | HIGH |
| Application Firewall | `socketfilterfw --getglobalstate` | HIGH |
| Firewall stealth mode | `socketfilterfw --getstealthmode` | MEDIUM |
| Administrator accounts | `dscl . -read /Groups/admin GroupMembership` | MEDIUM |
| Listening services | `lsof -nP -iTCP -sTCP:LISTEN` | HIGH |
| Automatic update checking | `defaults read /Library/Preferences/com.apple.SoftwareUpdate AutomaticCheckEnabled` | MEDIUM |
| Launch persistence inventory | `.plist` filenames in LaunchDaemon/LaunchAgent directories | MEDIUM |

`socketfilterfw` is invoked using its full path, `/usr/libexec/ApplicationFirewall/socketfilterfw`. The collector also reads its per-application rules with `--listapps`.

The administrator rule warns when there are more than two non-root administrators. The listening-service rule warns about ports 21, 23, 80, 5900, and 22 bound to `0.0.0.0` or `*`; it does not determine whether the services are reachable from outside the host. The update check reads the automatic-checking preference rather than verifying installed patches. The persistence rule catalogs startup items and returns an informational PASS; it does not detect malware.

Review findings and remediation advice in the dashboard or exported JSON. SysGuard does not execute the suggested fixes.

## System inventory

The system collector reads OS version, CPU, memory, boot time, computer name, and local accounts using `sw_vers`, `sysctl`, `scutil`, and `dscl`. It also collects:

- Startup `.plist` filenames from `/Library/LaunchDaemons`, `/Library/LaunchAgents`, and `~/Library/LaunchAgents`.
- Metadata for up to 40 entries per folder in Downloads, Documents, and Desktop.
- Process environment variable names and values.
- PATH directories and their world-writable permission bits.

Folder metadata and writable-bit checks are limited inventories, not comprehensive file or permission audits. Environment values are exported without automatic credential redaction.

## Network collection

| Command | Data collected |
|---|---|
| `lsof -nP -iTCP -sTCP:LISTEN` | TCP listening sockets and associated processes |
| `lsof -nP -iTCP -sTCP:ESTABLISHED` | Established TCP connections |
| `lsof -nP -iUDP` | UDP endpoints |
| `ifconfig` | Interfaces and addresses |
| `arp -an` | Cached IPv4 neighbors |
| `scutil --dns` | DNS configuration |
| `sharing -l` | Local sharing configuration |

Topology output includes counts and Mermaid diagram text in JSON. The diagram groups IPv4 addresses using an assumed `/24` subnet rather than the actual interface mask, and shows a limited selection of shares, ports, and cached neighbors. It is an approximate view of collected local data, not a complete network discovery scan or verified VLAN map.

## Permissions and interpretation

Start as a standard user and run `sysguard doctor` to check selected native utilities. System-owned sockets and protected files may be unavailable depending on macOS permissions and privacy controls. Elevated privileges may expose more data but do not guarantee complete collection.

Missing commands, unreadable directories, and failed queries can produce empty or partial data. Some rules default missing data to empty lists; a PASS or high posture score therefore does not prove that every data source was successfully inspected. Review diagnostic warnings and evidence.

The code targets native macOS utilities on Apple Silicon and Intel; a complete macOS version/architecture compatibility matrix has not been established. Windows remains untested, and Linux auditing is unsupported.
