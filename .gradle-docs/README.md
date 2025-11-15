# Bearsampp Module MySQL - Gradle Build Documentation

This directory contains comprehensive documentation for the Gradle build system used in the Bearsampp MySQL module.

## Documentation Files

### Quick Start
- **[QUICK_REFERENCE.md](QUICK_REFERENCE.md)** - Quick reference guide for common tasks and commands

### Build System
- **[FEATURE_SUMMARY.md](FEATURE_SUMMARY.md)** - Overview of all build system features
- **[CONFIGURATION_SUMMARY.md](CONFIGURATION_SUMMARY.md)** - Build configuration details

### Integration
- **[MODULES_UNTOUCHED_INTEGRATION.md](MODULES_UNTOUCHED_INTEGRATION.md)** - Integration with modules-untouched repository

## Quick Links

### Common Commands

```bash
# Display build information
gradle info

# List available versions
gradle listVersions

# Build specific version
gradle release -PbundleVersion=8.0.40

# Build all versions
gradle releaseAll

# Verify environment
gradle verify

# Clean build artifacts
gradle clean
```

### Key Features

1. **Modern Gradle Build** - Native Gradle implementation without Ant dependencies
2. **Modules-Untouched Integration** - Automatic version resolution from remote repository
3. **Hash Generation** - Automatic MD5, SHA1, SHA256, SHA512 hash file generation
4. **Multi-Version Support** - Build single or all available versions
5. **Build Caching** - Efficient incremental builds with caching
6. **Environment Verification** - Comprehensive build environment checks

### Directory Structure

```
module-mysql/
├── .gradle-docs/          # This documentation directory
├── bin/                   # MySQL binaries (organized by version)
│   ├── mysql8.0.40/
│   ├── mysql8.4.0/
│   └── archived/         # Archived versions
├── build/                # Build output directory
│   ├── release/         # Final release packages
│   └── tmp/             # Temporary build files
├── build.gradle         # Gradle build configuration
├── build.properties     # Build properties
├── gradle.properties    # Gradle configuration
└── settings.gradle      # Gradle settings
```

## Getting Started

1. **Prerequisites**
   - Java 8 or higher
   - Gradle 7.0+ (wrapper included)
   - 7-Zip (optional, for 7z format)

2. **Verify Environment**
   ```bash
   gradle verify
   ```

3. **List Available Versions**
   ```bash
   gradle listVersions
   ```

4. **Build a Release**
   ```bash
   gradle release -PbundleVersion=8.0.40
   ```

## Build Configuration

The build is configured through `build.properties`:

```properties
bundle.name = mysql
bundle.release = 2025.8.21
bundle.type = bins
bundle.format = 7z
```

## Output Structure

Release packages are created with the following structure:

```
build/release/
├── mysql8.0.40.7z          # Main archive
├── mysql8.0.40.7z.md5      # MD5 hash
├── mysql8.0.40.7z.sha1     # SHA1 hash
├── mysql8.0.40.7z.sha256   # SHA256 hash
└── mysql8.0.40.7z.sha512   # SHA512 hash
```

## Support

For issues and questions:
- Report issues on [bearsampp repository](https://github.com/bearsampp/bearsampp/issues)
- Check documentation in this directory
- Run `gradle tasks` to see all available tasks

## Version History

- **2025.8.21** - Modern Gradle build system implementation
  - Native Gradle build (no Ant dependencies)
  - Modules-untouched integration
  - Automatic hash generation
  - Multi-version build support
  - Enhanced verification and validation
