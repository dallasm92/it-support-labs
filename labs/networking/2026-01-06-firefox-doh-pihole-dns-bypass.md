# Lab: Raspberry Pi Pi-hole Setup + Firefox DoH DNS Bypass Troubleshooting (Coursera/YouTube)

Date: 2026-01-06  
Category: Networking / DNS / Home Lab  
Device(s): Raspberry Pi (Pi-hole DNS), Windows 11 PC, Firefox

## Summary

I set up a Raspberry Pi as a dedicated DNS device running Pi-hole. After Pi-hole was installed and working, Coursera and YouTube intermittently failed to load. At first it looked like the Pi or Pi-hole was the problem because Pi-hole logs were empty and disabling blocking didn’t change anything.

Root causes:
1) The Windows client was using an ISP-provided IPv6 DNS resolver, bypassing Pi-hole.  
2) Firefox’s DNS over HTTPS (DoH) “Default Protection” was interfering; switching DoH to **Off** restored normal browsing.

End result:
- The Windows client uses Pi-hole for DNS resolution (confirmed with `nslookup` and Pi-hole logs).
- Firefox uses system DNS (Pi-hole) instead of DoH.
- Coursera/YouTube load normally again.

---

## Environment

### Network (Redacted)
- Home LAN (private addressing)
- Router provides DHCP
- Raspberry Pi runs Pi-hole as the DNS resolver
- Windows 11 PC is a client on the same LAN

### Client Browser
- Firefox (primary browser)
- Firefox DoH initially: Default Protection
- Firefox DoH final: Off (uses system resolver)

---

## Goals

- Make the Raspberry Pi a stable, always-on DNS filtering device (Pi-hole).
- Verify Pi-hole is actually being used by clients (logs show client queries).
- Fix intermittent site failures caused by DNS bypass / DoH behavior.

---

## Build: Raspberry Pi Up and Running (High Level)

> Key steps to get the Pi online and manageable as a headless server.

### 1) Base OS + Network Access
- Installed Raspberry Pi OS (headless setup).
- Ensured SSH access (headless management).
- Updated packages:
  ```bash
  sudo apt update && sudo apt full-upgrade -y
  sudo reboot
2) Stable Addressing (Recommended)

Ensured the Pi keeps a consistent LAN address (via DHCP reservation on the router).

This avoids “moving DNS server” problems and simplifies troubleshooting.

Build: Pi-hole Installed and Functioning
1) Install Pi-hole

On the Raspberry Pi:

curl -sSL https://install.pi-hole.net | bash


During the installer:

Selected the correct network interface (Ethernet/Wi-Fi depending on how the Pi is connected).

Confirmed the Pi’s LAN address is stable (reservation/static approach).

Selected an upstream DNS provider (example: Cloudflare).

2) Access the Admin Console

From the Windows PC:

Open Pi-hole Web UI via the Pi-hole admin page (local network access).

3) Confirm Pi-hole is processing queries

In the Pi-hole Web UI:

Tools → Tail pihole.log
Expected patterns:

query[...] <domain> from <client>

forwarded <domain> to <upstream>

reply <domain> is <ip>

4) Update gravity / lists (maintenance)

In Pi-hole:

Tools → Update Gravity (or via CLI):

pihole -g

Client Configuration: Windows Uses Pi-hole DNS
Set the Windows PC DNS to Pi-hole

On Windows:

Network Connections → right-click the real adapter (Ethernet/Wi-Fi) → Properties → IPv4

Set DNS to:

Preferred DNS: Pi-hole

Alternate DNS: a public resolver (optional fallback)

Flush DNS cache:

ipconfig /flushdns

The Problem

Symptoms:

Coursera error page (“Something went wrong”)

YouTube content/thumbnails failing to load

Pi-hole Query Log showed no activity even while browsing

Disabling Pi-hole blocking didn’t change the behavior

Initial suspicion:

Raspberry Pi / Pi-hole was blocking something or failing DNS resolution

Reality:

DNS was being bypassed (IPv6 resolver) and Firefox DoH behavior was interfering.

Troubleshooting (Timeline)
1) “Pi-hole logs are empty” check

Observation:

Pi-hole showed ~0 queries/min while actively browsing.

That usually means the client is not using Pi-hole for DNS.

2) Validate which DNS resolver the client is actually using

On Windows:

ipconfig /all


Then verify with:

nslookup coursera.org


Finding:

nslookup showed the client was using an ISP-provided IPv6 DNS resolver, not Pi-hole.

3) Fix: force the client to use Pi-hole for DNS

After updating adapter DNS settings, flush and re-test:

ipconfig /flushdns
nslookup coursera.org


Expected result:

nslookup shows the DNS server is the Pi-hole resolver (not the ISP resolver).

4) Confirm Pi-hole sees the traffic (real-time)

In Pi-hole:

Tools → Tail pihole.log
Result:

Queries from the Windows PC started showing up immediately.

5) Identify Firefox DoH as the final blocker

Even after Pi-hole was working, site failures persisted until Firefox DoH was changed.

In Firefox:

Settings → Privacy & Security → DNS over HTTPS → set to Off

Result:

Coursera/YouTube loaded normally again.

Resolution

Windows DNS resolution uses Pi-hole (confirmed with nslookup).

Pi-hole logs show client queries (confirming traffic actually reaches Pi-hole).

Firefox DoH is set to Off, forcing Firefox to use system DNS and eliminating DoH interference.

Prevention / Lessons Learned

Empty Pi-hole logs usually means DNS bypass, not “Pi-hole is broken.”

Prove which resolver is in use:

nslookup coursera.org
ipconfig /all


IPv6 can bypass your intended DNS path

If the client prefers an ISP IPv6 DNS resolver, Pi-hole won’t see queries.

Choose a strategy:

Keep the lab simple (IPv4-only DNS path), or

Implement an IPv6-aware DNS strategy where clients are explicitly pointed at Pi-hole.

Firefox DNS over HTTPS can conflict with network DNS tooling

If you want Pi-hole to be authoritative, keep Firefox DoH set to Off (use system DNS).

Always configure DNS on the correct adapter

Hyper-V virtual adapters can confuse DNS troubleshooting.

Set DNS on the real interface used for internet connectivity.

Validation Checklist
Pi-hole side

Dashboard shows queries/min increasing while browsing.

Tools → Tail pihole.log shows live queries from the Windows client.

Windows side
nslookup coursera.org
nslookup youtube.com


Expected: the DNS server shown is the Pi-hole resolver.

Firefox side

DNS over HTTPS: Off

Coursera + YouTube load normally
