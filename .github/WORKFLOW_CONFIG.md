# Workflow Configuration Guide

## Overview
This document describes the enhanced GitHub Actions workflow configuration for the Brain Tumor Detection project.

## Workflow Files

### 1. CI Pipeline (`ci.yml`)
**Purpose**: Continuous Integration for testing and building

**Triggers**:
- Push to `main` or `develop` branches
- Pull requests to `main` or `develop`
- Manual trigger via workflow_dispatch

**Jobs**:
- **test**: Runs test suite on self-hosted runner
  - Sets up Python 3.9
  - Installs dependencies
  - Runs linting with flake8
  - Executes tests
  - Uploads test results as artifacts

- **build**: Builds the application
  - Depends on test job completion
  - Compiles Python files
  - Uploads build artifacts

**Enhanced Features**:
- Concurrency control to cancel in-progress runs
- Granular permissions (read contents, write issues/PRs)
- Dependency caching for faster runs
- Artifact retention for 30 days (tests) and 7 days (builds)

### 2. Security Scanning (`security.yml`)
**Purpose**: Security analysis and vulnerability detection

**Triggers**:
- Push to `main` or `develop` branches
- Pull requests to `main` or `develop`
- Weekly schedule (Sundays at midnight)
- Manual trigger

**Jobs**:
- **codeql-analysis**: Static code analysis
  - Runs CodeQL security scanning
  - Uses extended security queries
  - Analyzes Python code

- **dependency-review**: Dependency vulnerability check
  - Only runs on pull requests
  - Fails on moderate or higher severity issues

- **secret-scanning**: Secret detection
  - Uses Gitleaks for secret detection
  - Uploads SARIF results

- **security-audit**: Python dependency audit
  - Uses Safety to check for known vulnerabilities
  - Generates JSON report

**Enhanced Features**:
- Security-events write permission for SARIF uploads
- Multiple scanning tools for comprehensive coverage
- Automated scheduling for regular checks
- Artifact retention for audit trails

### 3. Deployment (`deploy.yml`)
**Purpose**: Application deployment to environments

**Triggers**:
- Push to `main` branch
- Version tags (v*)
- Manual trigger with environment selection

**Jobs**:
- **deploy**: Deploys to specified environment
  - Creates deployment package
  - Runs deployment scripts
  - Performs health checks
  - Uploads deployment artifacts

**Enhanced Features**:
- Environment-specific configurations
- Deployment concurrency control (no cancellation)
- 90-day artifact retention for deployments
- Supports staging and production environments

## Self-Hosted Runner Configuration

### Why Self-Hosted Runners?
1. **Performance**: Faster builds with dedicated hardware
2. **Control**: Full control over runner environment
3. **GPU Access**: Support for ML model operations
4. **Cost**: Reduced GitHub Actions minutes usage
5. **Security**: Keep sensitive data on-premise

### Runner Requirements
- **CPU**: 2+ cores recommended
- **RAM**: 4GB minimum, 8GB recommended
- **Disk**: 20GB+ available space
- **Network**: Stable connection to GitHub
- **OS**: Linux (Ubuntu/Debian), macOS, or Windows

### Setup Instructions
See [RUNNER_SETUP.md](RUNNER_SETUP.md) for detailed instructions.

## Enhanced Workflow Settings

### 1. Concurrency Management
```yaml
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true  # For CI/testing
  cancel-in-progress: false # For deployments
```

**Benefits**:
- Saves runner resources
- Prevents redundant builds
- Faster feedback on latest changes

### 2. Permission Scoping
```yaml
permissions:
  contents: read        # Read repository code
  issues: write         # Comment on issues
  pull-requests: write  # Comment on PRs
  security-events: write # Upload security results
  deployments: write    # Create deployments
```

**Benefits**:
- Principle of least privilege
- Enhanced security
- Clear permission requirements

### 3. Artifact Management
```yaml
- uses: actions/upload-artifact@v4
  with:
    name: descriptive-name
    path: artifact-path/
    retention-days: 30  # Adjust based on needs
```

**Retention Policies**:
- Test results: 30 days
- Build artifacts: 7 days
- Deployment packages: 90 days
- Security reports: 30 days

### 4. Dependency Caching
```yaml
- uses: actions/setup-python@v5
  with:
    python-version: '3.9'
    cache: 'pip'  # Automatic pip cache
```

**Benefits**:
- Faster workflow runs
- Reduced network usage
- Consistent dependency versions

## Workflow Best Practices

### 1. Job Dependencies
Use `needs` to create job dependencies:
```yaml
build:
  needs: test  # Build only runs if test succeeds
```

### 2. Conditional Execution
Skip unnecessary steps:
```yaml
if: github.event_name == 'push'
continue-on-error: true  # For non-critical steps
```

### 3. Error Handling
```yaml
- name: Step with error handling
  run: command || echo "Failed but continuing"
  continue-on-error: true
```

### 4. Status Checks
Always upload results:
```yaml
- name: Upload results
  if: always()  # Run even if previous steps failed
  uses: actions/upload-artifact@v4
```

## Security Considerations

### 1. Secret Management
- Store all secrets in GitHub Secrets
- Never log sensitive information
- Use environment secrets for deployment

### 2. Third-Party Actions
- Pin to specific versions or SHAs
- Review action permissions
- Prefer official/verified actions

### 3. Code Scanning
- Enable CodeQL for all branches
- Review security alerts promptly
- Use dependency review on PRs

### 4. Runner Security
- Isolate self-hosted runners
- Regular security updates
- Monitor runner access logs

## Monitoring and Debugging

### Workflow Monitoring
- Check Actions tab for run status
- Set up notifications for failures
- Review workflow run times

### Debug Mode
Enable via repository secrets:
```
ACTIONS_RUNNER_DEBUG=true
ACTIONS_STEP_DEBUG=true
```

### Log Analysis
- Download workflow logs
- Review artifact uploads
- Check runner diagnostics

## Customization

### Adding New Workflows
1. Create YAML file in `.github/workflows/`
2. Define triggers and jobs
3. Test with workflow_dispatch
4. Document in this file

### Modifying Existing Workflows
1. Test changes on feature branch
2. Review workflow run results
3. Update documentation
4. Merge after approval

### Environment-Specific Settings
Use environment variables:
```yaml
env:
  ENVIRONMENT: ${{ github.event.inputs.environment || 'production' }}
  DEBUG_MODE: false
```

## Troubleshooting

### Common Issues
1. **Runner not picking up jobs**
   - Verify runner is online
   - Check labels match workflow
   - Review runner logs

2. **Permission denied errors**
   - Check token permissions
   - Verify repository settings
   - Review workflow permissions block

3. **Artifact upload failures**
   - Check artifact size limits
   - Verify retention settings
   - Review storage quotas

### Getting Help
- Check GitHub Actions documentation
- Review workflow logs
- Contact repository maintainers
- Open an issue for persistent problems

## Maintenance

### Regular Tasks
- [ ] Update action versions quarterly
- [ ] Review and optimize workflow runs
- [ ] Clean up old artifacts
- [ ] Update runner software
- [ ] Review security scan results

### Performance Review
- Monitor workflow run times
- Identify bottlenecks
- Optimize caching strategies
- Review runner utilization

---

**Last Updated**: February 2026

For questions or suggestions, please open an issue or contact the maintainers.
