# Dual-Boot Setup: Arch + Windows 11 on Separate SSDs

> Follow-up to [Arch Linux Install in a VM: Bootloader and Boot Failures](Arch-Linux-VM-Install-Boot-Failures.md). After the install worked in the VM, this is how it went on real hardware.

## Problem
Set up a dual boot on my custom PC: Arch Linux on one SSD for programming and cybersecurity practice, and Windows 11 on a second SSD as my daily OS. The PC kept **booting into Windows first**, and Windows was slow to start up.

## Diagnosis
- **Boot order:** The UEFI firmware had the Windows SSD set as the first boot device.
- **Risk research:** Read reports that a Windows update can occasionally break the Linux bootloader, forcing a full rebuild. Also read that dual booting on a **single partitioned drive** is riskier than using separate SSDs, which is why I used two drives.
- **Licensing:** My Windows key from my laptop wouldn't transfer to the new PC, so Windows was running unactivated.

## Resolution
- Went into the UEFI (BIOS) settings and **changed the boot priority** to fix the boot order.
- Weighed the risk of Windows updates breaking Arch against the cost of buying a new Windows license, and decided to **disable the Windows SSD in the UEFI settings** and run Arch full time.
- With Windows out of the boot process, the slow startup was gone. Arch boots noticeably faster.
- Result: a stable, fully working PC with fast boot times, no license cost, and no risk of Windows breaking the Linux install.

## Lesson
- Research how two operating systems interact before running them side by side.
- Separate drives are safer than partitioning one drive for dual boot.
- Sometimes the best fix is to isolate the source of the issue.
