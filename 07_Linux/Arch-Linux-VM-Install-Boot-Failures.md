# Arch Linux Install in a VM: Bootloader and Boot Failures

| Field | Details |
|---|---|
| **Type** | Ticket |
| **Category** | Linux / OS Installation |
| **Environment** | VirtualBox VM (Arch Linux) |
| **Status** | Resolved |
| **Tools** | VirtualBox, GRUB, Arch installation guide |
| **Skills** | Linux installation, bootloader troubleshooting, testing before deployment |

## Problem
I wanted to practice installing Arch in VirtualBox before putting it on my new PC. My first four installs failed.

## Diagnosis
- Attempts 1 and 2: I broke the GRUB bootloader, so the VM couldn't load the OS.
- Attempts 3 and 4: something else was misconfigured and it still wouldn't boot.
- The real problem: I kept doing the same thing over and over instead of asking whether the guide I was following was the issue.

## Resolution
- Switched to a different install guide and went slower, checking each step before moving on.
- Attempt 5 installed and booted.
- Since it was all in a VM, none of the failed attempts touched my real PC.
- Once it booted, I set up the desktop myself: Hyprland, a taskbar, clock, file manager, system monitor, and my apps.
- Then I installed it on the real PC. That's in [Dual-Boot Setup: Arch + Windows 11 on Separate SSDs](Dual-Boot-Arch-Windows11-Separate-SSDs.md).

## Lesson
- Try it in a VM first. Breaking a VM costs nothing.
- If I fail the same way twice, I need to change my approach, not just try again.
