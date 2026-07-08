# DOCNOTE: Behavioral and Dependency Updates

## 1. Safety and Isolation
- **Subshell Wrapper**: The execution of the `osxiso` function is now fully encapsulated within a subshell `( ... )`. Sourcing this script into an active shell session will no longer pollute the user's terminal environment with temporary variables, custom functions, trap configurations, or strict shell flags (`set -e`).
- **Safe Mode Execution**: Safe execution flags (`set -Eeuo pipefail`) are active inside the subshell to immediately halt execution on errors, unassigned variables, or pipeline failures.

## 2. Dynamic Space Allocation (New Behavior)
- **Automatic Calculation**: The hardcoded 20GB and 8GB limits for macOS installer images have been replaced with a dynamic size computation. The script calculates the installer app size (`du -sk`) and appends a 2GB buffer.
- This ensures compatibility with future large macOS installers without requiring modifications to the code.

## 3. Storage Changes
- **Temporary Working Directory**: Instead of building inside `$PWD/temp`, the script now creates a secure, random temporary directory via `mktemp -d` inside the system's temporary paths (`/var/folders/...` or `/tmp`).
- All build files are cleaned up automatically upon exit (on both success and failure).

## 4. Administrative Privileges (Sudo)
- **Build v1 (Legacy macOS)**: Commands modifying files on the restored volume (`/Volumes/OS X Base System`) now run with `sudo` to prevent permission errors.
- **Build v2 (Modern macOS)**: The `createinstallmedia` tool continues to require administrative privileges and will prompt for the `sudo` password as usual.

## 5. OS Auto-Detection
- The script automatically scans `/Applications/` for any files matching `Install*.app`. Any installer discovered (even if not explicitly listed in the predefined array) is added to the selection menu and will default to the modern creation method (`build_v2`).
