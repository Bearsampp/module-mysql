# Quick Reference: MySQL Module Build System

## TL;DR

The MySQL module uses a modern Gradle build system with automatic version resolution from the modules-untouched repository.

## URL Resolution Order

```
1. Local bin/ directory (existing binaries)
   ↓ (if not found)
2. mysql.properties (modules-untouched)
   ↓ (if not found)
3. Standard URL format (constructed)
```

## Key URLs

- **mysql.properties**: `https://raw.githubusercontent.com/Bearsampp/modules-untouched/main/modules/mysql.properties`
- **Standard format**: `https://downloads.mysql.com/archives/get/p/23/file/mysql-{version}-winx64.zip`

## Essential Commands

### Information Commands
```bash
# Display build information
gradle info

# List all available tasks
gradle tasks

# List versions in bin/ directory
gradle listVersions

# List releases from modules-untouched
gradle listReleases
```

### Build Commands
```bash
# Build release (interactive mode)
gradle release

# Build specific version (non-interactive)
gradle release -PbundleVersion=8.0.40

# Build all available versions
gradle releaseAll

# Clean build artifacts
gradle clean
```

### Verification Commands
```bash
# Verify build environment
gradle verify

# Validate build.properties
gradle validateProperties

# Check modules-untouched integration
gradle checkModulesUntouched
```

## Build Flow

```
Building version 8.0.40
  ↓
Check bin/mysql8.0.40/ exists
  ↓
Copy MySQL files to build/tmp/
  ↓
Create archive (7z or zip)
  ↓
Generate hash files (MD5, SHA1, SHA256, SHA512)
  ↓
Output to build/release/
  ✓ Success
```

## Common Scenarios

### Build Single Version (Interactive)
```bash
# Interactive mode - prompts for version selection
gradle release

# Output:
# Available versions:
#   1. 8.0.42          [bin/archived]
#   2. 8.3.0           [bin/archived]
#   3. 8.4             [bin]
#   ...
# Enter version to build (index or version string):
```

### Build Single Version (Non-Interactive)
```bash
# Build MySQL 8.0.40 directly
gradle release -PbundleVersion=8.0.40
```

### Build All Versions
```bash
# Build all versions in bin/ directory
gradle releaseAll
```

### Check Available Versions
```bash
# List local versions
gradle listVersions

# List remote versions
gradle listReleases
```

### Verify Environment
```bash
# Check if everything is set up correctly
gradle verify
```

## Output Structure

After building version 8.0.40:

```
build/
├── release/
│   ├── mysql8.0.40.7z          # Main archive
│   ├── mysql8.0.40.7z.md5      # MD5 hash
│   ├── mysql8.0.40.7z.sha1     # SHA1 hash
│   ├── mysql8.0.40.7z.sha256   # SHA256 hash
│   └── mysql8.0.40.7z.sha512   # SHA512 hash
└── tmp/
    └── mysql8.0.40/            # Temporary build files
```

## Error Messages

### Version Not Found
```
Bundle version not found: mysql8.0.40

Available versions:
  - 8.4.0
  - 9.0.1
  - 9.1.0
```

**Solution**: Check available versions with `gradle listVersions`

### 7-Zip Not Found
```
7-Zip not found! Please install 7-Zip or set 7Z_HOME environment variable.
```

**Solution**: 
1. Install 7-Zip from https://www.7-zip.org/
2. Or set environment variable: `set 7Z_HOME=C:\Program Files\7-Zip`

### mysqld.exe Not Found
```
mysqld.exe not found at bin/mysql8.0.40/bin/mysqld.exe
```

**Solution**: Ensure MySQL binaries are properly extracted in bin/ directory

## Configuration

### build.properties
```properties
bundle.name = mysql
bundle.release = 2025.8.21
bundle.type = bins
bundle.format = 7z
```

### gradle.properties
```properties
org.gradle.daemon=true
org.gradle.parallel=true
org.gradle.caching=true
org.gradle.jvmargs=-Xmx2g -XX:MaxMetaspaceSize=512m
```

## Directory Structure

```
module-mysql/
├── bin/                    # MySQL binaries
│   ├── mysql8.0.40/
│   ├── mysql8.4.0/
│   └── archived/          # Archived versions
├── build/                 # Build output
│   ├── release/          # Final packages
│   └── tmp/              # Temporary files
├── .gradle-docs/         # Documentation
├── build.gradle          # Build configuration
├── build.properties      # Bundle properties
├── gradle.properties     # Gradle settings
└── settings.gradle       # Project settings
```

## Environment Variables

### Optional
- **`7Z_HOME`** - Path to 7-Zip installation (auto-detected if not set)

Example:
```bash
set 7Z_HOME=C:\Program Files\7-Zip
```

## Tips & Tricks

### Speed Up Builds
- Gradle daemon is enabled by default
- Build caching is enabled
- Parallel execution is enabled

### Clean Start
```bash
# Clean everything and rebuild
gradle clean
gradle release -PbundleVersion=8.0.40
```

### Check Integration
```bash
# Verify modules-untouched connection
gradle checkModulesUntouched
```

### List Everything
```bash
# See all available tasks
gradle tasks --all
```

## Benefits

✅ Modern Gradle build system  
✅ No Ant dependencies  
✅ Automatic hash generation  
✅ Multi-version support  
✅ Build caching for speed  
✅ Modules-untouched integration  
✅ Comprehensive verification  
✅ Clear error messages  

## See Also

- **[FEATURE_SUMMARY.md](FEATURE_SUMMARY.md)** - Complete feature list
- **[MODULES_UNTOUCHED_INTEGRATION.md](MODULES_UNTOUCHED_INTEGRATION.md)** - Integration details
- **[CONFIGURATION_SUMMARY.md](CONFIGURATION_SUMMARY.md)** - Configuration guide
- **[README.md](../README.md)** - Main project README
