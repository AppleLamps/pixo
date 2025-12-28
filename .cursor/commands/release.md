# Release New Version

Prepare, commit, tag, and publish a new crate release. The version number will be provided as a parameter (e.g., `/release 0.5.0`).

## Steps

### 1. Gather commits since last release

Find the last version tag or commit by searching git history. List all commits since that point:

```bash
git log --oneline <last-version-tag>..HEAD
```

### 2. Update CHANGELOG.md

Add a new version section at the top (below the header), following the existing format:

```markdown
## [X.X.X] - YYYY-MM-DD

### Added
- New features...

### Changed
- Changes to existing functionality...

### Fixed
- Bug fixes...

### Removed
- Removed features...
```

Only include sections that have entries. Write clear, user-facing descriptions based on the commits. Group related changes together.

### 3. Update Cargo.toml

Change the `version` field to the new version:

```toml
version = "X.X.X"
```

### 4. Update the lockfile

**IMPORTANT:** Always run cargo to regenerate `Cargo.lock` with the new version:

```bash
cargo check
```

This updates the version in Cargo.lock. Verify it's modified with `git status`.

### 5. Format and lint

Format code and verify it passes CI checks:

```bash
cargo fmt && cargo clippy --all-targets --all-features -- -D warnings
```

If clippy reports warnings, fix them before proceeding.

### 6. Commit and tag the release

Create a release commit and tag:

```bash
git add Cargo.toml Cargo.lock CHANGELOG.md
git commit -m "Release X.X.X"
git tag -a vX.X.X -m "Release X.X.X"
```

### 7. Push to remote

Push the commit and tag to the remote repository:

```bash
git push origin main --tags
```

### 8. Publish to crates.io

Publish the crate:

```bash
cargo publish
```

Wait for the publish to complete and verify success.

### 9. Build and update WASM

Rebuild the WASM bindings with the new version:

```bash
node web/scripts/build-wasm.mjs
```

Then commit the updated WASM files:

```bash
git add web/src/lib/pixo-wasm/
git commit -m "Update WASM bindings for X.X.X"
git push origin main
```

## Notes

- The version parameter is required (e.g., `/release 0.5.0`)
- Use today's date for the changelog entry
- Follow semantic versioning: MAJOR.MINOR.PATCH
- Breaking changes should be clearly marked with **BREAKING:**
- All steps should be executed automatically - don't just remind the user
