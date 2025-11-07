# Build Scripts

This directory contains build scripts that run during image creation. Scripts are executed in numerical order.

## How It Works

Scripts are named with a number prefix (e.g., `10-build.sh`, `20-build-gnome-extensions.sh`) and run in ascending order during the container build process.

## Included Scripts

- **`10-build.sh`** - Main build script that copies GNOME extensions and schemas
- **`20-build-gnome-extensions.sh`** - Compiles and configures GNOME Shell extensions

## Creating Your Own Scripts

Create numbered scripts for different purposes:

```bash
# 10-build.sh - Base system (already exists)
# 20-build-gnome-extensions.sh - GNOME extensions (already exists)
# 30-custom-packages.sh - Additional packages
# 40-configuration.sh - System configuration
# 50-cleanup.sh - Final cleanup tasks
```

### Script Template

```bash
#!/usr/bin/env bash
set -eoux pipefail

echo "Running custom setup..."
# Your commands here
```

### Best Practices

- **Use descriptive names**: `30-nvidia-drivers.sh` is better than `30-stuff.sh`
- **One purpose per script**: Easier to debug and maintain
- **Clean up after yourself**: Remove temporary files and build dependencies
- **Test incrementally**: Add one script at a time and test builds
- **Comment your code**: Future you will thank present you
- **Use dnf5**: Always use `dnf5` for package management, not `dnf` or `yum`
- **Non-interactive installs**: Always use `-y` flag with dnf5

### Disabling Scripts

To temporarily disable a script without deleting it:
- Rename it with `.disabled` extension: `20-script.sh.disabled`
- Or remove execute permission: `chmod -x build/20-script.sh`

## Execution Order

The Containerfile runs all numbered scripts automatically:

```dockerfile
RUN find /ctx/build -type f -name "*.sh" ! -name "*.example" -executable | sort | xargs -I {} bash {}
```

Scripts are executed in alphabetical/numerical order. The system automatically:
- Finds all `.sh` files
- Excludes `.example` and non-executable files
- Sorts them
- Executes each in order

## Notes

- Scripts run as root during build
- Build context is available at `/ctx`
- Use dnf5 for package management (required for bootc)
- Always use `-y` flag for non-interactive installs
- Extensions are built from git submodules in `usr/share/gnome-shell/extensions/`
