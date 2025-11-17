<p align="center"><a href="https://bearsampp.com/contribute" target="_blank"><img width="250" src="img/Bearsampp-logo.svg"></a></p>

[![GitHub release](https://img.shields.io/github/release/bearsampp/module-mysql.svg?style=flat-square)](https://github.com/bearsampp/module-mysql/releases/latest)
![Total downloads](https://img.shields.io/github/downloads/bearsampp/module-mysql/total.svg?style=flat-square)

This is a module of [Bearsampp project](https://github.com/bearsampp/bearsampp) involving MySQL.

## Build System

This project uses **Gradle** as its build system. The legacy Ant build has been fully replaced with a modern, pure Gradle implementation.

### Quick Start

```bash
# Display build information
gradle info

# List all available tasks
gradle tasks

# Verify build environment
gradle verify

# Build a release (interactive)
gradle release

# Build a specific version (non-interactive)
gradle release -PbundleVersion=8.0.40

# Clean build artifacts
gradle clean
```

### Prerequisites

| Requirement       | Version       | Purpose                                  |
|-------------------|---------------|------------------------------------------|
| **Java**          | 8+            | Required for Gradle execution            |
| **Gradle**        | 7.0+          | Build automation tool                    |
| **7-Zip**         | Latest        | Archive compression (optional)           |

### Available Tasks

| Task                  | Description                                      |
|-----------------------|--------------------------------------------------|
| `release`             | Build release package (interactive/non-interactive) |
| `releaseAll`          | Build all available versions                     |
| `clean`               | Clean build artifacts and temporary files        |
| `verify`              | Verify build environment and dependencies        |
| `info`                | Display build configuration information          |
| `listVersions`        | List available bundle versions in bin/           |
| `listReleases`        | List releases from modules-untouched             |
| `validateProperties`  | Validate build.properties configuration          |

For complete documentation, see [.gradle-docs/README.md](.gradle-docs/README.md)

## Documentation

- **Build Documentation**: [.gradle-docs/README.md](.gradle-docs/README.md)
- **Module Downloads**: https://bearsampp.com/module/mysql

## Issues

Issues must be reported on [bearsampp repository](https://github.com/bearsampp/bearsampp/issues).

## License

This project is licensed under the terms specified in the LICENSE file.
