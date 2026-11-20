# Self-Hosted Apps on hermes: Docker, Portainer, Uptime Kuma, Pi-hole, Jellyfin, Navidrome

| Field | Details |
|---|---|
| **Type** | Lab Build |
| **Category** | Homelab / Containers / Self-Hosting |
| **Environment** | Debian 13 VM (`hermes`) on Proxmox VE 9.2 (`olympus`), Docker Engine + Compose, Tailscale |
| **Status** | Complete |
| **Tools** | Docker, Docker Compose, Portainer CE, Uptime Kuma, Pi-hole, Jellyfin, Navidrome, Tailscale |
| **Skills** | Containers, Docker Compose, DNS, monitoring, port mapping, reading container logs, troubleshooting |

## Goal
Get my first real apps running on the homelab: a dashboard to manage containers, something to tell me when things go down, ad blocking, and my own media and music servers.

## Environment
- **Host VM:** `hermes`, Debian 13, 2 cores, 3 GB RAM, 40 GB disk, running on `olympus`
- **Layout:** every app gets its own folder in `/srv` with a `compose.yaml` and its data next to it (`/srv/pihole`, `/srv/jellyfin`, etc.). One folder per app makes it easy to back up, move, or rebuild.
- **Media:** `/srv/media` (movies, shows, music), shared into Jellyfin and Navidrome
- **Access:** from my PC and phone over Tailscale. Nothing is open to the internet.

| App | What it does | Port |
|---|---|---|
| Portainer | Web dashboard for managing containers | 9443 |
| Uptime Kuma | Checks everything every minute and shows what's down | 3001 |
| Pi-hole | Blocks ads and trackers at the DNS level | 53 (DNS), 8080 (web) |
| Jellyfin | Movie and TV server | 8096 |
| Navidrome | Music server | 4533 |

## Steps
1. Installed Docker Engine and the Compose plugin on `hermes`, added my user to the `docker` group, and tested with `docker run hello-world`.
2. Deployed each app the same way: make the folder, write `compose.yaml`, run `docker compose up -d`.
3. **Portainer:** used the `lts` image and mounted the Docker socket so it can see the other containers.
4. **Uptime Kuma:** added monitors for each layer so I can tell *where* something broke:
   - Ping `olympus`: is the server on?
   - HTTPS to the Proxmox web UI (ignore TLS errors, it's a self-signed cert): is Proxmox working?
   - Ping `google.com`: is my internet up?
   - DNS check against Pi-hole, plus HTTP checks for Jellyfin (`/health`) and Navidrome
5. **Pi-hole:** mapped the web UI to 8080 to keep port 80 free and set `FTLCONF_dns_listeningMode: all` so it answers queries coming in from Docker and Tailscale. Set the password with `pihole setpassword`.
6. Pointed Tailscale's DNS at Pi-hole (global nameserver = `hermes`'s Tailscale IP, override on). Now any device on my tailnet gets ad blocking while Tailscale is on, and my house router doesn't change at all. No DHCP on Pi-hole.
7. Turned off Tailscale DNS on `olympus` (`tailscale set --accept-dns=false`) so the Proxmox host doesn't depend on a VM for DNS. If `hermes` goes down, I still need the host to have internet to fix it.
8. **Jellyfin:** mounted `/srv/media` into the container, set up Movies and Shows libraries, and turned off trickplay and chapter image extraction since they eat CPU and disk on a 2-core VM.
9. **Navidrome:** mounted the music folder read-only and created the admin account in the browser before logging in from my phone (Amperfy).

## Verification
- Portainer shows every container running.
- Uptime Kuma shows all monitors green at 100%.
- Pi-hole dashboard is up with the default blocklist (76,105 domains).
- Jellyfin's web UI loads, and `http://<hermes>:8096/health` returns `Healthy`.
- Navidrome works from my phone in Amperfy.

## Issues Hit
- **`clear` failed over SSH** with `'xterm-kitty': unknown terminal type`. Debian didn't know my terminal. Installing `kitty-terminfo` on `hermes` fixed it.
- **Portainer locked me out.** It only gives you a few minutes to create the admin account on first start, then blocks setup. I had to wipe its `data` folder and redeploy.
- **Uptime Kuma said it was outdated.** The `latest` tag still points to v1, which isn't maintained anymore. Switched the image to the `2` tag and redeployed. My data carried over.
- **Firefox showed `SSL_ERROR_RX_RECORD_TOO_LONG`.** It was forcing HTTPS on an app that only speaks HTTP. Typing `http://` in front fixed it.
- **The Pi-hole container didn't exist.** My compose file never actually saved in nano, so `docker compose` had nothing to run. I rewrote it with a heredoc and ran `cat compose.yaml` to confirm it was there before starting it.
- **A Kuma monitor stayed red** after I edited it. The edit never saved and it was still pointing at `olympus` instead of `hermes`. Deleting it and making a new one fixed it.
- **Tailscale isn't in the Amazon Appstore** for the Fire Stick (sideload only). The Fire Stick is on my home Wi-Fi anyway, so the Jellyfin app just uses `hermes`'s local IP.

## Lesson
- Don't trust the `latest` tag. Pin to a major version like `:2` so you get updates without surprises.
- Check the file is actually there (`cat`) before blaming Docker.
- Use an app's health endpoint for monitoring. It checks the backend, not just that a page loads.
- Use Tailscale names instead of local IPs where I can. They don't change when I swap routers.
- Uptime Kuma runs on the same server it watches. If `olympus` dies completely, nothing alerts me. I need an outside check eventually.

## Next
- Stirling-PDF (lite version, since `hermes` only has 3 GB RAM)
- More RAM for `olympus`, then the cyber lab
- New router (R7800 with OpenWrt): move the LAN, reserve `hermes`'s IP, and hand Pi-hole to every device including the PS5
