# SysGuard Documentation

SysGuard is a terminal-based macOS security and network auditing application. Its interactive TUI runs inside a terminal; reports can also be printed as static tables or exported as JSON.

macOS is the current target platform. **Windows has not been tested and is not supported by the current release.** Linux auditing is not supported. See [Windows status](windows.md) for the distinction between earlier prototypes and the active scanner.

## Start here

Follow the [README installation steps](../README.md#requirements-and-installation), then run:

```bash
sysguard doctor
sysguard
```

For static output or a saved report:

```bash
sysguard scan --no-interactive
sysguard report --output reports --name macos-audit
```

The second command runs a fresh audit and writes `reports/macos-audit.json`.

## Guides

- [CLI reference](cli-reference.md): current commands, flags, export behavior, and exit behavior.
- [macOS auditing](macos.md): native data sources, security checks, and permission limits.
- [Windows status](windows.md): untested platform and historical prototype context.
- [Development transcript](CHAT_HISTORY.md): historical conversations containing superseded claims.
- [Commit guide](COMMIT_GUIDE.md): historical local commit workflow.

## Current architecture

```text
src/
  cli/         Commander commands, interactive checklist/dashboard, terminal UI
  core/        Scanner, orchestrator, native command runner, permissions, topology
  collectors/  macOS system, security, and network collection
  parsers/     Native command output to structured data
  rules/       macOS security rules and posture scoring
  reporters/   Terminal tables and JSON serialization
  types/       TypeScript types and Zod schemas
  utils/       Logging, dates, formatting, and errors
```

The orchestrator collects system information on every scan, optionally collects security and network modules, evaluates macOS rules, and calculates the score. The checklist enables modules rather than running only the individual commands highlighted in the menu.

The posture score starts at 100 and deducts points for FAIL findings by severity, bounded to 0–100. Higher scores indicate fewer scored failures; the score is a project-specific indicator rather than a security certification.

## Scope and limitations

The active application uses local macOS utilities. It has no active CVE feed crawler, Windows simulation mode, HTML reporter, or PDF reporter. Network topology is structured report data, including Mermaid diagram text; the application does not provide an embedded browser graph viewer.

Security collection observes settings; remediation advice is not applied automatically. Exporting creates report files. Reports include collected local system data, including process environment variable values; automatic credential redaction is not implemented for that collection.

Missing commands and permission restrictions can produce incomplete telemetry. `doctor` prints diagnostic results but currently does not set a failure exit code when an individual check fails. Its legacy Windows messages do not validate Windows compatibility.

For local development, use `npm run dev -- <command>`, `npm run build`, and `npm test`. Tests are currently Git-ignored and may be absent from a fresh checkout. Fixture tests do not validate live platform compatibility.
