# Changelog

All notable changes to the `osx-iso` project will be documented in this file.

## [6.1.0] - 2026-07-08

### Added
- Added shell safety modes (`set -Eeuo pipefail`) inside the isolated subshell environment.
- Added platform compatibility check (`Darwin` / macOS only) to prevent accidental execution on unsupported OSes.
- Added ShellCheck directives to bypass false positives for dynamically-called trap handlers (`SC2329`).
- Added color output utility using `tput` with automatic fallback to plain text if run in non-TTY environments.
- Added automated scanning of `/Applications` to detect installer applications not explicitly predefined.

### Fixed
- Fixed critical scope leakage of shell variables, settings, and traps by isolating the function's execution into a subshell wrapper.
- Fixed a bug where `prep_build` inside `build_v1` did not receive the OS name argument (`$1`), failing to clean up previous failed builds.
- Fixed a bug where `cleanup` was invoked with missing arguments in success paths, causing detached volumes to remain mounted.
- Fixed permission errors in `build_v1` (legacy OSes) by ensuring `asr`, `rm`, and `cp` are run with `sudo` where root privileges are required to modify OS X base system volumes.
- Fixed potential stack overflow and interactive loops in `choose` by eliminating recursive calls and utilizing proper loop continuation.
- Fixed redundant `cleanup` invocations by utilizing the `EXIT` trap to handle cleanup automatically in both success and failure cases.

### Changed
- Replaced hardcoded partition size estimation (`apps_large` and fixed values) with dynamic calculation (`du -sk`) and a 2GB safety buffer to prevent disk space issues on modern macOS installers.
- Removed legacy, hardcoded pipe-delimited string parsing (`apps_v1` and `apps_v2`) in favor of native bash `case` statements.
- Replaced fixed directory temporary storage (`$PWD/temp`) with secure, OS-level unique temporary directories (`mktemp -d`).
