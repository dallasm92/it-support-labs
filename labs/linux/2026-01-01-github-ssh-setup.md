# GitHub SSH setup on Linux Mint

## Issue
Needed to authenticate to GitHub via SSH to clone/push repositories from Linux Mint.

## Environment
- OS: Linux Mint
- GitHub: SSH auth with ed25519 key
- Tools: git, openssh-client

## Troubleshooting
1. `ssh -T git@github.com` returned: Permission denied (publickey)
2. Checked `~/.ssh` and found no default key pair.
3. Generated ed25519 key, but initially saved with a nonstandard filename.
4. Moved key into `~/.ssh/id_ed25519` and `~/.ssh/id_ed25519.pub`
5. Added key to ssh-agent and GitHub account settings.

## Resolution
- Generated key: `ssh-keygen -t ed25519 -C "email"`
- Added to agent: `eval "$(ssh-agent -s)"` then `ssh-add ~/.ssh/id_ed25519`
- Added public key to GitHub: Settings → SSH and GPG keys → New SSH key
- Verified: `ssh -T git@github.com` succeeded

## Prevention / Notes
- Use default save path during `ssh-keygen` to avoid confusion.
- Keep private key permissions at 600 and public key at 644.
