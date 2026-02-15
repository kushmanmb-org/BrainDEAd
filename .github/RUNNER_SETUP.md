# GitHub Actions Configuration

## Self-Hosted Runner Setup

### Prerequisites
- Linux/macOS/Windows server with Docker support
- Minimum 2 CPU cores, 4GB RAM
- 20GB available disk space
- Network access to GitHub.com

### Installation Steps

1. **Download the Runner**
   ```bash
   # Navigate to repository Settings → Actions → Runners → New self-hosted runner
   # Follow the provided commands to download the runner package
   mkdir actions-runner && cd actions-runner
   curl -o actions-runner-linux-x64-2.311.0.tar.gz -L https://github.com/actions/runner/releases/download/v2.311.0/actions-runner-linux-x64-2.311.0.tar.gz
   tar xzf ./actions-runner-linux-x64-2.311.0.tar.gz
   ```

2. **Configure the Runner**
   ```bash
   # Create the runner and start the configuration experience
   ./config.sh --url https://github.com/OWNER/REPO --token YOUR_TOKEN
   
   # Follow prompts:
   # - Enter runner name (e.g., "brain-tumor-runner-1")
   # - Enter runner group (press Enter for default)
   # - Enter labels (e.g., "self-hosted,linux,brain-tumor")
   # - Enter work folder (press Enter for default)
   ```

3. **Install as a Service (Optional but Recommended)**
   ```bash
   sudo ./svc.sh install
   sudo ./svc.sh start
   ```

### Runner Labels
Configure runners with appropriate labels:
- `self-hosted` - Default label for all self-hosted runners
- `linux` / `windows` / `macos` - Operating system
- `gpu` - If GPU is available for model training
- `production` / `staging` - Environment-specific runners

### Runner Groups (For Organizations)
Create runner groups for better management:
- `production-runners` - For production deployments
- `ci-runners` - For CI/CD workflows
- `security-runners` - For security scanning

## Enhanced Workflow Features

### 1. Concurrency Control
Prevents multiple workflow runs on the same branch:
```yaml
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true
```

### 2. Granular Permissions
Follows principle of least privilege:
```yaml
permissions:
  contents: read
  issues: write
  pull-requests: write
  security-events: write
```

### 3. Artifact Management
Efficient artifact storage:
```yaml
- uses: actions/upload-artifact@v4
  with:
    name: build-artifacts
    path: dist/
    retention-days: 30
```

### 4. Caching
Speed up workflows with caching:
```yaml
- uses: actions/setup-python@v5
  with:
    python-version: '3.9'
    cache: 'pip'
```

### 5. Matrix Builds
Test across multiple configurations:
```yaml
strategy:
  matrix:
    python-version: ['3.8', '3.9', '3.10']
    os: [ubuntu-latest, self-hosted]
```

## Workflow Security Best Practices

### 1. Token Permissions
- Use `GITHUB_TOKEN` with minimal permissions
- Never use personal access tokens in workflows
- Use repository secrets for sensitive data

### 2. Third-Party Actions
- Pin actions to specific SHAs for security
- Review action source code before use
- Use only verified/official actions when possible

### 3. Secret Management
- Store secrets in GitHub Secrets
- Never log secrets or sensitive data
- Use environment-specific secrets

### 4. Input Validation
- Validate all workflow inputs
- Sanitize user-provided data
- Use allow-lists for dynamic values

## Monitoring and Maintenance

### Runner Health Checks
```bash
# Check runner status
./run.sh --check

# View runner logs
cat _diag/Runner_*.log
```

### Workflow Monitoring
- Monitor workflow run times
- Review failed workflows regularly
- Set up notifications for failures

### Resource Management
- Monitor disk space usage
- Clean up old artifacts
- Rotate runner logs

## Troubleshooting

### Common Issues

1. **Runner Offline**
   - Check network connectivity
   - Verify runner service is running
   - Check authentication token

2. **Workflow Failures**
   - Review workflow logs
   - Check required secrets are set
   - Verify runner has required tools

3. **Permission Errors**
   - Verify token permissions
   - Check repository settings
   - Review workflow permissions block

### Debug Mode
Enable debug logging:
```bash
# Set repository secrets:
ACTIONS_RUNNER_DEBUG=true
ACTIONS_STEP_DEBUG=true
```

## Advanced Configuration

### Custom Environment Variables
```yaml
env:
  PYTHON_VERSION: '3.9'
  NODE_VERSION: '18'
  DOCKER_BUILDKIT: 1
```

### Conditional Execution
```yaml
if: github.event_name == 'push' && github.ref == 'refs/heads/main'
```

### Composite Actions
Create reusable workflow steps in `.github/actions/`

### Workflow Templates
Create organization-wide templates in `.github/workflow-templates/`

## Performance Optimization

1. **Parallel Jobs**: Run independent jobs in parallel
2. **Caching**: Cache dependencies and build outputs
3. **Conditional Steps**: Skip unnecessary steps
4. **Artifact Compression**: Compress large artifacts
5. **Runner Pools**: Maintain pool of warm runners

## Compliance and Auditing

- Review workflow runs in Actions tab
- Export workflow logs for compliance
- Monitor runner access patterns
- Regular security audits of workflows

## Resources

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Self-hosted Runners Guide](https://docs.github.com/en/actions/hosting-your-own-runners)
- [Workflow Syntax Reference](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions)
- [Security Hardening Guide](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions)

---

**Last Updated**: February 2026
