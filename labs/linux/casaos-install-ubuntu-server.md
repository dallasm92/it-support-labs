Lab — Install CasaOS on Ubuntu Server (ASUS Server)

Goal
Install CasaOS on an Ubuntu Server host to provide a lightweight web UI for managing “convenience” apps (without replacing existing Docker stacks).

Environment

Host: ASUS Server (Ubuntu 24.04.3 LTS, x86_64)

Host IP: 192.168.1.221

Docker: installed and running

Existing containers (pre-existing): Pi-hole, Portainer

Network: LAN (192.168.1.0/24)

Firewall: UFW enabled (default deny incoming)

Evidence (Screenshots)

screenshots/2026-02-03-casaos-install/

01-host-info.png

02-docker-running.png

03-casaos-installer-complete.png

04-casaos-ui-loaded.png

05-ufw-allow-81.png

Steps

Confirm host info and IP address
hostnamectl
lsb_release -a 2>/dev/null || cat /etc/os-release
ip a

Confirm Docker is installed and containers are running
docker --version
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"

Identify existing web ports in use (avoid collisions)
sudo ss -lntp

Install CasaOS
(Installer output confirms CasaOS UI binds to port 81)

Firewall: allow CasaOS UI access on LAN
sudo ufw status verbose
sudo ufw allow 81/tcp comment 'CasaOS UI'
sudo ufw reload
sudo ufw status verbose

Validate CasaOS UI from MAIN-PC browser

URL: http://192.168.1.221:81

Expected: CasaOS dashboard loads

Result
CasaOS installed successfully and reachable on the LAN at port 81. Existing Docker containers remained healthy and operational.

Notes / Lessons Learned

Check for port conflicts before installing web UIs (Portainer, Pi-hole, Nextcloud, etc.).

With UFW default deny incoming, new services may “listen” but still time out from the browser until explicitly allowed.
