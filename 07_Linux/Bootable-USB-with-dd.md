# Bootable USB with dd Quick Reference

| Field | Details |
|---|---|
| **Type** | Quick Reference |
| **Category** | Linux CLI / OS Installation |
| **Applies To** | Any Linux system; any bootable ISO (used here for the Proxmox VE 9.2 installer) |
| **Skills** | Linux CLI, file integrity verification, disk identification |

## When to Use This
Making a bootable USB from an ISO on Linux, no extra apps needed. I used this for my Proxmox installer.

## Commands / Settings

| Command or Setting | What It Does | Example |
|---|---|---|
| `sha256sum <file>` | Creates a fingerprint of the file. Compare it to the one on the download page to confirm the ISO isn't corrupted or tampered with. | `sha256sum ~/Downloads/proxmox-ve_9.2-1.iso` |
| `lsblk` | Lists every drive and partition. Use it to find the USB by size; `RM = 1` means removable. | `lsblk` |
| `sudo dd if=<iso> of=<drive> ...` | Copies the ISO byte for byte onto the USB. Erases everything on the target drive. | `sudo dd if=~/Downloads/proxmox-ve_9.2-1.iso of=/dev/sda bs=4M status=progress oflag=sync` |
| `if=` | **Input file:** what you're copying from (the ISO) | `if=~/Downloads/proxmox-ve_9.2-1.iso` |
| `of=` | **Output file:** what you're writing to (the USB) | `of=/dev/sda` |
| `bs=4M` | Copies in 4 MB chunks, much faster than the default | `bs=4M` |
| `status=progress` | Shows progress while it runs | `status=progress` |
| `oflag=sync` | Makes sure everything is physically written before it reports done | `oflag=sync` |
| `sync` | Flushes any remaining writes. Optional extra safety before unplugging. | `sync` |

## Notes
- Use the whole disk (`/dev/sda`), not a partition (`/dev/sda1`).
- Check `of=` more than once. `dd` doesn't ask for confirmation, and pointing it at the wrong drive wipes that drive. People call it "disk destroyer" for a reason.
- You don't need to format the USB first.
- Wait for the prompt to come back before pulling the USB.
