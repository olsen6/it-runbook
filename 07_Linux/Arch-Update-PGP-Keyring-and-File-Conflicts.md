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
I went way too long without updating. When I finally ran `sudo pacman -Syu` (about 1,100 packages), it failed twice with two different errors. Nothing got installed either time.

## Diagnosis
- First error, a signature check failure on several packages:
  ```
  error: <package>: signature from "<packager>" is unknown trust
  :: File /var/cache/pacman/pkg/<package>.pkg.tar.zst is corrupted (invalid or corrupted package (PGP signature)).
  error: failed to commit transaction (invalid or corrupted package)
  Errors occurred, no packages were upgraded.
  ```
- Deleting the "corrupted" files and retrying gave the same error, so the files weren't really corrupted.
- The actual cause: my keyring was too old. Pacman checks each package against a list of trusted Arch developer keys, and some packages were signed by developers my old list didn't have yet.
- Second error, after fixing the keyring, a file conflict:
  ```
  error: failed to commit transaction (conflicting files)
  virtualbox-ext-vnc: /usr/lib/virtualbox/ExtensionPacks/VNC/... exists in filesystem
  ```
- `pacman -Qo` showed no package owned those files.
- They were leftovers from an old VirtualBox setup, sitting where the new package wanted to go. Pacman won't overwrite files it doesn't own.

## Resolution
1. Reset the keyring and rebuilt it from the official Arch keys:
   ```
   sudo rm -rf /etc/pacman.d/gnupg      # or mv it to a .bak to keep a backup
   sudo pacman-key --init
   sudo pacman-key --populate archlinux
   ```
2. Updated the keyring first, then everything else:
   ```
   sudo pacman -Sy archlinux-keyring
   sudo pacman -Su
   ```
3. Double-checked that nothing owned the conflicting files:
   ```
   pacman -Qo /usr/lib/virtualbox/ExtensionPacks/VNC/ExtPack.xml
   ```
4. Let pacman overwrite just that one folder:
   ```
   sudo pacman -Su --overwrite '/usr/lib/virtualbox/ExtensionPacks/VNC/*'
   ```

## Lesson
- Update every couple of weeks. Waiting months is how the keyring falls behind.
- Check archlinux.org/news before a big update.
- Actually read the error. These two failures looked similar but had nothing to do with each other.
- Only use `--overwrite` on the exact folder that conflicts, after checking nothing owns it. `--overwrite '*'` is asking for trouble.
