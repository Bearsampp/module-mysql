# Modules-Untouched Integration

## Overview

The MySQL module integrates with the [modules-untouched](https://github.com/Bearsampp/modules-untouched) repository to automatically resolve MySQL version URLs and provide a centralized source of truth for available versions.

## What is modules-untouched?

The modules-untouched repository serves as a central registry for all Bearsampp module versions and their download URLs. It contains properties files for each module type (mysql, apache, php, etc.) that map version numbers to download URLs.

## Integration Features

### 1. Automatic Version Discovery

The build system can fetch available MySQL versions from the remote repository:

```bash
gradle listReleases
```

This fetches `mysql.properties` from:
```
https://raw.githubusercontent.com/Bearsampp/modules-untouched/main/modules/mysql.properties
```

### 2. URL Resolution

When building a version, the system resolves download URLs in this order:

1. **Local bin/ directory** - Check if binaries already exist locally
2. **modules-untouched** - Fetch URL from mysql.properties
3. **Standard format** - Construct URL using standard MySQL download format

### 3. Graceful Fallback

If the modules-untouched repository is unavailable:
- The system falls back to standard URL format
- Build continues without interruption
- Clear warning messages are displayed

## How It Works

### Version Resolution Flow

```
gradle release -PbundleVersion=8.0.40
  ↓
Check bin/mysql8.0.40/ exists?
  ↓ No
Fetch mysql.properties from modules-untouched
  ↓
Found 8.0.40 in mysql.properties?
  ↓ Yes
Use URL from mysql.properties
  ↓
Download MySQL binaries
  ↓
Extract and build package
  ✓ Success
```

### Fallback Flow

```
Fetch mysql.properties from modules-untouched
  ↓ Failed (network error, etc.)
Use standard URL format
  ↓
https://downloads.mysql.com/archives/get/p/23/file/mysql-8.0.40-winx64.zip
  ↓
Download MySQL binaries
  ↓
Extract and build package
  ✓ Success
```

## Implementation Details

### Fetching mysql.properties

The build system includes a helper function to fetch the properties file:

```groovy
def fetchModulesUntouchedProperties() {
    def propsUrl = "https://raw.githubusercontent.com/Bearsampp/modules-untouched/main/modules/mysql.properties"
    try {
        def connection = new URL(propsUrl).openConnection()
        connection.setRequestProperty("User-Agent", "Bearsampp-Module-MySQL-Build")
        connection.setConnectTimeout(10000)
        connection.setReadTimeout(10000)

        if (connection.responseCode == 200) {
            def props = new Properties()
            connection.inputStream.withCloseable { stream ->
                props.load(stream)
            }
            return props
        }
    } catch (Exception e) {
        println "[WARNING] Could not fetch mysql.properties: ${e.message}"
        return null
    }
}
```

### URL Resolution

```groovy
def resolveDownloadUrl(String version) {
    // First, try to get URL from modules-untouched
    def untouchedProps = fetchModulesUntouchedProperties()
    if (untouchedProps && untouchedProps.containsKey(version)) {
        return untouchedProps.getProperty(version)
    }

    // Fallback: construct standard URL format
    println "[INFO] Version ${version} not found in modules-untouched, using standard URL format"
    return "https://downloads.mysql.com/archives/get/p/23/file/mysql-${version}-winx64.zip"
}
```

## mysql.properties Format

The mysql.properties file in modules-untouched follows this format:

```properties
# MySQL versions and download URLs
5.7.39 = https://downloads.mysql.com/archives/get/p/23/file/mysql-5.7.39-winx64.zip
5.7.43 = https://downloads.mysql.com/archives/get/p/23/file/mysql-5.7.43-winx64.zip
8.0.27 = https://downloads.mysql.com/archives/get/p/23/file/mysql-8.0.27-winx64.zip
8.0.40 = https://downloads.mysql.com/archives/get/p/23/file/mysql-8.0.40-winx64.zip
8.4.0 = https://downloads.mysql.com/archives/get/p/23/file/mysql-8.4.0-winx64.zip
9.0.1 = https://downloads.mysql.com/archives/get/p/23/file/mysql-9.0.1-winx64.zip
```

## Usage Examples

### Check Integration Status

```bash
gradle checkModulesUntouched
```

Output:
```
======================================================================
Modules-Untouched Integration Check
======================================================================

Repository URL:
  https://raw.githubusercontent.com/Bearsampp/modules-untouched/main/modules/mysql.properties

Fetching mysql.properties from modules-untouched...

======================================================================
Available Versions in modules-untouched
======================================================================
  5.7.39
  5.7.43
  8.0.27
  8.0.40
  8.4.0
  9.0.1
======================================================================
Total versions: 26

[SUCCESS] modules-untouched integration is working

Version Resolution Strategy:
  1. Check modules-untouched mysql.properties (remote)
  2. Construct standard URL format (fallback)
```

### List Available Releases

```bash
gradle listReleases
```

Output:
```
Available MySQL Releases (modules-untouched):
--------------------------------------------------------------------------------
  5.7.39     -> https://downloads.mysql.com/archives/get/p/23/file/mysql-5.7.39-winx64.zip
  5.7.43     -> https://downloads.mysql.com/archives/get/p/23/file/mysql-5.7.43-winx64.zip
  8.0.27     -> https://downloads.mysql.com/archives/get/p/23/file/mysql-8.0.27-winx64.zip
  ...
--------------------------------------------------------------------------------
Total releases: 26
```

## Benefits

### 1. Centralized Version Management
- Single source of truth for all MySQL versions
- Easy to add new versions
- Consistent across all Bearsampp modules

### 2. Automatic Updates
- New versions automatically available
- No need to update individual module repositories
- Fetch latest versions on demand

### 3. Reliability
- Graceful fallback to standard URLs
- No breaking changes if repository unavailable
- Clear error messages and warnings

### 4. Flexibility
- Support for custom download URLs
- Easy to override for specific versions
- Compatible with existing workflows

## Error Handling

### Network Errors

If the modules-untouched repository is unreachable:

```
[WARNING] Could not fetch mysql.properties from modules-untouched: Connection timeout
[INFO] Version 8.0.40 not found in modules-untouched, using standard URL format
```

The build continues using the standard URL format.

### Version Not Found

If a version is not in mysql.properties:

```
[INFO] Version 8.0.40 not found in modules-untouched, using standard URL format
```

The build attempts to download from the standard MySQL download location.

### Invalid Response

If the server returns an error:

```
[WARNING] Failed to fetch mysql.properties: HTTP 404
```

The build falls back to standard URL format.

## Configuration

### Timeouts

Connection and read timeouts are set to 10 seconds:

```groovy
connection.setConnectTimeout(10000)
connection.setReadTimeout(10000)
```

### User Agent

Requests include a custom user agent:

```groovy
connection.setRequestProperty("User-Agent", "Bearsampp-Module-MySQL-Build")
```

## Maintenance

### Adding New Versions

To add a new MySQL version to modules-untouched:

1. Fork the modules-untouched repository
2. Edit `modules/mysql.properties`
3. Add the new version and URL:
   ```properties
   9.5.0 = https://downloads.mysql.com/archives/get/p/23/file/mysql-9.5.0-winx64.zip
   ```
4. Submit a pull request

### Updating URLs

If a download URL changes:

1. Update the URL in `modules/mysql.properties`
2. The change is immediately available to all builds
3. No need to update individual module repositories

## Testing

### Test Integration

```bash
# Check if integration is working
gradle checkModulesUntouched

# List available releases
gradle listReleases

# Verify environment
gradle verify
```

### Test Fallback

To test the fallback mechanism:

1. Temporarily disconnect from the internet
2. Run a build command
3. Verify it falls back to standard URL format

## See Also

- **[QUICK_REFERENCE.md](QUICK_REFERENCE.md)** - Quick reference guide
- **[FEATURE_SUMMARY.md](FEATURE_SUMMARY.md)** - Complete feature list
- **[modules-untouched repository](https://github.com/Bearsampp/modules-untouched)** - Central version registry
