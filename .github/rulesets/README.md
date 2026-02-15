# Repository Rulesets Configuration

This directory contains JSON configuration files for GitHub Repository Rulesets. These rulesets define branch protection rules and policies for the repository.

## Available Rulesets

### 1. Main Branch Protection (`main-branch-protection.json`)
Protects the `main` branch with strict rules:
- **Pull Request Required**: Requires 1 approving review
- **Required Status Checks**: 
  - CI Pipeline tests must pass
  - Build must succeed
  - Security scanning must complete
- **Branch Protection**:
  - Prevents deletion
  - Requires linear history
  - Requires signed commits
  - Prevents force pushes

### 2. Development Branch Protection (`develop-branch-protection.json`)
Protects `develop` and `release/*` branches with moderate rules:
- **Pull Request Required**: Requires 1 approving review
- **Required Status Checks**: CI Pipeline tests must pass
- **Branch Protection**: Prevents deletion

### 3. Tag Protection (`tag-protection.json`)
Protects version tags (`v*` pattern):
- Prevents unauthorized tag creation
- Prevents tag updates
- Prevents tag deletion
- Requires signed tags

## How to Apply These Rulesets

### Option 1: Using GitHub UI

1. Go to your repository on GitHub
2. Navigate to **Settings** → **Rules** → **Rulesets**
3. Click **New ruleset** → **New branch ruleset** (or tag ruleset)
4. Use the JSON configurations provided as a reference
5. Configure the rules through the UI
6. Save and activate the ruleset

### Option 2: Using GitHub API

You can apply these rulesets using the GitHub REST API:

```bash
# Example for creating a branch ruleset
curl -X POST \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  https://api.github.com/repos/OWNER/REPO/rulesets \
  -d @main-branch-protection.json
```

### Option 3: Using GitHub CLI

```bash
# Using gh CLI (requires manual configuration)
gh api \
  --method POST \
  -H "Accept: application/vnd.github+json" \
  /repos/OWNER/REPO/rulesets \
  --input main-branch-protection.json
```

## Ruleset Details

### Enforcement Levels
- **active**: Rules are enforced for all users except bypass actors
- **evaluate**: Rules are evaluated but not enforced (testing mode)
- **disabled**: Rules are not evaluated or enforced

### Bypass Actors
Bypass actors can be:
- **Repository roles**: Admin, Maintain, Write, Triage, Read
- **Teams**: Specific GitHub teams
- **Apps**: GitHub Apps with appropriate permissions
- **Organization roles**: Custom organization roles

### Actor ID Reference
- `5` = Repository Admin role
- You can find other actor IDs through the GitHub API

## Customization

To customize these rulesets for your needs:

1. Edit the JSON files to add/remove rules
2. Modify the `conditions.ref_name` to target different branches/tags
3. Adjust `required_approving_review_count` based on team size
4. Add/remove required status checks based on your CI/CD pipeline
5. Configure bypass actors based on your team structure

## Best Practices

1. **Start with Evaluate Mode**: Test rulesets in evaluate mode before activating
2. **Gradual Rollout**: Apply strict rules to main first, then other branches
3. **Team Communication**: Inform team members before activating new rules
4. **Regular Review**: Review and update rulesets quarterly
5. **Documentation**: Keep this README updated with any changes

## Monitoring and Compliance

- Review ruleset bypass events regularly in repository settings
- Monitor failed status checks and blocked pull requests
- Adjust rules based on team feedback and compliance requirements

## Support and Questions

For questions about these rulesets:
1. Check the [GitHub Rulesets documentation](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets)
2. Open an issue in this repository
3. Contact the repository maintainers

---

**Note**: These configurations are templates. Actual implementation may require adjustments based on your repository permissions and GitHub plan features.
