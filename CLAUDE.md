# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

nx-migrate-action is a GitHub Action that automatically migrates Nx workspaces to the latest version and creates PRs for review and validation. Optional auto-merge workflow available for seamless CI integration. The action is implemented as a composite action using shell scripts.

## Common Development Tasks

### Linting and Validation

- **YAML linting**: `yamllint action.yml` (uses .yamllint.yml config with 120 char line length)
- **Action validation**: Structural validation is done via GitHub Actions workflow test.yml
- **Code formatting**: `npx prettier --write --ignore-unknown .` (configured via lint-staged)

### Testing

- **Functional testing**: Run via `.github/workflows/test.yml` with matrix testing
- **Manual testing**: Use `dev-mode: 'true'` in action inputs to create unique test branches
- **Local validation**: Test action changes using workflow_dispatch trigger

### Git Hooks and Automation

- **Husky**: Pre-commit hooks configured via `npm run prepare`
- **Lint-staged**: Automatic formatting on commit using Prettier
- **Git configuration**: Action uses dedicated bot credentials for automated commits

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
- **Optional auto-merge**: Separate workflow can auto-merge PRs after CI validation passes
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

### Test Commands and Workflow

Testing is primarily done via GitHub Actions workflow with minimal local scripts:

- **YAML linting**: `yamllint action.yml` (uses .yamllint.yml config with 120 char line length)
- **Action validation**: Basic structural validation of action.yml (checks for required fields)
- **Functional testing**: Matrix testing across Node.js versions (22, 24) and package managers (npm, yarn, pnpm)
- **Migration testing**: Creates temporary test workspace with older Nx version to verify migration and PR creation
- **Dev mode testing**: Use `dev-mode: 'true'` to create unique branch names for parallel matrix testing
- **Available npm scripts**:
  - `npm run prepare` - Initializes Husky git hooks
  - Prettier formatting handled automatically via lint-staged on commits

## Development Guidelines

### Action Development

- **Single file architecture**: All logic is in action.yml as composite shell steps (no separate JavaScript files)
- **Shell scripting patterns**: Use proper error handling and exit codes
- **Package manager support**: Use `case` statements for npm/yarn/pnpm branching
- **GitHub Actions integration**: Use `echo "key=value" >> $GITHUB_OUTPUT` format for outputs
- **Environment variables**: Action relies on `GITHUB_TOKEN` environment variable for authentication
- **Input validation**: Handle edge cases (no changes, missing files, validation failures)
- **Node.js requirement**: Action requires Node.js setup (actions/setup-node@v4) before execution

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
- **Optional auto-merge**: Separate workflow can automatically merge PRs after CI validation passes
- **Modern approach**: Leverages existing repository CI/CD for validation
- **Better developer experience**: Standard GitHub review process with re-runnable tests

### Error Handling

- **Migration validation**: Graceful handling of missing migrations.json files
- **PR creation fallbacks**: Multiple fallback mechanisms for PR creation failures (retry without labels, check for existing PRs)
- **Version detection**: Validation of version strings and fallback to current version if invalid
- **Branch management**: Automatic cleanup of existing branches in dev mode, duplicate branch detection in prod mode
- **Comprehensive logging**: Detailed debug output throughout the migration process
- **Permission handling**: Graceful degradation when GitHub token lacks certain permissions

## Important Files and Patterns

### Core Files

- **action.yml**: Complete action definition (559 lines) - contains all business logic
- **package.json**: Minimal Node.js metadata, dev dependencies for formatting (husky, prettier, lint-staged)
- **.yamllint.yml**: YAML linting configuration with 120 character line length limit
- **.github/workflows/test.yml**: Comprehensive testing workflow with matrix strategy

### Key Input Parameters

- **dev-mode**: Creates unique branches for testing vs simple production branches
- **skip-initial-install**: Useful when dependencies are pre-installed in testing scenarios
- **push-migrations-json**: Controls whether to keep migration files for audit trail
- **create-missing-labels**: Automatic label creation with predefined colors and descriptions
