# Bootable USB with dd Quick Reference

| Field | Details |
|---|---|
| **Type** | Quick Reference |
| **Category** | Linux CLI / OS Installation |
| **Applies To** | Any Linux system; any bootable ISO (used here for the Proxmox VE 9.2 installer) |
| **Skills** | Linux CLI, file integrity verification, disk identification |

## When to Use This
Turning an ISO into a bootable USB installer from Linux, without extra software.

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
- **Write to the whole disk, not a partition:** `/dev/sda`, not `/dev/sda1`.
- **Triple-check `of=`.** `dd` never asks "are you sure?" Point it at the wrong drive and that drive is wiped instantly. That's why it's nicknamed "disk destroyer."
- No need to format the USB first; `dd` overwrites everything.
- Wait for the prompt to come back before unplugging.
