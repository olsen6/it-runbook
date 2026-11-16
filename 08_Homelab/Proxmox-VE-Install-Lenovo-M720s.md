# Proxmox VE Homelab Server on a Lenovo M720s ("Olympus")

| Field | Details |
|---|---|
| **Type** | Lab Build |
| **Category** | Virtualization / Homelab |
| **Environment** | Lenovo ThinkCentre M720s SFF (i5-8400, 8 GB RAM, 256 GB NVMe), Proxmox VE 9.2 |
| **Status** | In Progress |
| **Tools** | Proxmox VE, `dd`, `sha256sum`, `lsblk`, UEFI/BIOS settings, Debian 13 |
| **Skills** | Hypervisor installation, BIOS/UEFI configuration, static IP networking, Linux package repositories, VM provisioning |

## Goal
Turn a used business PC into a 24/7 homelab server for self-hosted services in Docker and, later, an isolated Active Directory lab. Examples of what I plan to run: Uptime Kuma, Pi-hole, and Jellyfin.

## Environment
- **Host:** Lenovo M720s SFF ("Olympus"), Intel i5-8400 (6 cores), 8 GB RAM, 256 GB Samsung NVMe, integrated graphics only
- **Hypervisor:** Proxmox VE 9.2, hostname `olympus.lab`
- **Network:** wired to the home router through a wall jack, static IP on the home LAN. A separate router (R7800 with OpenWrt) will isolate the lab later.
- **VMs:** VM 100 `docker`, Debian 13 (in progress)

## Steps
1. **Made the installer USB on my Arch PC.** Verified the ISO with `sha256sum`, then wrote it with `dd`. See [Bootable USB with dd](../07_Linux/Bootable-USB-with-dd.md).
2. **Enabled virtualization in the BIOS** (Intel VT-x and VT-d).
3. **Installed Proxmox** to the 256 GB NVMe (ext4), with hostname `olympus.lab` and a static IP on the home LAN.
4. **Logged in to the web UI** from my PC on port 8006.
5. **Switched to the free update repositories:** disabled the enterprise repos and added the No-Subscription repo.
6. **Updated and rebooted** to load the new kernel.
7. **Created VM 100 `docker`** from the Debian 13 netinst ISO: 2 cores (CPU type `host`), 3 GB RAM, 40 GB disk, and the QEMU Guest Agent on.

## Verification
- `ip a` on the console showed the expected static IP, and `ping` to the internet worked.
- The web UI loads from my PC, and the node shows a green status.
- Refreshing updates reached all four repositories (Debian, Debian security, and the Proxmox no-subscription repo) with **TASK OK**.
- After the upgrade, the system reported "up-to-date" and booted cleanly on the new kernel.

## Issues Hit
- **`apt-get update` failed right after install.** Proxmox points at the paid enterprise repositories by default, and they reject servers without a subscription. Switching to the No-Subscription repo fixed it.
- **A "No valid subscription" popup appears at every login.** This is normal for the free version, so I just click OK.
- **Docker doesn't belong on the Proxmox host itself.** It can interfere with Proxmox networking, so I put it in a dedicated Debian VM instead.

## Lesson
- Check the BIOS virtualization settings before installing a hypervisor.
- Free Proxmox needs the No-Subscription repo, or updates fail.
- Keep the hypervisor clean and put apps in VMs or containers, so a broken app never takes down the host.

## Next
- Finish the Debian install, then add Docker and Portainer
- First containers: Uptime Kuma, then Pi-hole (DNS only until the lab network is isolated)
- Add a second 8 GB RAM stick, then build the Active Directory lab on an internal-only Proxmox network
