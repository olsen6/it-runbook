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
Installing Arch Linux in VirtualBox before putting it on new hardware. The first four installs failed.

## Diagnosis
- **Attempts 1–2:** Broke the **GRUB bootloader**, so the VM couldn't load the OS.
- **Attempts 3–4:** The system was **misconfigured** and wouldn't boot.
- **Root cause:** Kept repeating the same approach without stepping back to check whether my process or my source was the problem.

## Resolution
- **Switched to a different installation guide** and followed it **step by step**, checking each stage before moving on.
- **Attempt 5 installed and booted successfully.**
- Tested everything in the VM first, so the real PC was never at risk.
- After it booted, built the desktop from scratch: Hyprland, taskbar, clock, file manager, system monitor, and apps.
- After it worked in the VM, installed Arch on real hardware. See [Dual-Boot Setup: Arch + Windows 11 on Separate SSDs](Dual-Boot-Arch-Windows11-Separate-SSDs.md).

## Lesson
- Test in a VM before deploying to real hardware.
- When you're running in circles, stop and review your sources and your process instead of repeating the same mistakes.
