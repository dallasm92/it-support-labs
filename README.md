# IT Support Labs
Ticket-style troubleshooting labs written like real help desk work: issue → environment → troubleshooting → resolution → prevention. :contentReference[oaicite:4]{index=4}

## Start here (2 minutes)
- Lab Index: [LAB_INDEX.md](LAB_INDEX.md)
- Ticket template: [templates/ticket-template.md](templates/ticket-template.md)

## Featured labs (portfolio)
- [GitHub SSH setup on Linux Mint](labs/linux/2026-01-01-github-ssh-setup.md)
- [GitHub SSH setup on Windows 11](labs/windows/2026-01-01-github-ssh-setup-windows.md)
- [Windows Server File Share + Map Network Drive (AD Domain)](labs/windows/2026-01-06-windows-server-file-share-map-drive.md)
- [Pi-hole DNS + Firefox DoH bypass (Coursera/YouTube failing to load)](labs/networking/)

---

## What this repo demonstrates
- Realistic support scenarios across Windows, Linux, and networking
- A repeatable troubleshooting methodology (symptoms → hypotheses → tests → fix → verification)
- Clear documentation and prevention notes that mirror ticket work :contentReference[oaicite:5]{index=5}

Repo started: 2026-01-01 :contentReference[oaicite:6]{index=6}

---

## Repo structure
- Windows labs: `labs/windows/`
- Linux labs: `labs/linux/`
- Networking labs: `labs/networking/`
- Security labs: `labs/security/`

Naming convention:
`labs/<category>/YYYY-MM-DD-short-title.md` :contentReference[oaicite:7]{index=7}

---

## Evidence (screenshots & screen recordings)
You do NOT need screenshots for every lab, but for “featured” labs you should capture proof.

Recommended evidence checklist (pick 2–4 per lab):
- Screenshot: error message/symptom (what user saw)
- Screenshot: key config state (before/after)
- Screenshot: verification test result (ping/nslookup/logon/etc.)
- Screen recording (30–90s): the exact fix steps in GUI

Storage rule:
- Put lab artifacts in `labs/<category>/screenshots/<YYYY-MM-DD-lab-slug>/`
- Link them at the bottom of the lab in an “Evidence” section

---

## Sanitization & security
Public write-ups omit sensitive data (keys/tokens, private hostnames, unique IDs). :contentReference[oaicite:8]{index=8}

