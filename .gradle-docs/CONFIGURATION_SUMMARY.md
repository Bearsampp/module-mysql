# Configuration Summary: MySQL Module Build System

## Overview

This document provides a comprehensive guide to configuring the MySQL module build system. The build system uses multiple configuration files to control behavior, performance, and output.

## Configuration Files

### 1. build.properties

**Location:** `build.properties` (project root)

**Purpose:** Bundle configuration and metadata

**Contents:**
```properties
bundle.name = mysql
bundle.release = 2025.8.21
bundle.type = bins
bundle.format = 7z
```

**Properties:**

| Property | Description | Default | Required |
|----------|-------------|---------|----------|
| `bundle.name` | Module name | `mysql` | Yes |
| `bundle.release` | Release version | - | Yes |
| `bundle.type` | Bundle type | `bins` | Yes |
| `bundle.format` | Archive format (7z/zip) | `7z` | Yes |

**Example:**
```properties
bundle.name = mysql
bundle.release = 2025.8.21
bundle.type = bins
bundle.format = 7z

# Optional: Custom build path
# build.path = C:/bearsampp-build
```

### 2. gradle.properties

**Location:** `gradle.properties` (project root)

**Purpose:** Gradle performance and behavior settings

**Contents:**
```properties
# Gradle daemon configuration
org.gradle.daemon=true
org.gradle.parallel=true
org.gradle.caching=true

# JVM settings for Gradle
org.gradle.jvmargs=-Xmx2g -XX:MaxMetaspaceSize=512m -XX:+HeapDumpOnOutOfMemoryError

# Configure console output
org.gradle.console=auto
org.gradle.warning.mode=all

# Build performance
org.gradle.configureondemand=false
```

**Properties:**

| Property | Description | Default | Recommended |
|----------|-------------|---------|-------------|
| `org.gradle.daemon` | Keep Gradle process running | `true` | `true` |
| `org.gradle.parallel` | Execute tasks in parallel | `true` | `true` |
| `org.gradle.caching` | Enable build cache | `true` | `true` |
| `org.gradle.jvmargs` | JVM memory settings | - | `-Xmx2g` |
| `org.gradle.console` | Console output mode | `auto` | `auto` |
| `org.gradle.warning.mode` | Warning display mode | `all` | `all` |

**JVM Settings Explained:**
- `-Xmx2g` - Maximum heap size (2GB)
- `-XX:MaxMetaspaceSize=512m` - Maximum metaspace size
- `-XX:+HeapDumpOnOutOfMemoryError` - Create heap dump on OOM

### 3. settings.gradle

**Location:** `settings.gradle` (project root)

**Purpose:** Project settings and Gradle features

**Contents:**
```groovy
/*
 * Bearsampp Module MySQL - Gradle Settings
 */

rootProject.name = 'module-mysql'

// Enable Gradle features for better performance
enableFeaturePreview('STABLE_CONFIGURATION_CACHE')

// Configure build cache for faster builds
buildCache {
    local {
        enabled = true
        directory = file("${rootDir}/.gradle/build-cache")
    }
}
```

**Configuration:**

| Setting | Description | Purpose |
|---------|-------------|---------|
| `rootProject.name` | Project name | Identifies the project |
| `enableFeaturePreview` | Enable preview features | Access new Gradle features |
| `buildCache.local` | Local build cache | Store build outputs |

## Environment Variables

### Optional Variables

#### 7Z_HOME

**Purpose:** Specify 7-Zip installation directory

**Usage:**
```bash
# Windows Command Prompt
set 7Z_HOME=C:\Program Files\7-Zip

# PowerShell
$env:7Z_HOME = "C:\Program Files\7-Zip"

# Linux/Mac
export 7Z_HOME=/usr/local/bin
```

**Auto-Detection:**
If not set, the build system checks:
1. `C:/Program Files/7-Zip/7z.exe`
2. `C:/Program Files (x86)/7-Zip/7z.exe`
3. `D:/Program Files/7-Zip/7z.exe`
4. `D:/Program Files (x86)/7-Zip/7z.exe`
5. System PATH

## Build Configuration

### Archive Format

**7z Format (Default):**
```properties
bundle.format = 7z
```

**Features:**
- High compression ratio
- Requires 7-Zip installation
- Maximum compression level (-mx9)

**ZIP Format:**
```properties
bundle.format = zip
```

**Features:**
- Standard ZIP format
- No external dependencies
- Built-in Gradle support

### Build Path

**Default:**
```
build/release/
```

**Custom Path:**
```properties
# In build.properties
build.path = C:/bearsampp-build
```

**Benefits:**
- Centralized build output
- Shared across modules
- Custom organization

## Performance Tuning

### Memory Settings

**Default JVM Settings:**
```properties
org.gradle.jvmargs=-Xmx2g -XX:MaxMetaspaceSize=512m
```

**For Large Builds:**
```properties
org.gradle.jvmargs=-Xmx4g -XX:MaxMetaspaceSize=1g
```

**For Limited Memory:**
```properties
org.gradle.jvmargs=-Xmx1g -XX:MaxMetaspaceSize=256m
```

### Parallel Execution

**Enable Parallel Builds:**
```properties
org.gradle.parallel=true
```

**Configure Workers:**
```properties
org.gradle.workers.max=4
```

**Recommendations:**
- Set workers to number of CPU cores
- Leave 1-2 cores for system
- Monitor memory usage

### Build Cache

**Enable Local Cache:**
```properties
org.gradle.caching=true
```

**Cache Location:**
```
.gradle/build-cache/
```

**Benefits:**
- Faster incremental builds
- Reuse previous outputs
- Automatic invalidation

## Directory Structure

### Source Directories

```
module-mysql/
├��─ bin/                    # MySQL binaries
│   ├── mysql8.0.40/
│   ├── mysql8.4.0/
│   └── archived/          # Archived versions
├── .gradle/               # Gradle cache
├── .gradle-docs/          # Documentation
└── build/                 # Build output
```

### Build Output

```
build/
├── release/               # Final packages
│   ├── mysql8.0.40.7z
│   ├── mysql8.0.40.7z.md5
│   ├── mysql8.0.40.7z.sha1
│   ├── mysql8.0.40.7z.sha256
│   └── mysql8.0.40.7z.sha512
└── tmp/                   # Temporary files
    └── mysql8.0.40/
```

## Task Configuration

### Default Task

**Configured in build.gradle:**
```groovy
defaultTasks 'info'
```

**Change Default:**
```groovy
defaultTasks 'tasks'
```

### Task Dependencies

**Release Task:**
- No dependencies (standalone)

**Clean Task:**
- Cleans build/ directory
- Removes temporary files

## Validation

### Validate Configuration

**Check build.properties:**
```bash
gradle validateProperties
```

**Verify Environment:**
```bash
gradle verify
```

**Check Integration:**
```bash
gradle checkModulesUntouched
```

## Common Configurations

### Development Configuration

**Optimized for fast iteration:**
```properties
# gradle.properties
org.gradle.daemon=true
org.gradle.parallel=true
org.gradle.caching=true
org.gradle.jvmargs=-Xmx2g
org.gradle.console=plain
```

### Production Configuration

**Optimized for reliability:**
```properties
# gradle.properties
org.gradle.daemon=false
org.gradle.parallel=false
org.gradle.caching=false
org.gradle.jvmargs=-Xmx4g
org.gradle.console=plain
```

### CI/CD Configuration

**Optimized for automation:**
```properties
# gradle.properties
org.gradle.daemon=false
org.gradle.parallel=true
org.gradle.caching=true
org.gradle.jvmargs=-Xmx4g
org.gradle.console=plain
org.gradle.warning.mode=all
```

## Troubleshooting

### Configuration Issues

**Problem:** Build fails with "Out of Memory"

**Solution:**
```properties
# Increase heap size
org.gradle.jvmargs=-Xmx4g -XX:MaxMetaspaceSize=1g
```

**Problem:** Slow builds

**Solution:**
```properties
# Enable performance features
org.gradle.daemon=true
org.gradle.parallel=true
org.gradle.caching=true
```

**Problem:** 7-Zip not found

**Solution:**
```bash
# Set environment variable
set 7Z_HOME=C:\Program Files\7-Zip
```

### Validation Errors

**Missing Properties:**
```
[ERROR] Missing required properties:
    - bundle.name
```

**Solution:** Add missing properties to build.properties

**Invalid Format:**
```
[ERROR] Invalid bundle.format: xyz
```

**Solution:** Use 7z or zip

## Best Practices

### 1. Version Control

**Include in Git:**
- build.properties
- gradle.properties
- settings.gradle

**Exclude from Git:**
- .gradle/
- build/
- *.log

### 2. Documentation

**Document Custom Settings:**
```properties
# Custom build path for centralized builds
build.path = C:/bearsampp-build

# Increased memory for large builds
org.gradle.jvmargs=-Xmx4g
```

### 3. Environment-Specific

**Use Profiles:**
```properties
# gradle-dev.properties
org.gradle.jvmargs=-Xmx2g

# gradle-prod.properties
org.gradle.jvmargs=-Xmx4g
```

**Load Profile:**
```bash
gradle -I gradle-dev.properties release
```

## See Also

- **[QUICK_REFERENCE.md](QUICK_REFERENCE.md)** - Quick reference guide
- **[FEATURE_SUMMARY.md](FEATURE_SUMMARY.md)** - Complete feature list
- **[MODULES_UNTOUCHED_INTEGRATION.md](MODULES_UNTOUCHED_INTEGRATION.md)** - Integration details
- **[Gradle Documentation](https://docs.gradle.org/)** - Official Gradle docs
