# MySQL Module CI/CD Testing

## Overview

This document describes the automated testing workflow for the Bearsampp MySQL module. The CI/CD pipeline ensures that MySQL builds are properly packaged, can be extracted, and that all executables are functional.

## Workflow Triggers

The MySQL testing workflow is triggered automatically on:

- **Pull Request Events**: When a PR is opened, synchronized (new commits pushed), reopened, or edited
  - Target branches: `main` or `develop`
- **Manual Workflow Dispatch**: Can be triggered manually with options to test specific versions

## Test Scope

The workflow intelligently determines which versions to test based on the context:

### Pull Request Testing
- **Smart Detection**: Automatically detects which MySQL versions were added or modified in the PR
- **Targeted Testing**: Only tests the versions that changed in `releases.properties`
- **Efficiency**: Reduces CI runtime by testing only relevant versions
- **Fallback**: If no version changes detected, tests the latest 5 versions
- **Comprehensive**: Tests all versions including RC (Release Candidate), beta, and alpha versions

### Manual Testing
- **Latest 5 Versions**: Tests the 5 most recent versions from `releases.properties`
- **Specific Version**: Tests a single version provided as input parameter
- **Flexibility**: Useful for re-testing or validating specific versions

### Example Scenarios
- **Add MySQL 9.5.0**: Only version 9.5.0 is tested
- **Add versions 8.4.7 and 9.5.0**: Both versions are tested
- **Modify existing version URL**: That specific version is tested
- **Non-version changes**: Latest 5 versions tested as fallback

## Test Phases

### Phase 1: Installation Validation

**Purpose**: Verify the MySQL package can be downloaded and extracted correctly.

**Steps**:
1. **Download MySQL** (Phase 1.1)
   - Reads the download URL from `releases.properties`
   - Downloads the `.7z` archive from GitHub releases
   - Reports download size and success status
   - Validates file size (must be > 0.1 MB)

2. **Extract Archive** (Phase 1.1)
   - Extracts the `.7z` file using 7-Zip
   - Locates the `mysql*` subfolder
   - Validates the directory structure
   - Verifies `bin` directory exists

3. **Verify Executables** (Phase 1.2)
   - Checks for required MySQL executables:
     - `mysqld.exe` - MySQL server daemon
     - `mysql.exe` - MySQL command-line client
     - `mysqladmin.exe` - MySQL administration utility
     - `mysqldump.exe` - MySQL backup utility
   - Retrieves and displays MySQL version
   - Saves verification results to JSON

### Phase 2: Basic Functionality

**Purpose**: Verify MySQL executables can run and display version information.

**Steps**:
1. **Test Executable Versions** (Phase 2)
   - Runs `mysqld.exe --version` to verify server executable
   - Runs `mysql.exe --version` to verify client executable
   - Runs `mysqladmin.exe --version` to verify admin utility
   - Validates all executables return successfully
   - Displays version output for verification

## Test Results

### Artifacts

The workflow generates and uploads the following artifacts:

1. **Test Results** (`test-results-mysql-VERSION`)
   - `verify.json` - Executable verification results
   - `summary.md` - Human-readable test summary
   - Retention: 30 days

### PR Comments

For pull requests, the workflow automatically posts a comment with:
- Test date and time (UTC)
- Summary for each tested version
- Pass/fail status for each phase
- Overall test status
- Troubleshooting tips for failures
- Links to detailed artifacts

The comment is updated if it already exists (no duplicate comments).

### Test Summary Format

Each version's summary includes:

```
### MySQL X.Y.Z

**Phase 1: Installation Validation**
- Download & Extract: ✅ PASS / ❌ FAIL
- Verify Executables: ✅ PASS / ❌ FAIL

**Phase 2: Basic Functionality**
- Test Executables: ✅ PASS / ❌ FAIL

**Overall Status:** ✅ ALL TESTS PASSED / ❌ SOME TESTS FAILED
```

If tests fail, additional troubleshooting information is provided:
- Specific error messages
- Collapsible troubleshooting tips
- Links to workflow logs
- References to test artifacts

## Error Handling

The workflow provides comprehensive error reporting at multiple levels:

### Detailed Error Messages

Each phase captures and reports specific error information:

- **Download failures**: 
  - HTTP status codes and error messages
  - Attempted URLs
  - File size validation errors
  
- **Extraction failures**: 
  - 7-Zip exit codes and output logs
  - Directory listings showing actual structure
  - Expected vs. actual directory patterns
  
- **Missing files**: 
  - List of expected executables
  - Actual directory contents
  - Specific missing files highlighted
  
- **Execution failures**: 
  - Command output and error messages
  - Exit codes
  - Version information (if available)

### Error Propagation

- Each phase uses `continue-on-error: true` to allow subsequent phases to run
- Failed phases are clearly marked in the summary with ❌ indicators
- Error messages are included inline in test summaries
- All test results are uploaded regardless of success/failure
- Overall workflow status reflects any failures

### Troubleshooting Assistance

When tests fail, the summary includes:
- Specific error messages for each failed phase
- Collapsible troubleshooting tips section with:
  - Links to detailed workflow logs
  - Instructions to download test artifacts
  - Common issues and solutions
  - Archive structure validation tips
  - DLL dependency checks

## Platform

- **Runner**: `windows-latest`
- **Reason**: MySQL builds are Windows executables (.exe)
- **Tools**: Native Windows PowerShell, 7-Zip (pre-installed)
- **OS**: Windows Server (latest GitHub Actions runner)

## Matrix Strategy

- **Parallel Execution**: Tests run in parallel for different versions
- **Fail-Fast**: Disabled (`fail-fast: false`) to test all versions even if one fails
- **Dynamic Matrix**: Version list is generated dynamically based on trigger context
- **Resource Efficiency**: Only tests relevant versions to minimize CI time

## Workflow File Location

`.github/workflows/mysql-test.yml`

## Maintenance

### Adding New Test Cases

To add new functionality tests:

1. Add a new step after Phase 2
2. Use `continue-on-error: true` to prevent blocking other tests
3. Check previous phase success with `if: steps.PREVIOUS_STEP.outputs.success == 'true'`
4. Set output variable: `echo "success=true/false" >> $env:GITHUB_OUTPUT`
5. Update the test summary generation to include the new phase
6. Document the new phase in this file

### Modifying Version Selection

To change which versions are tested, edit the `Get MySQL Versions` step:

```bash
# Current: Latest 5 versions
VERSIONS=$(... | head -5 | ...)

# Example: Latest 3 versions
VERSIONS=$(... | head -3 | ...)

# Example: Latest 10 versions
VERSIONS=$(... | head -10 | ...)
```

### Adjusting Test Parameters

Key parameters that can be adjusted:

- **Download timeout**: `TimeoutSec 300` (5 minutes) in Phase 1.1
- **Minimum file size**: `0.1 MB` validation in Phase 1.1
- **Artifact retention**: `retention-days: 30` in Upload Test Results step

### Adding New Executables to Verify

To verify additional MySQL executables:

1. Edit Phase 1.2 in the workflow file
2. Add the executable name to the `$requiredExes` array:
   ```powershell
   $requiredExes = @("mysqld.exe", "mysql.exe", "mysqladmin.exe", "mysqldump.exe", "NEW_EXE.exe")
   ```
3. Optionally add version test in Phase 2

## Troubleshooting

### Common Issues

1. **Download Failures**
   - **Symptom**: Phase 1.1 fails with download error
   - **Causes**:
     - Invalid URL in `releases.properties`
     - GitHub release not published or deleted
     - Network connectivity issues
     - Rate limiting from GitHub
   - **Solutions**:
     - Verify the release URL is accessible in a browser
     - Check GitHub releases page for the module
     - Ensure the version number matches exactly
     - Wait and retry if rate limited

2. **Extraction Failures**
   - **Symptom**: Phase 1.1 fails after successful download
   - **Causes**:
     - Corrupted `.7z` file
     - Incorrect archive structure
     - Insufficient disk space
   - **Solutions**:
     - Re-download and re-upload the release
     - Verify archive structure locally before uploading
     - Check that archive contains `mysql*` directory with `bin` subdirectory

3. **Missing Executables**
   - **Symptom**: Phase 1.2 fails with missing files
   - **Causes**:
     - Incomplete MySQL build
     - Wrong directory structure in archive
     - Missing DLL dependencies
   - **Solutions**:
     - Verify all required executables are in the `bin` directory
     - Check that the build is complete and not a partial package
     - Ensure all dependencies are included in the archive

4. **Version Command Failures**
   - **Symptom**: Phase 2 fails when running version commands
   - **Causes**:
     - Missing DLL dependencies
     - Corrupted executables
     - Incompatible Windows version
   - **Solutions**:
     - Include all required DLLs in the archive
     - Test the build locally on Windows
     - Check for Visual C++ runtime dependencies

### Debugging Steps

To debug a specific version failure:

1. **Check Workflow Logs**
   - Navigate to the failed workflow run in GitHub Actions
   - Expand the failed step to see detailed output
   - Look for specific error messages

2. **Download Artifacts**
   - Download the `test-results-mysql-VERSION` artifact
   - Review `verify.json` for executable validation details
   - Check `summary.md` for formatted results

3. **Local Testing**
   - Download the same `.7z` file from releases
   - Extract it locally on Windows
   - Try running the executables manually
   - Check for missing DLL errors

4. **Compare with Working Versions**
   - Compare directory structure with a passing version
   - Check file sizes and contents
   - Verify all dependencies are present

### Manual Workflow Testing

To manually test a specific version:

1. Go to the Actions tab in GitHub
2. Select "MySQL Module Tests" workflow
3. Click "Run workflow"
4. Choose "Specific version" from the dropdown
5. Enter the version number (e.g., "9.5.0")
6. Click "Run workflow"
7. Monitor the test results

## Best Practices

1. **Always test before merging**: The workflow runs on PRs to catch issues early
2. **Review test summaries**: Check PR comments for test results before merging
3. **Investigate failures**: Don't merge if tests fail without understanding why
4. **Keep versions updated**: Regularly update `releases.properties` with new MySQL versions
5. **Monitor artifact storage**: Old artifacts are automatically cleaned up after retention period
6. **Test locally first**: Before creating a PR, test the MySQL build locally on Windows
7. **Document changes**: Update this documentation when modifying the workflow
8. **Version consistency**: Ensure version numbers in filenames match `releases.properties`

## Version Naming Conventions

MySQL versions in `releases.properties` should follow these patterns:

- **Major.Minor.Patch**: `9.5.0`, `8.4.7`, `8.0.44`
- **Major.Minor**: `8.4`, `8.3.0`
- **Legacy versions**: `5.7.39`

The workflow supports all version formats and will test them accordingly.

## Workflow Outputs

### Success Indicators

When all tests pass, you'll see:
- ✅ Green checkmark on the PR
- "All tests passed" status in PR comment
- All phases marked with ✅ PASS
- Artifacts uploaded successfully

### Failure Indicators

When tests fail, you'll see:
- ❌ Red X on the PR
- "Some tests failed" status in PR comment
- Failed phases marked with ❌ FAIL
- Error messages in the summary
- Troubleshooting tips displayed

## Related Documentation

- [MySQL Official Documentation](https://dev.mysql.com/doc/)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Bearsampp Project](https://github.com/Bearsampp)
- [Module MySQL Repository](https://github.com/Bearsampp/module-mysql)

## Changelog

### Version History

- **Initial Release**: Basic download and extraction testing
- **Current**: Two-phase testing with executable verification and version checks

### Future Enhancements

Potential improvements for future versions:

1. **Database Initialization Testing**: Add tests for `mysqld --initialize`
2. **Server Startup Testing**: Test starting and stopping MySQL server
3. **Connection Testing**: Verify client can connect to server
4. **Basic SQL Testing**: Execute simple SQL commands
5. **Performance Benchmarks**: Add basic performance metrics
6. **Security Checks**: Verify default security settings

## Contributing to Tests

To contribute improvements to the testing workflow:

1. Fork the repository
2. Create a feature branch
3. Modify `.github/workflows/mysql-test.yml`
4. Update this documentation
5. Test your changes with manual workflow dispatch
6. Submit a pull request with:
   - Description of changes
   - Rationale for improvements
   - Test results showing the changes work

## Support and Contact

For questions or issues with the CI/CD workflow:

- **GitHub Issues**: [Open an issue](https://github.com/Bearsampp/module-mysql/issues)
- **Discussions**: Use GitHub Discussions for questions
- **Pull Requests**: Submit improvements via PR

When reporting issues, please include:
- Version number being tested
- Link to failed workflow run
- Error messages from logs
- Steps to reproduce (if applicable)
