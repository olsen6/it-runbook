# Arch Update Failing: PGP "Unknown Trust" and File Conflicts

| Field | Details |
|---|---|
| **Type** | Ticket |
| **Category** | Linux / Package Management |
| **Environment** | Custom PC running Arch Linux (Hyprland) |
| **Status** | Resolved |
| **Tools** | pacman, pacman-key, archlinux-keyring |
| **Skills** | Linux package management, reading error output, GPG/PGP signature troubleshooting, safe use of overrides |

## Problem
After going a long time without updating, a full system update (`sudo pacman -Syu`, about 1,100 packages) failed twice in a row with two different errors. No packages were installed either time.

## Diagnosis
- **Error 1: signature check failed.** Several packages failed with:
  ```
  error: <package>: signature from "<packager>" is unknown trust
  :: File /var/cache/pacman/pkg/<package>.pkg.tar.zst is corrupted (invalid or corrupted package (PGP signature)).
  error: failed to commit transaction (invalid or corrupted package)
  Errors occurred, no packages were upgraded.
  ```
- Deleting the "corrupted" files and running the update again gave the same error, so the files weren't actually the problem.
- **Root cause 1:** my local keyring was out of date. Pacman checks every package's signature against a list of trusted Arch packager keys. Newer packages were signed by keys my old keyring didn't trust yet.
- **Error 2: file conflicts.** Once the keyring was fixed, the update failed again with:
  ```
  error: failed to commit transaction (conflicting files)
  virtualbox-ext-vnc: /usr/lib/virtualbox/ExtensionPacks/VNC/... exists in filesystem
  ```
- Checked who owned the files with `pacman -Qo` → no package owned them.
- **Root cause 2:** leftover files from an older VirtualBox setup were sitting where the new `virtualbox-ext-vnc` package wanted to install. Pacman refuses to overwrite files it doesn't own.

## Resolution
1. Reset pacman's keyring and rebuilt it from the official Arch keys:
   ```
   sudo rm -rf /etc/pacman.d/gnupg      # or mv it to a .bak to keep a backup
   sudo pacman-key --init
   sudo pacman-key --populate archlinux
   ```
2. Updated the keyring package first, then everything else:
   ```
   sudo pacman -Sy archlinux-keyring
   sudo pacman -Su
   ```
3. Confirmed the conflicting files weren't owned by any package:
   ```
   pacman -Qo /usr/lib/virtualbox/ExtensionPacks/VNC/ExtPack.xml
   ```
4. Let pacman overwrite **only that folder**:
   ```
   sudo pacman -Su --overwrite '/usr/lib/virtualbox/ExtensionPacks/VNC/*'
   ```

## Lesson
- Update every 2–3 weeks. Waiting months is what lets the keyring fall behind.
- Before a big update, check archlinux.org/news for manual steps.
- Read the error before acting. Two failures in a row had two completely different causes.
- Only use `--overwrite` on the exact path that conflicts, and only after confirming no package owns it. Never use `--overwrite '*'`.
