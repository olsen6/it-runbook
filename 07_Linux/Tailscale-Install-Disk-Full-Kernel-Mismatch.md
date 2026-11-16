# Tailscale Install Failed: Old Package List, Full Disk, No Reboot

| Field | Details |
|---|---|
| **Type** | Ticket |
| **Category** | Linux / Package Management / Networking |
| **Environment** | Custom PC running Arch Linux (Hyprland) |
| **Status** | Resolved |
| **Tools** | pacman, reflector, df, journalctl, systemctl, Tailscale |
| **Skills** | Reading logs, disk space cleanup, kernel modules, troubleshooting one error at a time |

## Problem
I wanted my Arch PC on the same Tailscale network as my homelab server and phone. The install failed three times, with a different error each time.

## Diagnosis
1. **404 errors.** Every mirror returned 404 for the Tailscale package.
   - My package list was out of date. The mirrors had a newer version, so the file pacman was looking for didn't exist anymore.
2. **"Partition / too full."** After refreshing, the install failed because there was no disk space.
   - `df -h /` showed my 46 GB root partition was full.
   - The pacman cache was still holding every package from a big update I'd done a few days earlier (1,100+ packages).
3. **Service kept crashing.** Tailscale installed, but `tailscale up` gave `503 Service Unavailable: no backend`.
   - `journalctl -u tailscaled` showed `Module tun not found` and said the driver only existed for newer kernel versions.
   - My kernel had updated but I never rebooted, so the running kernel couldn't load the network driver Tailscale needs.

## Resolution
1. Cleared space:
   ```
   sudo pacman -Scc
   sudo journalctl --vacuum-size=100M
   ```
2. Switched to US mirrors and updated everything along with the install:
   ```
   sudo reflector --country US --protocol https --latest 10 --sort rate --save /etc/pacman.d/mirrorlist
   sudo pacman -Syu tailscale
   ```
3. Checked that the new kernel files were in `/boot`, then rebooted.
4. Started Tailscale and logged in:
   ```
   sudo systemctl enable --now tailscaled
   sudo tailscale up
   ```
5. Confirmed my PC, server, and phone all show online, and that `ssh root@olympus` works from my PC.

## Lesson
- Fix one error at a time and re-test. Each fix revealed the next problem.
- Read the logs first. `journalctl` told me exactly what was missing and why.
- Reboot after kernel updates, and run `sudo pacman -Sc` now and then so the cache doesn't fill the disk.
- Use `pacman -Syu`, not `pacman -Sy <package>`. Partial updates can break Arch.
