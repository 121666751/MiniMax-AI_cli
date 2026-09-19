# Releasing mmx-cli

Releases are tag-driven. The repository's `package.json` always uses the
development placeholder version `0.0.0-dev`; do not change it for a release.

## Prerequisites

- The commit to release is on `main`.
- CI has passed for that commit.
- The release version is a new SemVer version, for example `1.0.23`.
- npm trusted publishing is configured for this repository and its
  `.github/workflows/release.yml` workflow. Publishing uses GitHub Actions OIDC;
  the workflow does not use an `NPM_TOKEN` secret.

## Publish a release

Create an annotated `v`-prefixed tag on the commit to release and push it:

```bash
git switch main
git pull --ff-only
git tag -a v1.0.23 -m "Release 1.0.23"
git push origin v1.0.23
```

The `Release` GitHub Actions workflow validates the tag, derives `1.0.23` as
the release version, builds the CLI with that version, and creates a temporary
npm tarball whose `package.json` has the matching version. It then creates a
GitHub Release with automatically generated notes and publishes that tarball to npm.
Review the release notes against all changes since the previous release and add
a user-facing summary of new features and fixes.

## Verify

```bash
gh run list --workflow release.yml --branch v1.0.23 --limit 1
npm view mmx-cli@1.0.23 version
```

The npm version and `mmx --version` from the published package should both
match the tag without its `v` prefix.

## Failed releases

Do not move or reuse an existing tag. npm versions are immutable.

After fixing the issue, publish a new patch version with a new tag. If the
failure occurred after the GitHub Release was created but before npm publish,
use a new version as well; the original release can be marked as a prerelease
or deleted according to the repository's release policy.
