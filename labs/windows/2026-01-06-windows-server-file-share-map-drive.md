# Windows Server File Share + Map Network Drive (AD Domain)

## Issue
Need a centralized SMB file share on a Windows Server for domain users, plus a mapped drive on a Windows 11 domain-joined client with verified read/write access.

## User Impact
- Users lacked a consistent shared location for team files.
- Manual file transfers increased the chance of version confusion and access issues.

## Business Impact
- Slower collaboration and increased risk of data sprawl.
- Support overhead increased when share permissions or mappings were inconsistent.

## Environment
- Hyper-V lab environment
- Domain: `lab.local`
- Domain user: `LAB\dmorison` (created in OU: `Lab Users`)
- Server: Windows Server 2022 (SMB share hosted locally)
- Client: Windows 11 (domain-joined, logged in as domain user)
- Shared folder (local path on server): `C:\Shares\LabShare`
- Share name: `LabShare`
- UNC path (sanitized example): `\\DC01\LabShare`
- Mapped drive letter: `L:`

> Note: For public documentation, use neutral names like `DC01` / `FILE01` instead of real hostnames.

## Troubleshooting / Implementation

### 1) Create a domain user (ADUC)
1. Open **Active Directory Users and Computers**
2. Go to: `lab.local` → OU `Lab Users`
3. Create new user:
   - Name: Dallas Morison (lab)
   - Logon: `dmorison` (UPN) / `dmorison` (pre-Windows 2000)
4. Set a lab password (optionally enable lab-only setting: **Password never expires**)

Result: Domain user exists and can log in.

### 2) Verify domain login on the Windows 11 client
Open PowerShell and run:

```powershell
whoami
echo $env:USERDOMAIN
echo $env:LOGONSERVER
gpresult /r
