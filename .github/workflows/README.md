# GitHub Actions Workflows

This directory contains automated workflows for building and releasing Claudia.

## Workflows

### 1. Build and Release (`build-and-release.yml`)

**Purpose**: Builds and releases Claudia for all platforms (Linux, macOS, Windows) when a version tag is pushed.

**Triggers**:
- Push of version tags (e.g., `v1.0.0`, `v2.1.3`)
- Manual workflow dispatch

**What it does**:
- Builds on Ubuntu 20.04 (Linux), macOS (universal binary for Intel + Apple Silicon), and Windows
- Creates platform-specific installers:
  - Linux: `.deb` package and `.AppImage`
  - macOS: `.dmg` installer (universal binary)
  - Windows: `.msi` and `.exe` installers
- Creates a GitHub release with all artifacts

**How to use**:
```bash
# Create a version tag and push it
git tag v1.0.0
git push origin v1.0.0
```

### 2. Daily Update and Build (`daily-update-and-build.yml`)

**Purpose**: Automatically fetches upstream changes and creates nightly builds.

**Triggers**:
- Daily at 2 AM UTC
- Manual workflow dispatch

**What it does**:
- Checks for updates from upstream repository (getAsterisk/claudia)
- If changes are detected:
  - Merges upstream changes
  - Builds for all platforms
  - Creates a pre-release with "nightly" tag
- Keeps only the 5 most recent nightly builds
- Handles merge conflicts gracefully

**Configuration**:
- Update `UPSTREAM_REPO` in the workflow file if the upstream repository changes
- The workflow assumes the main branch is called `main` (falls back to `master`)

## Important Notes

1. **Permissions**: These workflows require `contents: write` permission to create releases and push changes.

2. **Secrets**: The workflows use the default `GITHUB_TOKEN` which is automatically provided by GitHub Actions.

3. **Build Times**: 
   - Full builds typically take 15-30 minutes per platform
   - macOS universal builds may take longer due to building for both architectures

4. **Upstream Configuration**: For the daily update workflow, ensure the `UPSTREAM_REPO` environment variable in the workflow file points to the correct upstream repository.

5. **Manual Triggers**: Both workflows can be manually triggered from the Actions tab in your GitHub repository.

## Troubleshooting

### Build Failures

If builds fail, check:
1. Dependency installation logs (especially for Linux)
2. Rust/Cargo build logs
3. Tauri action logs

### Merge Conflicts

If the daily update workflow encounters merge conflicts:
1. The workflow will not fail but will skip the build
2. Manual intervention will be required to resolve conflicts
3. Consider creating a pull request for complex merges

### Release Creation

If release creation fails:
1. Check that the tag format matches the expected pattern
2. Ensure the GitHub token has proper permissions
3. Verify no existing release with the same tag exists