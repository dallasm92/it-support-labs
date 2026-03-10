# GitHub SSH setup on Windows 11 (Git Bash)

## Issue
Needed SSH authentication to GitHub to clone/push repositories from Windows 11 without HTTPS credentials.

## User Impact
- User could not reliably push code from the Windows workstation.
- Daily workflow required repeated credential handling until SSH was configured.

## Business Impact
- Slower repository operations and higher chance of credential-related errors.
- Onboarding friction for a common workstation setup requirement.

## Environment
- OS: Windows 11
- Shell: Git Bash (MINGW64)
- SSH key type: ed25519
- GitHub auth: SSH public key added to account

## Troubleshooting
1. Generated ed25519 key pair using `ssh-keygen`.
2. Started ssh-agent and added the private key with `ssh-add`.
3. Added GitHub host fingerprint to known_hosts on first connection.
4. Verified authentication using `ssh -T git@github.com`.

## Resolution
Commands used:
- `ssh-keygen -t ed25519 -C "email"`
- `eval "$(ssh-agent -s)"`
- `ssh-add ~/.ssh/id_ed25519`
- `cat ~/.ssh/id_ed25519.pub` (paste into GitHub → SSH keys)
- `ssh -T git@github.com`

## Prevention / Notes
- Keep each device on its own SSH key so access can be revoked per machine.
- If using multiple OSes, configure line endings to prevent noisy diffs.
