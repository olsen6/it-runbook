# IT Runbook — Olsen Romelus

My notes from fixing things, building labs, and breaking my own setup. Mostly Linux and homelab work so far. Active Directory, networking, and Windows entries are coming as I do them.

---

## About Me

I'm Olsen. I'm going for help desk and desktop support roles, with cybersecurity and pentesting as the long-term goal. CompTIA A+ certified, Per Scholas IT Support grad (Nov 2026), studying for Network+ and Security+. Everything here is stuff I actually ran into.

---

## Entries

| Entry | Type | Category | Summary |
|---|---|---|---|
| [Arch Linux Install in a VM: Bootloader and Boot Failures](07_Linux/Arch-Linux-VM-Install-Boot-Failures.md) | Ticket | Linux / OS Installation | Four failed installs (GRUB and boot misconfiguration) before a clean install by changing my approach |
| [Dual-Boot Setup: Arch + Windows 11 on Separate SSDs](07_Linux/Dual-Boot-Arch-Windows11-Separate-SSDs.md) | Ticket | Linux / Boot Configuration | Fixed boot order in UEFI, then isolated Windows to run Arch full time |
| [Arch Update Failing: PGP "Unknown Trust" and File Conflicts](07_Linux/Arch-Update-PGP-Keyring-and-File-Conflicts.md) | Ticket | Linux / Package Management | Reset an outdated keyring, then cleared leftover files with a scoped overwrite |
| [Bootable USB with dd](07_Linux/Bootable-USB-with-dd.md) | Quick Reference | Linux CLI / OS Installation | Verify an ISO, find the right drive, and write a bootable USB safely |
| [Hyprland Update Broke the Desktop: Migrating hyprland.conf to Lua](07_Linux/Hyprland-0.57-Lua-Config-Migration.md) | Ticket | Linux / Desktop Environment | Hyprland 0.57 dropped the old config format; converted my config to Lua by hand |
| [Proxmox VE Homelab Server on a Lenovo M720s ("Olympus")](08_Homelab/Proxmox-VE-Install-Lenovo-M720s.md) | Lab Build | Virtualization / Homelab | Turned a used business PC into a Proxmox server with a Debian Docker VM |
| [Self-Hosted Apps on hermes](08_Homelab/Self-Hosted-Apps-Docker-hermes.md) | Lab Build | Homelab / Containers | Portainer, Uptime Kuma, Pi-hole over Tailscale, Jellyfin, and Navidrome in Docker |
| [Nightly Proxmox Backups with a Second Copy on My PC](08_Homelab/Proxmox-Backups-Second-Copy-on-PC.md) | Lab Build | Homelab / Backup and Recovery | Nightly VM backups, an old Windows drive wiped for storage, and an automatic daily copy with rsync |
| [Tailscale Install Failed: Old Package List, Full Disk, No Reboot](07_Linux/Tailscale-Install-Disk-Full-Kernel-Mismatch.md) | Ticket | Linux / Package Management / Networking | Three stacked problems found by reading logs: stale mirrors, full root partition, kernel not rebooted |

---

## Repository Structure

| Folder | Contents |
|--------|----------|
| `07_Linux/` | Linux installs, configuration, and troubleshooting |
| `08_Homelab/` | Homelab server builds: Proxmox, VMs, and self-hosted services |
| `_templates/` | Templates for each type of entry: **Ticket**, **Lab Build**, and **Quick Reference** |

More folders (Active Directory, Networking, Windows, PowerShell) get added once I have real entries for them.

---

## How to Use This Runbook

Every entry starts with a quick summary table. There are three kinds:

- **Ticket:** Problem → Diagnosis → Resolution → Lesson
- **Lab Build:** Goal → Environment → Steps → Verification → Issues Hit → Lesson
- **Quick Reference:** commands and settings for a specific tool or topic

I update entries when something changes or breaks again.

---

> If you spot a mistake or a better way to do something, let me know.
