# SysGuard — Windows Status (Untested)

SysGuard is currently a terminal-based macOS application. It has not been tested on Windows, and Windows is not a supported release target.

Earlier development explored a Windows auditor using PowerShell and CIM/WMI. Any preserved prototype files under the ignored `windows/` directory are separate from the active macOS scanner and may be absent from a fresh clone. The active orchestrator uses macOS collectors and macOS security rules.

Windows-related types, reporter branches, and legacy checks in `doctor` do not establish working Windows auditing. In particular, a successful Windows `doctor` result would not validate the active scanner on Windows.

The previous Windows installation instructions, feature checklist, and simulated Windows demo commands have been withdrawn because they implied verified functionality. The active CLI has no `--demo` mode.

Windows support would require integration and live testing of collectors, parsers, security rules, permissions, reporting, and terminal interactions before installation instructions or compatibility claims can be published.

For current setup and usage, see the [macOS guide](macos.md) and [CLI reference](cli-reference.md).
