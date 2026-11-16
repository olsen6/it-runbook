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
I bought a used business PC to run 24/7 as my homelab server. The plan is self-hosted apps in Docker (things like Uptime Kuma, Pi-hole, and Jellyfin) and later an isolated Active Directory lab.

## Environment
- **Host:** Lenovo M720s SFF ("Olympus"), Intel i5-8400 (6 cores), 8 GB RAM, 256 GB Samsung NVMe, integrated graphics only
- **Hypervisor:** Proxmox VE 9.2, hostname `olympus.lab`
- **Network:** wired to the home router through a wall jack, static IP on the home LAN. A separate router (R7800 with OpenWrt) will isolate the lab later.
- **VMs:** VM 100 `hermes`, Debian 13 (Docker host, Docker not installed yet)
- **Remote access:** Tailscale on the server, my PC, and my phone

## Steps
1. Made the installer USB on my Arch PC. Checked the ISO with `sha256sum`, then wrote it with `dd` ([Bootable USB with dd](../07_Linux/Bootable-USB-with-dd.md)).
2. Turned on Intel VT-x and VT-d in the BIOS.
3. Installed Proxmox on the 256 GB NVMe (ext4), hostname `olympus.lab`, static IP on my home network.
4. Logged in to the web UI from my PC on port 8006.
5. Turned off the paid enterprise repos and added the free No-Subscription repo.
6. Updated everything and rebooted for the new kernel.
7. Installed Tailscale on the server so I can reach it from my phone and PC anywhere, without opening any ports on my router.
8. Created VM 100, `hermes`, from the Debian 13 netinst ISO: 2 cores (CPU type `host`), 3 GB RAM, 40 GB disk, QEMU Guest Agent on.
9. Installed Debian with no desktop, just the SSH server and standard utilities.

## Verification
- `ip a` showed the right IP and `ping` to the internet worked.
- The web UI loads from my PC and the node shows green.
- Updates pulled from every repo with **TASK OK**, and the server came back up fine on the new kernel.
- With Wi-Fi off on my phone (cell data only), the Proxmox UI still loaded through Tailscale.

## Issues Hit
- Updates failed right after install. Proxmox uses the paid enterprise repos by default and they block you without a subscription. Switching to the free repo fixed it.
- A "No valid subscription" popup shows up every time I log in. That's normal on the free version.
- I didn't install Docker on the Proxmox host directly because it can mess with Proxmox's networking. It gets its own Debian VM instead.
- The VM console got stuck in Caps Lock (the VM and my keyboard got out of sync). Pressing Caps Lock inside the console fixed it. I'll use SSH for the VM from now on since the console can't paste either.

## Lesson
- Turn on virtualization in the BIOS before installing a hypervisor.
- Free Proxmox needs the No-Subscription repo or updates fail.
- Keep the host clean. If an app breaks inside a VM, the server stays up.

## Next
- Install Docker and Portainer on `hermes`
- First containers: Uptime Kuma, then Pi-hole (DNS only until the lab network is isolated)
- Add a second 8 GB RAM stick, then build the Active Directory lab on an internal-only Proxmox network
