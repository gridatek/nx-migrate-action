# 🚀 Creating a Release

Simple guide to create releases for nx-migrate-action.

## 📋 Prerequisites

- All changes merged to `main` branch
- Tests passing ✅

## 🎯 Create Release

```bash
# 1. Checkout main and pull latest
git checkout main
git pull origin main

# 2. Create and push version tag
git tag -a "v1.0.0" -m "Release v1.0.0"
git push origin "v1.0.0"
```

## 📈 Version Numbers

Follow [Semantic Versioning](https://semver.org/):
- **v1.0.1** - Bug fixes
- **v1.1.0** - New features
- **v2.0.0** - Breaking changes

## ✅ What Happens Next

The release workflow automatically:
- Creates GitHub release
- Updates major version tag (v1, v2, etc.)
- Generates release notes

Users can then reference:
- `@v1.0.0` - Specific version
- `@v1` - Latest v1.x.x version