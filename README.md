# Nx Migration Action

Automatically migrate your Nx workspace to the latest version with smart validation and PR creation.

## Features

- 🔄 **Automatic Updates**: Checks for and applies Nx updates automatically
- 🛠️ **Migration Handling**: Runs Nx migrations when available
- ✅ **Validation**: Runs configurable validation commands (build, test, etc.)
- 🤖 **Smart Branching**: Auto-merge on success, create PR on failure
- 📦 **Multi Package Manager**: Supports npm, yarn, and pnpm
- ⚙️ **Highly Configurable**: Customize every aspect of the migration process
- 🏷️ **Auto Labeling**: Automatically labels PRs for easy organization

## Quick Start

### Basic Usage

```yaml
name: Nx Migration
on:
  workflow_dispatch:
  schedule:
    - cron: '0 1 * * *'  # Daily at 1 AM

jobs:
  nx-migrate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
          
      - uses: gridatek/nx-migrate-action@v0
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
```

### Advanced Configuration

```yaml
name: Nx Migration
on:
  workflow_dispatch:
  schedule:
    - cron: '0 1 * * 1'  # Weekly on Mondays

jobs:
  nx-migrate:
    runs-on: ubuntu-latest
    permissions:
      contents: write
      pull-requests: write
      
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
          
      - uses: gridatek/nx-migrate-action@v0
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          node-version: '18'
          package-manager: 'pnpm'
          validation-commands: 'build,test,lint,e2e'
          validation-scope: 'all'
          validation-and-merge-strategy: 'run-and-always-pr'
          pr-labels: 'dependencies,nx-migration,high-priority'
          target-branch: 'develop'
```

## Configuration Options

| Input | Description                                  | Default | Required |
|-------|----------------------------------------------|---------|----------|
| `github-token` | GitHub token for creating PRs and pushing    | `${{ github.token }}` | Yes |
| `nx-package` | The Nx package to check for updates          | `@nx/workspace` | No |
| `nx-version-tag` | Nx version tag to use (latest, canary, next) | `latest` | No |
| `node-version` | Node.js version to use                       | `22` | No |
| `package-manager` | Package manager (npm, yarn, pnpm)            | `npm` | No |
| `validation-commands` | Validation commands (comma-separated)        | `build,test` | No |
| `validation-scope` | Validation scope (all, affected)             | `affected` | No |
| `validation-and-merge-strategy` | Strategy for validation and merging (run-and-auto-merge, run-and-always-pr, skip-and-always-pr) | `run-and-auto-merge` | No |
| `pr-labels` | PR labels (comma-separated)                  | `dependencies,nx-migration,automated` | No |
| `commit-message-prefix` | Commit message prefix                        | `build` | No |
| `target-branch` | Target branch for changes                    | `main` | No |
| `working-directory` | Working directory                            | `.` | No |
| `push-migrations-json` | Push migrations.json to repository after successful migration | `false` | No |

## Outputs

| Output | Description |
|--------|-------------|
| `updated` | Whether Nx was updated |
| `current-version` | Current Nx version before update |
| `latest-version` | Latest Nx version |
| `has-migrations` | Whether migrations were found |
| `validation-result` | Result of validation tests |
| `pr-url` | URL of created PR (if any) |

## Usage Examples

### With Different Package Managers

#### npm (default)
```yaml
- uses: gridatek/nx-migrate-action@v0
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
```

#### Yarn
```yaml
- uses: gridatek/nx-migrate-action@v0
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
    package-manager: 'yarn'
```

#### pnpm
```yaml
- uses: gridatek/nx-migrate-action@v0
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
    package-manager: 'pnpm'
```

### Conservative Approach (Always Create PRs)

```yaml
- uses: gridatek/nx-migrate-action@v0
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
    validation-and-merge-strategy: 'run-and-always-pr'
    validation-commands: 'build,test,lint'
```

### Skip Validation (Fast Updates)

```yaml
- uses: gridatek/nx-migrate-action@v0
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
    validation-and-merge-strategy: 'skip-and-always-pr'
```

### Multiple Nx Packages

You can run the action multiple times for different Nx packages:

```yaml
steps:
  - uses: actions/checkout@v4
    with:
      fetch-depth: 0
      
  - uses: gridatek/nx-migrate-action@v0
    with:
      github-token: ${{ secrets.GITHUB_TOKEN }}
      nx-package: '@nx/workspace'
      
  - uses: gridatek/nx-migrate-action@v0
    with:
      github-token: ${{ secrets.GITHUB_TOKEN }}
      nx-package: '@nx/angular'
```

## How It Works

1. **Check for Updates**: Compares current Nx version with latest available
2. **Apply Updates**: Runs `nx migrate latest` if update is available
3. **Run Migrations**: Executes any migrations found in `migrations.json`
4. **Validate Changes**: Runs specified validation commands
5. **Smart Branching**:
    - ✅ **Success**: Push directly to target branch (if enabled)
    - ❌ **Failure**: Create PR for manual review

## Workflow Strategies

### Daily Updates (Aggressive)
```yaml
on:
  schedule:
    - cron: '0 2 * * *'  # Daily at 2 AM
```

### Weekly Updates (Balanced)
```yaml
on:
  schedule:
    - cron: '0 2 * * 1'  # Monday at 2 AM
```

### Monthly Updates (Conservative)
```yaml
on:
  schedule:
    - cron: '0 2 1 * *'  # First day of month at 2 AM
```

## Permissions

Your workflow needs these permissions:

```yaml
permissions:
  contents: write      # To push commits
  pull-requests: write # To create PRs
```

## Security Considerations

- The action uses the provided GitHub token for authentication
- All operations respect your repository's branch protection rules
- No sensitive data is logged or exposed
- Commits are signed with a dedicated bot account

## Troubleshooting

### Common Issues

**"No changes to commit"**
- This is normal when Nx is already up to date

**"Validation failed"**
- Check the workflow logs for specific test failures
- The action will create a PR for manual review

**"Permission denied"**
- Ensure your workflow has `contents: write` and `pull-requests: write` permissions

**"Package manager not found"**
- Verify your package manager is correctly specified and available

### Debug Mode

Enable verbose logging:

```yaml
- uses: gridatek/nx-migrate-action@v0
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
  env:
    ACTIONS_STEP_DEBUG: true
```

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests if applicable
5. Submit a pull request

## License

MIT License - see [LICENSE](LICENSE) file for details.

## Changelog

See [CHANGELOG.md](CHANGELOG.md) for version history and updates.