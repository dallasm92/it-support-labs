Lab — Install CasaOS on Ubuntu Server (ASUS Server)

Goal
Install CasaOS on an Ubuntu Server host to provide a lightweight web UI for managing “convenience” apps (without replacing existing Docker stacks).

## User Impact

- Admins lacked a simple centralized web interface for app management tasks.
- Routine service checks required more manual CLI effort.

## Business Impact

- Slower operational workflows for common container/service actions.
- Higher support friction for basic administration tasks.

Environment

Host: ASUS Server (Ubuntu 24.04.3 LTS, x86_64)

Host IP: <REDACTED_LAN_HOST>

Docker: installed and running

Existing containers (pre-existing): Pi-hole, Portainer

Network: LAN (sanitized subnet)

Firewall: UFW enabled (default deny incoming)

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

URL: http://<REDACTED_LAN_HOST>:81

Expected: CasaOS dashboard loads

Result
CasaOS installed successfully and reachable on the LAN at port 81. Existing Docker containers remained healthy and operational.

Notes / Lessons Learned

Check for port conflicts before installing web UIs (Portainer, Pi-hole, Nextcloud, etc.).

With UFW default deny incoming, new services may “listen” but still time out from the browser until explicitly allowed.
