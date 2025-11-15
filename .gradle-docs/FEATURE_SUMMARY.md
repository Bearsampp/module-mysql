# Feature Summary: MySQL Module Build System

## Overview

The MySQL module uses a modern Gradle-based build system that provides comprehensive features for building, packaging, and managing MySQL releases for Bearsampp.

## Core Features

### 1. Modern Gradle Build System

**Native Gradle Implementation**
- Pure Gradle build configuration (no Ant dependencies)
- Leverages Gradle's modern features and ecosystem
- Compatible with Gradle 7.0+
- Uses Gradle wrapper for consistent builds

**Benefits:**
- Faster builds with incremental compilation
- Better dependency management
- Modern tooling support
- Easier maintenance and updates

### 2. Multi-Version Support

**Single Version Build**
```bash
gradle release -PbundleVersion=8.0.40
```

**All Versions Build**
```bash
gradle releaseAll
```

**Features:**
- Build specific MySQL version
- Build all available versions in one command
- Support for both bin/ and bin/archived/ directories
- Automatic version detection
- Detailed build summary with success/failure counts

### 3. Automatic Hash Generation

**Supported Hash Algorithms:**
- MD5
- SHA1
- SHA256
- SHA512

**Output Format:**
```
mysql8.0.40.7z
mysql8.0.40.7z.md5
mysql8.0.40.7z.sha1
mysql8.0.40.7z.sha256
mysql8.0.40.7z.sha512
```

**Benefits:**
- Verify package integrity
- Ensure secure downloads
- Comply with security best practices
- Automatic generation during build

### 4. Modules-Untouched Integration

**Automatic Version Resolution**
- Fetch available versions from remote repository
- Resolve download URLs automatically
- Graceful fallback to standard URLs
- No breaking changes if repository unavailable

**Commands:**
```bash
gradle listReleases              # List remote versions
gradle checkModulesUntouched     # Check integration status
```

**See:** [MODULES_UNTOUCHED_INTEGRATION.md](MODULES_UNTOUCHED_INTEGRATION.md)

### 5. Comprehensive Verification

**Environment Verification**
```bash
gradle verify
```

**Checks:**
- Java version (8+)
- Required files (build.properties)
- Directory structure (dev, bin)
- 7-Zip availability (if using 7z format)

**Properties Validation**
```bash
gradle validateProperties
```

**Validates:**
- bundle.name
- bundle.release
- bundle.type
- bundle.format

### 6. Archive Format Support

**Supported Formats:**
- **7z** - High compression using 7-Zip
- **zip** - Standard ZIP format using Gradle

**Configuration:**
```properties
bundle.format = 7z
```

**7-Zip Features:**
- Maximum compression (-mx9)
- Automatic 7-Zip detection
- Support for custom 7Z_HOME environment variable
- Fallback to common installation paths

### 7. Build Caching

**Gradle Build Cache**
- Enabled by default
- Stores build outputs for reuse
- Speeds up incremental builds
- Shared across builds

**Configuration:**
```properties
org.gradle.caching=true
```

**Benefits:**
- Faster subsequent builds
- Efficient disk usage
- Automatic cache management

### 8. Information and Help Tasks

**Build Information**
```bash
gradle info
```

**List Tasks**
```bash
gradle tasks
```

**List Versions**
```bash
gradle listVersions              # Local versions
gradle listReleases              # Remote versions
```

**Features:**
- Comprehensive build information
- Task descriptions and groups
- Version listing with location indicators
- Quick start commands

### 9. Clean Build Support

**Clean Command**
```bash
gradle clean
```

**Cleans:**
- Build directory
- Temporary files
- Generated artifacts

**Benefits:**
- Fresh start for builds
- Remove stale artifacts
- Free up disk space

### 10. Parallel Execution

**Configuration:**
```properties
org.gradle.parallel=true
org.gradle.daemon=true
```

**Benefits:**
- Faster builds on multi-core systems
- Efficient resource utilization
- Background daemon for quick startup

## Task Reference

### Build Tasks

| Task | Description |
|------|-------------|
| `release` | Build release package for specific version |
| `releaseAll` | Build all available versions |
| `clean` | Clean build artifacts |

### Information Tasks

| Task | Description |
|------|-------------|
| `info` | Display build configuration |
| `tasks` | List all available tasks |
| `listVersions` | List local versions |
| `listReleases` | List remote versions |

### Verification Tasks

| Task | Description |
|------|-------------|
| `verify` | Verify build environment |
| `validateProperties` | Validate build.properties |
| `checkModulesUntouched` | Check integration status |

## Configuration Files

### build.properties

```properties
bundle.name = mysql
bundle.release = 2025.8.21
bundle.type = bins
bundle.format = 7z
```

**Purpose:** Bundle configuration and metadata

### gradle.properties

```properties
org.gradle.daemon=true
org.gradle.parallel=true
org.gradle.caching=true
org.gradle.jvmargs=-Xmx2g -XX:MaxMetaspaceSize=512m
```

**Purpose:** Gradle performance and behavior settings

### settings.gradle

```groovy
rootProject.name = 'module-mysql'
enableFeaturePreview('STABLE_CONFIGURATION_CACHE')
```

**Purpose:** Project settings and Gradle features

## Build Output

### Directory Structure

```
build/
├── release/                    # Final release packages
│   ├── mysql8.0.40.7z
│   ├── mysql8.0.40.7z.md5
│   ├── mysql8.0.40.7z.sha1
│   ├── mysql8.0.40.7z.sha256
│   └── mysql8.0.40.7z.sha512
└── tmp/                        # Temporary build files
    └── mysql8.0.40/
```

### Archive Contents

The release archive contains:
- MySQL binaries (bin/)
- MySQL libraries (lib/)
- MySQL includes (include/)
- MySQL share files (share/)
- Documentation (docs/)
- Configuration files

## Performance Features

### 1. Incremental Builds
- Only rebuild changed components
- Skip up-to-date tasks
- Faster iteration during development

### 2. Build Cache
- Reuse outputs from previous builds
- Share cache across projects
- Automatic cache invalidation

### 3. Parallel Execution
- Execute independent tasks in parallel
- Utilize multi-core processors
- Configurable worker threads

### 4. Gradle Daemon
- Keep Gradle process running
- Faster subsequent builds
- Reduced startup time

## Error Handling

### Clear Error Messages

**Version Not Found:**
```
Bundle version not found: mysql8.0.40

Available versions:
  - 8.4.0
  - 9.0.1
```

**7-Zip Not Found:**
```
7-Zip not found! Please install 7-Zip or set 7Z_HOME environment variable.

Download from: https://www.7-zip.org/
```

**Missing Files:**
```
mysqld.exe not found at bin/mysql8.0.40/bin/mysqld.exe
```

### Graceful Degradation

- Fallback to standard URLs if modules-untouched unavailable
- Continue build with warnings for non-critical issues
- Detailed error messages with solutions

## Integration Points

### 1. Modules-Untouched Repository
- Fetch version information
- Resolve download URLs
- Centralized version management

### 2. Build Environment
- Java runtime
- 7-Zip (optional)
- Gradle wrapper

### 3. File System
- bin/ directory for MySQL binaries
- build/ directory for outputs
- Temporary directories for processing

## Best Practices

### 1. Version Management
- Keep bin/ directory organized
- Use archived/ subdirectory for old versions
- Regular cleanup of temporary files

### 2. Build Verification
- Run `gradle verify` before building
- Check environment setup
- Validate configuration files

### 3. Performance Optimization
- Enable build cache
- Use parallel execution
- Keep Gradle daemon running

### 4. Error Recovery
- Check error messages carefully
- Use verification tasks to diagnose issues
- Clean build if problems persist

## Future Enhancements

Potential future features:
- Automatic download from modules-untouched
- Incremental archive updates
- Build profiles for different configurations
- Integration with CI/CD pipelines
- Automated testing of built packages

## See Also

- **[QUICK_REFERENCE.md](QUICK_REFERENCE.md)** - Quick reference guide
- **[MODULES_UNTOUCHED_INTEGRATION.md](MODULES_UNTOUCHED_INTEGRATION.md)** - Integration details
- **[CONFIGURATION_SUMMARY.md](CONFIGURATION_SUMMARY.md)** - Configuration guide
- **[README.md](../README.md)** - Main project README
