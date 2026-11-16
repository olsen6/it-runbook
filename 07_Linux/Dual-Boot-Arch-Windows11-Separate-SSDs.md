# Dual-Boot Setup: Arch + Windows 11 on Separate SSDs

| Field | Details |
|---|---|
| **Type** | Ticket |
| **Category** | Linux / Boot Configuration |
| **Environment** | Custom PC with two SSDs: Arch Linux and Windows 11 |
| **Status** | Resolved |
| **Tools** | UEFI firmware settings |
| **Skills** | UEFI/BIOS configuration, risk assessment, troubleshooting |

> Follow-up to [Arch Linux Install in a VM: Bootloader and Boot Failures](Arch-Linux-VM-Install-Boot-Failures.md). This is what happened when I put it on the real PC.

## Problem
I set up my PC with Arch on one SSD (for programming and security practice) and Windows 11 on a second SSD (for everyday use). It kept booting straight into Windows, and Windows was slow to start.

## Diagnosis
- The Windows SSD was set as the first boot device in UEFI.
- While looking into it, I read that Windows updates sometimes wipe out the Linux bootloader. I'd already put the two systems on separate drives because sharing one drive is riskier, but updates could still cause problems.
- My Windows key from my old laptop didn't carry over, so Windows wasn't activated anyway.

## Resolution
- Changed the boot order in UEFI so Arch comes first.
- Since Windows wasn't activated and could break my Linux boot, I decided it wasn't worth paying for a license. I disabled the Windows SSD in UEFI and switched to Arch full time.
- With Windows out of the boot process, startup is a lot faster. No license cost, and nothing can mess with my bootloader now.

## Lesson
- Look up how two OSes affect each other before dual booting.
- Separate drives beat splitting one drive.
- Removing the thing causing the problem can be a valid fix.
