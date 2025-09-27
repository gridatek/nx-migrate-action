# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

nx-migrate-action is a GitHub Action that automatically migrates Nx workspaces to the latest version with validation and smart PR creation. The action is implemented as a composite action using shell scripts.

## Architecture

### Core Components

- **action.yml**: Main action definition with inputs, outputs, and composite steps
- **README.md**: Comprehensive documentation with usage examples and configuration options
- **package.json**: Basic Node.js project metadata (no actual dependencies - action uses npx/composite approach)

### Action Workflow

The action follows this sequence:
1. **Setup** (action.yml:118-149): Node.js setup and dependency installation
2. **Version Check** (action.yml:151-183): Compare current vs latest Nx package version
3. **Migration** (action.yml:184-207): Run `nx migrate latest` and reinstall dependencies
4. **Migrations Execution** (action.yml:209-232): Run any generated migrations from migrations.json
5. **Git Operations** (action.yml:233-265): Commit changes and clean up migration files
6. **Validation** (action.yml:267-304): Run configurable build/test commands with `nx run-many`
7. **Branch Strategy** (action.yml:305-385): Auto-merge on success or create PR on failure

### Key Features

- **Multi-package manager support**: npm, yarn, pnpm with different command patterns
- **Smart branching**: Auto-merge successful migrations, create PRs for failed validations
- **Configurable validation**: Run build, test, lint, e2e with --affected or --all scope
- **Comprehensive outputs**: Version info, migration status, validation results, PR URLs

## Configuration

### Essential Inputs
- `github-token`: Required for PR creation and pushing
- `nx-package`: Target package to migrate (default: nx)
- `package-manager`: npm/yarn/pnpm (default: npm)
- `validation-commands`: Comma-separated commands to run (default: build)
- `node-version`: Node.js version to use (default: 22)

### Behavioral Controls
- `merge-strategy`: auto-merge (push directly to main if validation passes) or always-pr (default: auto-merge)
- `validation-scope`: Use --affected or --all (default: affected)
- `nx-version-tag`: Version tag for updates - latest, beta, canary, next (default: latest)
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
- **Local testing**: Create test workspace with older Nx version to verify migration

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
The action creates detailed PRs with:
- Version change summary
- Migration status
- Validation results
- Proper labeling and targeting
- Fallback to existing PR detection

### Error Handling
- Graceful handling of missing migrations.json
- Continue-on-error for validation with proper output setting
- Fallback mechanisms for PR creation
- Comprehensive logging throughout process