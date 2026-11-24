# SysGuard

**Terminal-based security and network auditing application for macOS**

SysGuard is a read-only CLI application with an interactive Terminal User Interface (TUI). It uses built-in macOS commands to inspect system information, security settings, and local network exposure without installing a persistent agent or background service.

**Platform status:** macOS is the current target platform. Windows has not been tested and is not a supported release target. Linux auditing is not supported.

## Features

- Interactive audit checklist and keyboard-driven dashboard inside your terminal.
- macOS security checks for SIP, FileVault, Gatekeeper, Application Firewall, stealth mode, administrator accounts, automatic update settings, listening services, and launch persistence.
- System information, local accounts, startup items, user-folder file metadata, process environment variables, and PATH directory permissions.
- Network interfaces, TCP listening and established connections, UDP endpoints, ARP neighbors, DNS configuration, and sharing information.
- Network topology data, security findings, remediation guidance, and a posture score.
- Static terminal tables and JSON report export.

## Requirements and installation

- macOS with Node.js 18 or later and npm.
- A terminal with keyboard input for the interactive interface.
- Access to native macOS utilities; run `doctor` to inspect availability.

The code uses native macOS utilities on Apple Silicon and Intel; a complete macOS version/architecture compatibility matrix has not been established.

```bash
git clone https://github.com/MAYANKSHARMA01010/SysGuard.git
cd SysGuard
npm install
npm run build
node dist/cli/index.js doctor
```

Optionally link the CLI to make both `sysguard` and its `guard` alias available:

```bash
npm link
sysguard --help
```

Without linking, replace `sysguard` in the examples with `node dist/cli/index.js`, or use `npm run dev -- <command>` during development.

## Usage

```bash
# Launch the interactive checklist in a terminal
sysguard
sysguard scan

# Print static terminal tables
sysguard scan --no-interactive

# Write a JSON report
sysguard scan --format json --output reports/scan.json

# Run a fresh audit and save reports/sysguard-report.json
sysguard report
sysguard report --output reports --name audit-report

# Inspect individual modules or findings
sysguard system
sysguard security
sysguard network
sysguard findings

# Check the environment
sysguard doctor
```

Use an output file for machine processing: scan logging currently accompanies JSON printed to stdout. Report export writes files but does not change the audited security settings.

Start as a standard user. Some system-owned sockets or protected data may be unavailable depending on permissions. Review unavailable checks before interpreting the results.

## Interactive controls

In the checklist, use ↑/↓ to move, Space to toggle, A to select all, D to deselect all, and Enter to run. Selection enables the corresponding modules; it does not isolate every individual command within a module. System information is always collected.

In the dashboard, use Tab or 1–3 to switch views, ↑/↓ to select a finding, Enter to inspect it, F to cycle filters, R to rescan, E to export JSON, and B to return to the checklist. Q or Ctrl+C exits.

## Development

```bash
npm run build
npm test
```

Local tests use Vitest. The `tests/` directory is currently ignored by Git, so it may be absent from a fresh clone. Unit tests are not evidence of Windows support or a complete macOS compatibility matrix.

## Architecture

```text
src/
  cli/         Commands, interactive checklist/dashboard, terminal formatting
  core/        Scanner, orchestrator, native command runner, permissions, topology
  collectors/  macOS system, security, and network collection
  parsers/     Native command output to structured data
  rules/       Security rules and posture scoring
  reporters/   Terminal tables and JSON export
  types/       TypeScript types and Zod schemas
  utils/       Logging, dates, formatting, and errors
```

## Data and result limits

Reports include local system details, usernames, file metadata, network information, and process environment variable values. Environment values are not automatically redacted.

The posture score starts at 100 and deducts points for FAIL findings by severity, bounded to 0–100. Higher scores mean fewer scored failures. Missing data and WARN findings do not reduce the score, so review individual findings and collection warnings alongside it.

The scanner observes settings and provides remediation advice; it does not apply fixes. It exports JSON reports and topology diagram text. HTML/PDF export, CVE feed crawling, and Windows demo mode are unavailable.

## Documentation

- [CLI reference](docs/cli-reference.md): commands, options, keyboard controls, and exit behavior.
- [macOS audit guide](docs/macos.md): native data sources, rule coverage, and collection limits.

## License

MIT — Copyright (c) 2026 Mayank Sharma
