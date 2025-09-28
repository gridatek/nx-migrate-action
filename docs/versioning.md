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
git tag -a "v0.1.0" -m "Release v0.1.0"
git push origin "v0.1.0"
```

## 📈 Version Numbers

Using 0.x.x versions for initial development:

- **v0.0.1** - Initial patches
- **v0.1.0** - New features (pre-1.0)
- **v0.x.x** - Breaking changes allowed

## ✅ What Happens Next

The release workflow automatically:

- Creates GitHub release
- Updates major version tag (v0)
- Generates release notes

Users can then reference:

- `@v0.1.0` - Specific version
- `@v0` - Latest v0.x.x version
