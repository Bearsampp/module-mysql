<p align="center"><a href="https://bearsampp.com/contribute" target="_blank"><img width="250" src="img/Bearsampp-logo.svg"></a></p>

[![GitHub release](https://img.shields.io/github/release/bearsampp/module-mysql.svg?style=flat-square)](https://github.com/bearsampp/module-mysql/releases/latest)
![Total downloads](https://img.shields.io/github/downloads/bearsampp/module-mysql/total.svg?style=flat-square)

This is a module of [bearsampp project](https://github.com/bearsampp/bearsampp) involving MySQL.

## Documentation and downloads

https://bearsampp.com/module/mysql

## Building from Source

This module uses Gradle for building release packages. The build system provides modern features including caching, incremental builds, and integration with the modules-untouched repository.

### Prerequisites

- **Java 8+** - Required for running Gradle
- **Gradle 7.0+** - Build automation tool (wrapper included)
- **7-Zip** (optional) - Required only if using 7z format for archives

### Quick Start

```bash
# Display build information
gradle info

# List all available tasks
gradle tasks

# Verify build environment
gradle verify

# List available versions in bin/ directory
gradle listVersions

# List available releases from modules-untouched
gradle listReleases

# Build release (interactive mode - prompts for version)
gradle release

# Build release for specific version (non-interactive)
gradle release -PbundleVersion=8.0.40

# Build all available versions
gradle releaseAll

# Clean build artifacts
gradle clean
```

### Build Configuration

The build is configured through `build.properties`:

```properties
bundle.name = mysql
bundle.release = 2025.8.21
bundle.type = bins
bundle.format = 7z
```

### Build Tasks

#### Core Build Tasks

- **`gradle release`** - Build release package (interactive mode - prompts for version selection)
- **`gradle release -PbundleVersion=X.X.X`** - Build release package for a specific MySQL version (non-interactive)
- **`gradle releaseAll`** - Build release packages for all available versions in bin/ directory
- **`gradle clean`** - Clean build artifacts and temporary files

#### Information Tasks

- **`gradle info`** - Display build configuration and environment information
- **`gradle listVersions`** - List all available MySQL versions in bin/ and bin/archived/ directories
- **`gradle listReleases`** - List all available releases from modules-untouched repository
- **`gradle tasks`** - List all available Gradle tasks

#### Verification Tasks

- **`gradle verify`** - Verify build environment and dependencies
- **`gradle validateProperties`** - Validate build.properties configuration
- **`gradle checkModulesUntouched`** - Check modules-untouched repository integration

### Build Output

Release packages are created in the `build/release/` directory with the following structure:

```
build/
├── release/
│   ├── mysql8.0.40.7z          # Main archive
│   ├── mysql8.0.40.7z.md5      # MD5 hash
│   ├── mysql8.0.40.7z.sha1     # SHA1 hash
│   ├── mysql8.0.40.7z.sha256   # SHA256 hash
│   └── mysql8.0.40.7z.sha512   # SHA512 hash
└── tmp/                         # Temporary build files
```

### Modules-Untouched Integration

The build system integrates with the [modules-untouched](https://github.com/Bearsampp/modules-untouched) repository to:

1. Fetch available MySQL versions from `mysql.properties`
2. Resolve download URLs for specific versions
3. Provide fallback to standard URL format if remote fetch fails

To check the integration status:

```bash
gradle checkModulesUntouched
```

### Directory Structure

```
module-mysql/
├── bin/                    # MySQL binaries (organized by version)
│   ├── mysql8.0.40/
│   ├── mysql8.4.0/
│   └── archived/          # Archived versions
├── build/                 # Build output directory
│   ├── release/          # Final release packages
│   └── tmp/              # Temporary build files
├── img/                  # Images and assets
├── build.gradle          # Gradle build configuration
├── build.properties      # Build properties
├── gradle.properties     # Gradle configuration
├── settings.gradle       # Gradle settings
└── README.md            # This file
```

### Environment Variables

- **`7Z_HOME`** - Path to 7-Zip installation (optional, auto-detected if not set)

Example:
```bash
set 7Z_HOME=C:\Program Files\7-Zip
```

### Troubleshooting

#### 7-Zip Not Found

If you encounter "7-Zip not found" error:

1. Install 7-Zip from https://www.7-zip.org/
2. Set the `7Z_HOME` environment variable
3. Or ensure 7z.exe is in your system PATH

#### Version Not Found

If a version is not found in bin/:

1. Check available versions: `gradle listVersions`
2. Ensure the version directory exists in `bin/` or `bin/archived/`
3. Directory should be named like `mysql8.0.40`

#### Build Verification Failed

Run the verification task to identify issues:

```bash
gradle verify
```

This will check:
- Java version (8+)
- Required files (build.properties)
- Directory structure (dev, bin)
- 7-Zip availability (if using 7z format)

### Advanced Usage

#### Custom Build Path

You can specify a custom build path in `build.properties`:

```properties
build.path = C:/bearsampp-build
```

#### Parallel Builds

Gradle is configured for parallel execution in `gradle.properties`:

```properties
org.gradle.parallel=true
org.gradle.caching=true
```

#### Build Performance

The build system uses:
- Gradle daemon for faster builds
- Build caching for incremental builds
- Parallel execution when possible

## Issues

Issues must be reported on [bearsampp repository](https://github.com/bearsampp/bearsampp/issues).

## License

This project is licensed under the terms specified in the LICENSE file.
