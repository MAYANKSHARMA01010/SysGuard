# SysGuard CLI Reference

SysGuard is a terminal-based macOS application. Windows is untested and is not a supported release target; Linux auditing is not supported.

## Syntax

```bash
sysguard [options] [command]
```

`guard` is an alias when the package is linked. Without linking, use `node dist/cli/index.js` or `npm run dev --` in place of `sysguard`.

Running `sysguard` without a command opens the interactive checklist when stdout is a terminal. Otherwise it prints command guidance. Use `sysguard --help` for the registered command list.

Global options: `-V` / `--version`, `-h` / `--help`, and `--verbose` for debug output. Place the global verbose option before the command: `sysguard --verbose scan --no-interactive`.

## `scan`

Runs a macOS security and network audit.

```bash
sysguard scan [options]
```

| Option | Behavior | Default |
|---|---|---|
| `--format <format>` | `table` or `json` | `table` |
| `--output <file>` | Write JSON to a file; create parent directories | No file |
| `--modules <list>` | Comma-separated `system,security,network` | All three |
| `--no-interactive` | Print static terminal tables | Interactive in a TTY |
| `--verbose` | Accepted scan option; prefer the global option above for debug logging | Off |

With table format, no output file, and a terminal session, `scan` opens the checklist. To use `--modules` directly, pass `--no-interactive`. System information is always collected, even if omitted from `--modules`.

```bash
sysguard scan --no-interactive --modules system,network
sysguard scan --format json --output reports/scan.json
```

Specifying `--output` writes JSON even with the default table format. JSON printed to stdout is accompanied by scan logging, so use a file for machine processing.

The active CLI does not provide `--demo`, `--no-crawl`, HTML export, or PDF export.

## `report`

Runs a fresh audit of all modules and exports one JSON file.

```bash
sysguard report --output reports --name macos-audit
```

| Option | Description | Default |
|---|---|---|
| `--output <dir>` | Destination directory, created as needed | `reports` |
| `--name <name>` | Base filename, without extension | `sysguard-report` |

The example writes `reports/macos-audit.json`. No HTML or PDF bundle is generated.

## Inspection commands

| Command | Output |
|---|---|
| `sysguard system` | System information in terminal tables |
| `sysguard security` | macOS security controls and findings in terminal tables |
| `sysguard network` | Interfaces, sockets, neighbors, and related network data in terminal tables |
| `sysguard findings` | Findings from a fresh full scan, excluding NOT_APPLICABLE results |
| `sysguard findings --format json` | Findings array printed to stdout, accompanied by scan logging |

System information is also collected when running security or network commands.

## `doctor`

```bash
sysguard doctor
```

Checks the Node.js version, platform, permissions, and selected native utilities. Start with a standard user account; permissions may limit available data.

Individual failed checks print warnings but currently do not set a nonzero exit code. Legacy Windows diagnostics are not evidence of tested Windows support.

## Interactive controls

Checklist: ↑/↓ moves, Space toggles, A selects all, D deselects all, Enter runs, and Q / Ctrl+C exits. Checklist choices enable the associated modules.

Dashboard: Tab, ←/→, or 1–3 switches views; ↑/↓ selects findings; Enter shows details; F cycles filters; R rescans; E exports `reports/sysguard-report.json`; B returns to the checklist when available; Q / Ctrl+C exits.

## Exit behavior and configuration

Normal completion returns 0. Unhandled CLI errors and explicit scan/report failures return 1. Findings with FAIL status do not themselves cause a nonzero exit code. Native utility failures can instead result in partial data; exit status alone does not establish a complete audit.

There are no implemented `SYSGUARD_OFFLINE` or `SYSGUARD_LOG_LEVEL` environment settings. Use the global `--verbose` flag for debug output.
