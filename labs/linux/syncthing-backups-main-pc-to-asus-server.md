Lab — Syncthing Backups: Windows 11 MAIN-PC → Ubuntu Server (CasaOS/Docker)

Goal
Create a safe one-way backup workflow from Windows 11 (MAIN-PC) to Ubuntu Server (ASUS Server) using Syncthing installed through CasaOS. Configure firewall ports, validate syncing, and document troubleshooting of container storage paths.

## User Impact

- User backups were not centralized or validated end-to-end.
- Failed sync paths risked giving a false sense of backup success.

## Business Impact

- Increased risk of data loss without reliable off-device backups.
- More support time required to troubleshoot path/permission mismatches.

Environment

MAIN-PC: Windows 11 (Syncthing installed locally)

ASUS Server: Ubuntu 24.04.3 LTS (<REDACTED_LAN_HOST>)

CasaOS UI: http://<REDACTED_LAN_HOST>:81

Syncthing UI (ASUS): http://<REDACTED_LAN_HOST>:8384

Firewall: UFW enabled (default deny incoming)

Syncthing container: big-bear-syncthing

Persistent storage (host): /DATA/AppData/big-bear-syncthing/data

Container storage mount: /var/syncthing

Steps

Install Syncthing via CasaOS (ASUS Server)

Install from CasaOS App Store

Confirm Syncthing UI opens on MAIN-PC: http://<REDACTED_LAN_HOST>:8384

Firewall: allow Syncthing Web UI
sudo ufw allow 8384/tcp comment 'Syncthing Web UI'
sudo ufw reload

Pair MAIN-PC ↔ ASUS Server in Syncthing

Add Remote Device on ASUS Syncthing UI

Accept pairing on MAIN-PC

Validate status shows Connected

Firewall: allow sync + discovery traffic (LAN)
sudo ufw allow 22000/tcp comment 'Syncthing Sync'
sudo ufw allow 22000/udp comment 'Syncthing QUIC'
sudo ufw allow 21027/udp comment 'Syncthing Local Discovery'
sudo ufw reload

Create backup folder with safe one-way behavior

MAIN-PC folder: Send Only

ASUS folder: Receive Only

Enable File Versioning on ASUS folder (Simple File Versioning, keep 10)

Troubleshooting: folder path permission denied / missing path
Symptom

Syncthing reported: Failed to create folder root directory / folder path missing

Attempted folder path used host path: /DATA/Backups/... (failed)

Root cause

Syncthing runs inside a Docker container. The container does not have host /DATA mounted as /DATA.

The persistent mount inside the container is /var/syncthing, mapped to host /DATA/AppData/big-bear-syncthing/data.

Verification
docker ps | grep syncthing
docker inspect big-bear-syncthing --format '{{json .Mounts}}' | head -c 2000; echo
docker exec -it big-bear-syncthing sh -lc 'ls -ld /var/syncthing; id; whoami'

Fix

Set ASUS folder path to container-mounted path:
/var/syncthing/MAIN-PC-Backups

Host validation (files persist on host storage):
sudo ls -ld /DATA/AppData/big-bear-syncthing/data/MAIN-PC-Backups
sudo ls -l /DATA/AppData/big-bear-syncthing/data/MAIN-PC-Backups | head

Validation test

Create a test file on MAIN-PC in backup folder (e.g., test.txt)

Confirm the file appears on ASUS server under:
/DATA/AppData/big-bear-syncthing/data/MAIN-PC-Backups

Result
One-way backup sync is operational. MAIN-PC sends backups, ASUS server receives and versions them for rollback protection. UFW rules explicitly allow only necessary Syncthing ports.

Prevention / Best Practices

Use Send Only (client) + Receive Only (server) for backups to reduce accidental deletion risk.

When a service is containerized, confirm the container’s mounted storage path before choosing filesystem paths.

Keep UFW rules minimal and documented.
