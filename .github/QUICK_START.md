# Quick Start Guide

## Overview
This guide provides quick instructions to get started with the new GitHub Actions workflows and repository configuration.

## 1. Self-Hosted Runner Setup (5 minutes)

### Prerequisites
- Linux/macOS/Windows server
- 2+ CPU cores, 4GB+ RAM
- 20GB+ disk space

### Quick Setup
```bash
# Download runner (get your specific token from GitHub)
mkdir actions-runner && cd actions-runner
curl -o actions-runner-linux-x64-2.311.0.tar.gz -L \
  https://github.com/actions/runner/releases/download/v2.311.0/actions-runner-linux-x64-2.311.0.tar.gz
tar xzf ./actions-runner-linux-x64-2.311.0.tar.gz

# Configure (get token from: Settings → Actions → Runners → New self-hosted runner)
./config.sh --url https://github.com/Kushmanmb/BrainDEAd --token YOUR_TOKEN

# Install as service (recommended)
sudo ./svc.sh install
sudo ./svc.sh start
```

**Full Instructions**: See [.github/RUNNER_SETUP.md](.github/RUNNER_SETUP.md)

## 2. Test Workflows (2 minutes)

### Option A: Manual Trigger
1. Go to **Actions** tab on GitHub
2. Select any workflow (e.g., "CI Pipeline")
3. Click **Run workflow** button
4. Watch the execution

### Option B: Create a Test PR
1. Create a new branch: `git checkout -b test-workflows`
2. Make a small change
3. Create a pull request
4. Workflows will trigger automatically

## 3. Apply Repository Rulesets (10 minutes)

### Via GitHub UI (Recommended)
1. Go to **Settings** → **Rules** → **Rulesets**
2. Click **New ruleset** → **New branch ruleset**
3. Use `.github/rulesets/*.json` files as reference
4. Configure rules through the UI
5. Start in **Evaluate** mode, then activate

**Full Instructions**: See [.github/rulesets/README.md](.github/rulesets/README.md)

## 4. Configure Repository Secrets (if needed)

If your workflows need secrets (API keys, credentials):

1. Go to **Settings** → **Secrets and variables** → **Actions**
2. Click **New repository secret**
3. Add required secrets

## What Each Workflow Does

### CI Pipeline (`ci.yml`)
- **Triggers**: Push/PR to main/develop
- **Actions**: 
  - Runs tests
  - Performs linting
  - Builds application
  - Uploads artifacts

### Security Scanning (`security.yml`)
- **Triggers**: Push/PR to main/develop, weekly schedule
- **Actions**:
  - CodeQL analysis
  - Dependency review
  - Secret scanning
  - Security audit

### Deployment (`deploy.yml`)
- **Triggers**: Push to main, manual trigger
- **Actions**:
  - Creates deployment package
  - Deploys to environment
  - Runs health checks
  - Uploads deployment artifacts

## Files Overview

```
.github/
├── workflows/           # GitHub Actions workflows
│   ├── ci.yml          # CI Pipeline
│   ├── security.yml    # Security Scanning  
│   └── deploy.yml      # Deployment
├── rulesets/           # Repository rulesets
│   ├── *.json         # Ruleset configurations
│   └── README.md      # How to apply rulesets
├── RUNNER_SETUP.md    # Full runner setup guide
├── WORKFLOW_CONFIG.md # Workflow configuration guide
├── IMPLEMENTATION_SUMMARY.md  # Complete implementation details
└── QUICK_START.md     # This file

.gitignore             # Git ignore rules
POLICY.md              # Security policy (ignored by git)
```

## Key Features

### Self-Hosted Runners
✅ Faster builds with dedicated hardware  
✅ Full control over environment  
✅ Support for GPU operations  
✅ Cost-efficient for heavy workloads

### Enhanced Workflows
✅ Concurrency control  
✅ Minimal permissions  
✅ Dependency caching  
✅ Smart artifact retention

### Security
✅ CodeQL scanning  
✅ Dependency review  
✅ Secret detection  
✅ Automated audits

### Repository Protection
✅ Branch protection rules  
✅ Required reviews  
✅ Status checks  
✅ Signed commits

## Troubleshooting

### Runner Not Picking Up Jobs
```bash
# Check runner status
cd actions-runner
./run.sh --check

# View logs
cat _diag/Runner_*.log
```

### Workflow Failures
1. Check **Actions** tab for details
2. Review workflow logs
3. Verify runner is online
4. Check repository secrets

### Permission Errors
1. Verify token permissions in workflow
2. Check repository settings
3. Review workflow permissions block

## Getting Help

- 📖 [Full Documentation](.github/IMPLEMENTATION_SUMMARY.md)
- 🔧 [Runner Setup](.github/RUNNER_SETUP.md)
- ⚙️ [Workflow Configuration](.github/WORKFLOW_CONFIG.md)
- 🛡️ [Rulesets Guide](.github/rulesets/README.md)
- 🔒 [Security Policy](POLICY.md)

## Next Steps

1. ✅ Set up self-hosted runner
2. ✅ Test workflows
3. ✅ Apply rulesets
4. ✅ Configure secrets (if needed)
5. ✅ Train team on new processes
6. ✅ Monitor and iterate

---

**Questions?** Open an issue or contact the repository maintainers.

**Last Updated**: February 2026
