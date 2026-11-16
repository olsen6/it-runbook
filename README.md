# IT Runbook — Olsen Romelus

A personal IT runbook documenting procedures, troubleshooting steps, lab work, and resolved tickets from hands-on practice and my homelab.

This repository serves as a living reference and portfolio piece demonstrating practical IT support skills, starting with Linux administration, with Active Directory, networking, and Windows entries coming next.

---

## About Me

**Olsen Romelus** — IT Support candidate focused on help desk and desktop support roles, building toward cybersecurity and penetration testing. CompTIA A+ certified, now studying for Security+, and a graduate of the Per Scholas IT Support Program (November 2026). This runbook reflects real lab work, self-study, and problem-solving experience built along the way.

---

## Entries

| Entry | Type | Category | Summary |
|---|---|---|---|
| [Arch Linux Install in a VM: Bootloader and Boot Failures](07_Linux/Arch-Linux-VM-Install-Boot-Failures.md) | Ticket | Linux / OS Installation | Four failed installs (GRUB and boot misconfiguration) before a clean install by changing my approach |
| [Dual-Boot Setup: Arch + Windows 11 on Separate SSDs](07_Linux/Dual-Boot-Arch-Windows11-Separate-SSDs.md) | Ticket | Linux / Boot Configuration | Fixed boot order in UEFI, then isolated Windows to run Arch full time |
| [Arch Update Failing: PGP "Unknown Trust" and File Conflicts](07_Linux/Arch-Update-PGP-Keyring-and-File-Conflicts.md) | Ticket | Linux / Package Management | Reset an outdated keyring, then cleared leftover files with a scoped overwrite |
| [Bootable USB with dd](07_Linux/Bootable-USB-with-dd.md) | Quick Reference | Linux CLI / OS Installation | Verify an ISO, find the right drive, and write a bootable USB safely |
| [Hyprland Update Broke the Desktop: Migrating hyprland.conf to Lua](07_Linux/Hyprland-0.57-Lua-Config-Migration.md) | Ticket | Linux / Desktop Environment | Hyprland 0.57 dropped the old config format; converted my config to Lua by hand |
| [Proxmox VE Homelab Server on a Lenovo M720s ("Olympus")](08_Homelab/Proxmox-VE-Install-Lenovo-M720s.md) | Lab Build | Virtualization / Homelab | Turned a used business PC into a Proxmox server; Docker VM in progress |
| [Tailscale Install Failed: Old Package List, Full Disk, No Reboot](07_Linux/Tailscale-Install-Disk-Full-Kernel-Mismatch.md) | Ticket | Linux / Package Management / Networking | Three stacked problems found by reading logs: stale mirrors, full root partition, kernel not rebooted |

---

## Repository Structure

| Folder | Contents |
|--------|----------|
| `07_Linux/` | Linux installs, configuration, and troubleshooting |
| `08_Homelab/` | Homelab server builds: Proxmox, VMs, and self-hosted services |
| `_templates/` | Templates for each type of entry: **Ticket**, **Lab Build**, and **Quick Reference** |

**Coming soon:** Active Directory, Custom PC Build, Networking, Windows, PowerShell, and Tickets Solved sections. Each is added once it has real content.

---

## How to Use This Runbook

Each document is a standalone reference, and each one starts with a summary table (type, category, environment, status, tools, and skills). Entries come in three types:

- **Ticket:** Problem → Diagnosis → Resolution → Lesson
- **Lab Build:** Goal → Environment → Steps → Verification → Issues Hit → Lesson
- **Quick Reference:** commands and settings for a specific tool or topic

Entries are updated as new issues are encountered and resolved.

---

> This repository is part of my public IT portfolio. Feedback and suggestions are welcome.
