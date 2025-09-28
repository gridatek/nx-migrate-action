# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

nx-migrate-action is a GitHub Action that automatically migrates Nx workspaces to the latest version and creates PRs for review and validation. The action is implemented as a composite action using shell scripts.

## Architecture

### Core Components

- **action.yml**: Main action definition with inputs, outputs, and composite steps
- **README.md**: Comprehensive documentation with usage examples and configuration options
- **package.json**: Basic Node.js project metadata (no actual dependencies - action uses npx/composite approach)

### Action Workflow

The action follows this sequence:

1. **Setup**: Node.js setup and dependency installation
2. **Version Check**: Compare current vs latest Nx package version
3. **Migration**: Run `nx migrate latest` and reinstall dependencies
4. **Migrations Execution**: Run any generated migrations from migrations.json
5. **Git Operations**: Commit changes and clean up migration files
6. **PR Creation**: Always create a PR for review and validation by repository CI/CD

### Key Features

- **Multi-package manager support**: npm, yarn, pnpm with different command patterns
- **Always creates PRs**: All migrations create PRs for proper review and CI validation
- **Clean migration process**: Handles dependency updates and migration execution
- **Comprehensive outputs**: Version info, migration status, PR URLs

## Configuration

### Essential Inputs

- `nx-package`: Target package to migrate (default: nx)
- `package-manager`: npm/yarn/pnpm (default: npm)
- `node-version`: Node.js version to use (default: 22)
- `nx-version-tag`: Version tag for updates - latest, beta, canary, next (default: latest)

### PR Configuration

- `pr-labels`: Labels to add to PRs (default: nx-migrate-action)
- `target-branch`: Target branch for PRs (default: main)
- `push-migrations-json`: Keep migrations.json for audit trail (default: false)
- `create-missing-labels`: Auto-create PR labels if missing (default: true)

## Testing

The action includes comprehensive testing via `.github/workflows/test.yml`:

- Matrix testing across package managers (npm, yarn, pnpm)
- Matrix testing across Node.js versions (22, 24)
- Default Node.js version is set to LTS (v22) for stability
- Creates temporary Nx workspace with older version for testing upgrades
- YAML linting via yamllint with custom configuration (.yamllint.yml)

### Test Commands

No package.json scripts are configured. Testing is done via GitHub Actions workflow:

- **YAML linting**: `yamllint action.yml` (uses .yamllint.yml config)
- **Action validation**: Basic structural validation of action.yml
- **Functional testing**: Matrix testing across Node.js versions (22, 24) and package managers
- **Migration testing**: Create test workspace with older Nx version to verify migration and PR creation

## Development Guidelines

### Action Development

- All logic is in action.yml as composite shell steps
- Use proper shell scripting with error handling
- Support multiple package managers with case statements
- Use GitHub Actions output format: `echo "key=value" >> $GITHUB_OUTPUT`
- Handle edge cases (no changes, missing files, validation failures)

### Shell Script Patterns

- Use `case` statements for package manager branching
- Parse JSON with `jq` for package version detection
- Use `continue-on-error: true` for validation steps
- Proper Git configuration for automated commits
- Branch naming with run IDs to avoid conflicts

### PR Creation Logic

The action always creates detailed PRs with:

- Version change summary
- Migration status and changes
- Proper labeling and targeting
- Fallback to existing PR detection

### Validation Strategy

- **No built-in validation**: Action only performs migration and creates PR
- **Repository CI handles validation**: Tests, builds, and lints run via PR workflows
- **Modern approach**: Leverages existing repository CI/CD for validation
- **Better developer experience**: Standard GitHub review process with re-runnable tests

### Error Handling

- Graceful handling of missing migrations.json
- Fallback mechanisms for PR creation
- Comprehensive logging throughout migration process
