# Nightly Proxmox Backups with a Second Copy on My PC

| Field | Details |
|---|---|
| **Type** | Lab Build |
| **Category** | Homelab / Backup and Recovery |
| **Environment** | Proxmox VE 9.2 (`olympus`), Arch Linux PC with a spare 1 TB NVMe, Tailscale |
| **Status** | Complete |
| **Tools** | Proxmox Backup (vzdump), `lsblk`, `wipefs`, `sfdisk`, `mkfs.ext4`, `/etc/fstab`, SSH keys, `rsync`, systemd timers |
| **Skills** | Backup strategy, disk partitioning and formatting, persistent mounts, SSH key auth, task automation |

## Goal
Back up my Docker VM every night, and keep a second copy off the server. A backup that lives on the same drive as the server isn't much of a backup. If that drive dies, both go with it.

## Environment
- **Server:** `olympus`, Proxmox VE 9.2 on a 256 GB NVMe, running VM 100 `hermes`
- **Backup target:** my Arch PC. It had a second 1 TB NVMe that still held my old Windows 11 install, which I wasn't using anymore.
- **Connection:** Tailscale between the PC and the server (direct connection, not relayed)

## Steps
1. **Proxmox backup job:** all VMs, every day at 04:00, snapshot mode (the VM keeps running), ZSTD compression, keep the last 3. Ran it once by hand to test it.
2. **Found the right drive before wiping anything.** `lsblk` showed the Arch drive mounted at `/boot`, `/`, and `/home`. The 1 TB drive had nothing mounted and the standard Windows layout (200M boot, 16M reserved, the big C: partition, and recovery). `lsblk -f` showed `vfat` and `ntfs`, which confirmed it.
3. Wiped it and made one Linux partition:
   ```
   sudo wipefs -a /dev/nvme1n1p1 /dev/nvme1n1p2 /dev/nvme1n1p3 /dev/nvme1n1p4 /dev/nvme1n1
   echo ',,L' | sudo sfdisk --label gpt /dev/nvme1n1
   sudo mkfs.ext4 -L storage /dev/nvme1n1p1
   ```
4. Mounted it at `/mnt/storage` through `/etc/fstab`, by label, with `nofail` so the PC still boots if the drive ever dies:
   ```
   LABEL=storage  /mnt/storage  ext4  defaults,nofail  0 2
   ```
5. Made an SSH key on the PC (`ssh-keygen -t ed25519`) and copied it to the server (`ssh-copy-id root@olympus`) so the copy can run without a password.
6. The PC pulls the backups from the server. The server never needs access to my PC.
   ```
   rsync -a --delete root@olympus:/var/lib/vz/dump/ /mnt/storage/backups/olympus/
   ```
   `--delete` keeps the PC matching the server, so when Proxmox drops an old backup the PC does too.
7. Automated it with a systemd user service and timer:
   - Runs every day at 10:00, after the 04:00 backup
   - `Persistent=true` so it catches up at login if the PC was off
   - `ConditionPathIsMountPoint=/mnt/storage` so it skips instead of filling my main drive if the 1 TB isn't mounted

## Verification
- Proxmox backup task finished with **OK** in 46 seconds (4.3 GB file).
- `df -h /mnt/storage` shows 916G mounted.
- `ssh root@olympus exit` works with no password prompt.
- `tailscale status` shows a direct connection to `olympus`, not a relay.

## Issues Hit
- **Pasting added junk to the command** (`^[[200~...~`), so the shell said `command not found`. It's a terminal paste glitch. Pasting again worked.
- **`systemctl --user start` looked frozen.** It wasn't. A `oneshot` service makes `start` wait until the job is done, and the copy was 4.3 GB.
- **`ls` only showed the log file mid-copy.** rsync writes to a hidden temp file and renames it at the end. `ls -la` showed it growing.

## Lesson
- The 3-2-1 rule is 3 copies, on 2 different drives, with 1 offsite. I have 2 copies on 2 machines now. Nothing offsite yet.
- Check the drive two different ways before wiping it. `dd`, `wipefs`, and `mkfs` don't ask "are you sure."
- Mount by label with `nofail`, and make automated jobs check that the drive is actually mounted.
- Pull backups to the safer machine instead of letting the server push to it.
- A backup I've never restored is just a guess. I still need to test a restore.

## Next
- Test a restore to a new VM ID
- Two 4 TB drives in RAID 1 for `olympus` once I buy them
