# Bearsampp Module MySQL - Gradle Build Documentation

## Table of Contents

- [Overview](#overview)
- [Quick Start](#quick-start)
- [Installation](#installation)
- [Build Tasks](#build-tasks)
- [Configuration](#configuration)
- [Architecture](#architecture)
- [Troubleshooting](#troubleshooting)
- [Migration Guide](#migration-guide)

---

## Overview

The Bearsampp Module MySQL project has been converted to a **pure Gradle build system**, replacing the legacy Ant build configuration. This provides:

- **Modern Build System**     - Native Gradle tasks and conventions
- **Better Performance**       - Incremental builds and caching
- **Simplified Maintenance**   - Pure Groovy/Gradle DSL
- **Enhanced Tooling**         - IDE integration and dependency management
- **Cross-Platform Support**   - Works on Windows, Linux, and macOS

### Project Information

| Property          | Value                                    |
|-------------------|------------------------------------------|
| **Project Name**  | module-mysql                             |
| **Group**         | com.bearsampp.modules                    |
| **Type**          | MySQL Module Builder                     |
| **Build Tool**    | Gradle 7.x+                              |
| **Language**      | Groovy (Gradle DSL)                      |

---

## Quick Start

### Prerequisites

| Requirement       | Version       | Purpose                                  |
|-------------------|---------------|------------------------------------------|
| **Java**          | 8+            | Required for Gradle execution            |
| **Gradle**        | 7.0+          | Build automation tool                    |
| **7-Zip**         | Latest        | Archive compression (optional)           |

### Basic Commands

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

---

## Installation

### 1. Clone the Repository

```bash
git clone https://github.com/bearsampp/module-mysql.git
cd module-mysql
```

### 2. Verify Environment

```bash
gradle verify
```

This will check:
- Java version (8+)
- Required files (build.properties)
- Directory structure (bin/)
- Build dependencies

### 3. List Available Versions

```bash
gradle listVersions
```

### 4. Build Your First Release

```bash
# Interactive mode (prompts for version)
gradle release

# Or specify version directly
gradle release -PbundleVersion=8.0.40
```

---

## Build Tasks

### Core Build Tasks

| Task                  | Description                                      | Example                                  |
|-----------------------|--------------------------------------------------|------------------------------------------|
| `release`             | Build and package release (interactive/non-interactive) | `gradle release -PbundleVersion=8.0.40` |
| `releaseAll`          | Build all available versions                     | `gradle releaseAll`                      |
| `clean`               | Clean build artifacts and temporary files        | `gradle clean`                           |

### Verification Tasks

| Task                      | Description                                  | Example                                      |
|---------------------------|----------------------------------------------|----------------------------------------------|
| `verify`                  | Verify build environment and dependencies    | `gradle verify`                              |
| `validateProperties`      | Validate build.properties configuration      | `gradle validateProperties`                  |
| `checkModulesUntouched`   | Check modules-untouched integration          | `gradle checkModulesUntouched`               |

### Information Tasks

| Task                | Description                                      | Example                    |
|---------------------|--------------------------------------------------|----------------------------|
| `info`              | Display build configuration information          | `gradle info`              |
| `listVersions`      | List available bundle versions in bin/           | `gradle listVersions`      |
| `listReleases`      | List all available releases from modules-untouched | `gradle listReleases`    |

### Task Groups

| Group            | Purpose                                          |
|------------------|--------------------------------------------------|
| **build**        | Build and package tasks                          |
| **verification** | Verification and validation tasks                |
| **help**         | Help and information tasks                       |

---

## Configuration

### build.properties

The main configuration file for the build:

```properties
bundle.name     = mysql
bundle.release  = 2025.8.21
bundle.type     = bins
bundle.format   = 7z
```

| Property          | Description                          | Example Value  |
|-------------------|--------------------------------------|----------------|
| `bundle.name`     | Name of the bundle                   | `mysql`        |
| `bundle.release`  | Release version                      | `2025.8.21`    |
| `bundle.type`     | Type of bundle                       | `bins`         |
| `bundle.format`   | Archive format                       | `7z`           |

### gradle.properties

Gradle-specific configuration:

```properties
# Gradle daemon configuration
org.gradle.daemon=true
org.gradle.parallel=true
org.gradle.caching=true

# JVM settings
org.gradle.jvmargs=-Xmx2g -XX:MaxMetaspaceSize=512m
```

### Directory Structure

```
module-mysql/
├── .gradle-docs/          # Gradle documentation
│   └── README.md          # Main documentation
├── bin/                   # MySQL version bundles
│   ├── mysql8.0.40/
│   ├── mysql8.4.0/
│   └── archived/          # Archived versions
├── pear/                  # (Not applicable to MySQL)
bearsampp-build/           # External build directory (outside repo)
├── tmp/                   # Temporary build files
│   ├── bundles_prep/bins/mysql/      # Prepared bundles
│   ├── bundles_build/bins/mysql/     # Build staging
│   ├── downloads/mysql/              # Downloaded dependencies
│   └── extract/mysql/                # Extracted archives
└── bins/mysql/                       # Final packaged archives
    └── 2025.8.21/                    # Release version
        ├── bearsampp-mysql-8.0.40-2025.8.21.7z
        ├── bearsampp-mysql-8.0.40-2025.8.21.7z.md5
        └── ...
├── build.gradle           # Main Gradle build script
├── settings.gradle        # Gradle settings
└── build.properties       # Build configuration
```

---

## Architecture

### Build Process Flow

```
1. User runs: gradle release -PbundleVersion=8.0.40
                    ↓
2. Validate environment and version
                    ↓
3. Check if MySQL binaries exist in bin/mysql8.0.40/
                    ↓
4. If not found, download from modules-untouched
   - Fetch mysql.properties from modules-untouched
   - Download MySQL archive
   - Extract to bearsampp-build/tmp/extract/
                    ↓
5. Create preparation directory (tmp/prep/)
                    ↓
6. Copy MySQL binaries from source
                    ↓
7. Overlay bundle files (bearsampp.conf, my.ini, etc.)
                    ↓
8. Output prepared bundle to tmp/prep/
                    ↓
9. Copy to bundles_build directory
                    ↓
10. Package prepared folder into archive in bearsampp-build/bins/mysql/{bundle.release}/
   - The archive includes the top-level folder: mysql{version}/
```

### Packaging Details

- **Archive name format**: `bearsampp-mysql-{version}-{bundle.release}.{7z|zip}`
- **Location**: `bearsampp-build/bins/mysql/{bundle.release}/`
  - Example: `bearsampp-build/bins/mysql/2025.8.21/bearsampp-mysql-8.0.40-2025.8.21.7z`
- **Content root**: The top-level folder inside the archive is `mysql{version}/` (e.g., `mysql8.0.40/`)
- **Structure**: The archive contains the MySQL version folder at the root with all MySQL files inside

**Archive Structure Example**:
```
bearsampp-mysql-8.0.40-2025.8.21.7z
└── mysql8.0.40/              ← Version folder at root
    ├── bin/
    │   ├── mysqld.exe
    │   ├── mysql.exe
    │   └── ...
    ├── lib/
    ├── include/
    ├── share/
    ├── bearsampp.conf
    └── my.ini
```

**Verification Commands**:

```bash
# List archive contents with 7z
7z l bearsampp-build/bins/mysql/2025.8.21/bearsampp-mysql-8.0.40-2025.8.21.7z | more

# You should see entries beginning with:
#   mysql8.0.40/bin/mysqld.exe
#   mysql8.0.40/bin/mysql.exe
#   mysql8.0.40/lib/...
#   mysql8.0.40/...

# Extract and inspect with PowerShell (zip example)
Expand-Archive -Path bearsampp-build/bins/mysql/2025.8.21/bearsampp-mysql-8.0.40-2025.8.21.zip -DestinationPath .\_inspect
Get-ChildItem .\_inspect\mysql8.0.40 | Select-Object Name

# Expected output:
#   bin/
#   lib/
#   include/
#   share/
#   bearsampp.conf
#   my.ini
#   ...
```

**Note**: This archive structure matches the PHP module pattern where archives contain `php{version}/` at the root. This ensures consistency across all Bearsampp modules.

**Hash Files**: Each archive is accompanied by hash sidecar files:
- `.md5` - MD5 checksum
- `.sha1` - SHA-1 checksum
- `.sha256` - SHA-256 checksum
- `.sha512` - SHA-512 checksum

Example:
```
bearsampp-build/bins/mysql/2025.8.21/
├── bearsampp-mysql-8.0.40-2025.8.21.7z
├── bearsampp-mysql-8.0.40-2025.8.21.7z.md5
├── bearsampp-mysql-8.0.40-2025.8.21.7z.sha1
├── bearsampp-mysql-8.0.40-2025.8.21.7z.sha256
└── bearsampp-mysql-8.0.40-2025.8.21.7z.sha512
```

### Modules-Untouched Integration

The build system integrates with the [modules-untouched](https://github.com/Bearsampp/modules-untouched) repository to:

1. Fetch available MySQL versions from `mysql.properties`
2. Resolve download URLs for specific versions
3. Provide fallback to standard URL format if remote fetch fails

**Commands**:
```bash
gradle listReleases              # List remote versions
gradle checkModulesUntouched     # Check integration status
```

---

## Troubleshooting

### Common Issues

#### Issue: "Dev path not found"

**Symptom:**
```
Dev path not found: E:/Bearsampp-development/dev
```

**Solution:**
This is a warning only. The dev path is optional for most tasks. If you need it, ensure the `dev` project exists in the parent directory.

---

#### Issue: "Bundle version not found"

**Symptom:**
```
Bundle version not found: E:/Bearsampp-development/module-mysql/bin/mysql8.0.99
```

**Solution:**
1. List available versions: `gradle listVersions`
2. Use an existing version: `gradle release -PbundleVersion=8.0.40`

---

#### Issue: "7-Zip not found"

**Symptom:**
```
7-Zip not found! Please install 7-Zip or set 7Z_HOME environment variable.
```

**Solution:**
1. Install 7-Zip from https://www.7-zip.org/
2. Set the `7Z_HOME` environment variable
3. Or ensure 7z.exe is in your system PATH

---

#### Issue: "Java version too old"

**Symptom:**
```
Java 8+ required
```

**Solution:**
1. Check Java version: `java -version`
2. Install Java 8 or higher
3. Update JAVA_HOME environment variable

---

### Debug Mode

Run Gradle with debug output:

```bash
gradle release -PbundleVersion=8.0.40 --info
gradle release -PbundleVersion=8.0.40 --debug
```

### Clean Build

If you encounter issues, try a clean build:

```bash
gradle clean
gradle release -PbundleVersion=8.0.40
```

---

## Migration Guide

### From Ant to Gradle

The project has been fully migrated from Ant to Gradle. Here's what changed:

#### Removed Files

| File              | Status    | Replacement                |
|-------------------|-----------|----------------------------|
| `build.xml`       | ✗ Removed | `build.gradle`             |

#### Command Mapping

| Ant Command                          | Gradle Command                              |
|--------------------------------------|---------------------------------------------|
| `ant release`                        | `gradle release`                            |
| `ant release -Dinput.bundle=8.0.40`  | `gradle release -PbundleVersion=8.0.40`     |
| `ant clean`                          | `gradle clean`                              |

#### Key Differences

| Aspect              | Ant                          | Gradle                           |
|---------------------|------------------------------|----------------------------------|
| **Build File**      | XML (build.xml)              | Groovy DSL (build.gradle)        |
| **Task Definition** | `<target name="...">`        | `tasks.register('...')`          |
| **Properties**      | `<property name="..." />`    | `ext { ... }`                    |
| **Dependencies**    | Manual downloads             | Automatic with repositories      |
| **Caching**         | None                         | Built-in incremental builds      |
| **IDE Support**     | Limited                      | Excellent (IntelliJ, Eclipse)    |

---

## Additional Resources

- [Gradle Documentation](https://docs.gradle.org/)
- [Bearsampp Project](https://github.com/bearsampp/bearsampp)
- [MySQL Downloads](https://dev.mysql.com/downloads/mysql/)
- [Modules-Untouched Repository](https://github.com/Bearsampp/modules-untouched)

---

## Support

For issues and questions:

- **GitHub Issues**: https://github.com/bearsampp/module-mysql/issues
- **Bearsampp Issues**: https://github.com/bearsampp/bearsampp/issues
- **Documentation**: https://bearsampp.com/module/mysql

---

**Last Updated**: 2025-01-31  
**Version**: 2025.8.21  
**Build System**: Pure Gradle (no wrapper, no Ant)

Notes:
- This project deliberately does not ship the Gradle Wrapper. Install Gradle 7+ locally and run with `gradle ...`.
- Legacy Ant files (e.g., Eclipse `.launch` referencing `build.xml`) are deprecated and not used by the build.
