# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- **Simplified validation strategy**: Removed built-in validation, now always creates PRs
- **Repository CI integration**: Leverages existing CI/CD workflows for validation
- **Modern workflow approach**: Standard GitHub review process with re-runnable tests
- **Dev/Prod Mode Support**: New `dev-mode` input for different workflow strategies
  - **Dev Mode**: Creates unique branches with matrix info for comprehensive testing
  - **Prod Mode** (default): Simple branch names with duplicate detection
- **Enhanced yarn version detection**: Improved reliability and proper version parsing
- **Matrix-aware branching**: Automatic matrix variable inclusion in branch names
- **Branch existence checking**: Prevents duplicate migration work in production mode

### Changed

- **Always creates PRs**: All successful migrations now create PRs instead of auto-merging
- **Removed validation inputs**: No longer accepts `validation-commands`, `affected`, or `merge-strategy`
- **Simplified outputs**: Removed `validation-result` output

### Features

- **Multi-package manager support**: npm, yarn, pnpm with proper lockfile handling
- **Version tag selection**: Choose between latest, canary, next, or beta versions
- **Smart Git operations**: Automatic commits, branching, and PR creation
- **Migration audit trail**: Optional preservation of migrations.json files
- **Comprehensive outputs**: Version info, migration status, PR URLs
- **Error resilience**: Graceful handling of failures and edge cases

### Fixed

- **Yarn version detection**: Now correctly displays version numbers instead of CLI messages
- **Branch naming**: Sanitized invalid characters for Git compatibility
- **Matrix conflicts**: Unique branch creation for each matrix combination

---

## Release Notes

This project follows semantic versioning for releases:

- **MAJOR**: Breaking changes that require user workflow updates
- **MINOR**: New features that are backward compatible
- **PATCH**: Bug fixes and improvements

For upgrade instructions and breaking change details, see individual release notes.
