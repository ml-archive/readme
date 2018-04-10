# How to release a version

When we release a package, we try to align the format and naming of these releases to make it easier to work and use our different packages. This document describes how we create a release on GitHub.

## Tagging a version

We use semantic versioning for tagging our releases. See the [official docs](https://semver.org/) for more information. Unless we have branches that point to different versions of e.g. Vapor, we usually tag from the `master` branch. If the version is not stable yet (e.g. a beta release) we mark it as a "Pre-release".

## Creating release notes

### Title

Our release title follows our version tag. Some examples:

- `0.0.1` becomes "Version 0.0.1"
- `1.1.0-beta.1` becomes "Version 1.1.0 Beta 1"
- `2.0.0` could become "Version 2.0.0"

### Body

We use the headlines and the order from [Keep a Changelog](https://keepachangelog.com/en/1.0.0/) for describing our release. We use `h3` tags for the different headlines and we use bullets for describing the different changes within each headline. E.g:

```
### Fixed
- Some bug which causes this and this
```
