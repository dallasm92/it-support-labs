# Windows Server File Share + Map Network Drive (AD Domain)

## Issue
Need a centralized file share on a Windows Server for domain users, and a mapped drive on a Windows 11 domain-joined client with verified read/write access.

## Environment
- Hyper-V lab environment
- Domain: lab.local
- Domain user: LAB\dmorison (created in OU "Lab Users")
- Server: Windows Server 2022 (File Share hosted locally)
- Client: Windows 11 (domain-joined, logged in as domain user)
- Shared folder (local path): C:\Shares\LabShare
- Share name: LabShare
- UNC path used (example): \\<SERVERNAME>\LabShare
- Mapped drive letter: L:

> Note: Public documentation should avoid leaking real hostnames/internal IPs. In this write-up, replace your server hostname with a neutral label like DC01.

---

## Troubleshooting / Implementation

### 1) Create a domain user (ADUC)
1. Open **Active Directory Users and Computers**
2. Go to: `lab.local` → `Lab Users` OU
3. Create new user:
   - First/Last: Dallas Morison
   - User logon name: dallas.morison (UPN) / dmorison (pre-Windows 2000)
4. Set a lab password and (lab-only) optionally enable:
   - Password never expires

Result: Domain user created successfully in the OU.

---

### 2) Verify domain login on the Windows 11 client
Open PowerShell and run:

```powershell
whoami
echo $env:USERDOMAIN
echo $env:LOGONSERVER
gpresult /r

Expected indicators:

whoami shows LAB\dmorison

USERDOMAIN shows LAB

LOGONSERVER shows your domain controller hostname

gpresult /r shows the domain + user context

3) Create and share the folder on the Server

Create folder:

C:\Shares\LabShare

Share the folder:

Right-click folder → Properties → Sharing → Advanced Sharing

Check: Share this folder

Share name: LabShare

Set Share Permissions (SMB share permissions)
For lab simplicity this can be broad, but best practice is to assign a security group.

Example lab approach used:

Add Authenticated Users

Allow: Change / Read (or Full Control in a lab)

Set NTFS permissions (Security tab)

Confirm permissions allow the intended access.

Example lab approach used:

Authenticated Users: Modify / Read & execute / List / Read (as needed)

Important concept:

Effective access is the most restrictive combination of Share + NTFS permissions.

4) Map the drive on the Windows 11 client

Option 1 (GUI):

File Explorer → This PC → Map network drive

Drive letter: L:

Folder: \\<SERVERNAME>\LabShare

Enable: Reconnect at sign-in

Finish

Option 2 (PowerShell):

New-PSDrive -Name L -PSProvider FileSystem -Root "\\<SERVERNAME>\LabShare" -Persist


If you see: “The local device name is already in use”

The drive letter is already mapped. Verify and/or remove it (see Troubleshooting section).

Verify mapping:

Get-PSDrive L
net use

5) Verify read/write access (proof)

Create a folder and file on the mapped drive:

mkdir L:\_test
"hello from LAB\dmorison" | Out-File L:\_test\proof.txt
dir L:\_test


Validation:

Folder/file appear on the server under C:\Shares\LabShare\_test

Confirms SMB share access + NTFS permissions are working for the domain user

Resolution

Created a Windows Server SMB share and successfully mapped it as drive L: on a Windows 11 domain client. Verified end-to-end read/write by creating a directory and writing a test file from the client, then confirming it appeared on the server.

Prevention / Notes (what I’d do next)

Replace “Authenticated Users” broad access with a dedicated security group:

LAB\LabShare_RW (Modify)

LAB\LabShare_RO (Read)

Keep Share permissions simple (e.g., Change for RW group) and enforce detail with NTFS.

Document naming conventions:

Server: DC01 / FILE01

Client: WIN11-CLIENT1

If mapping issues happen again, clear old sessions and remap cleanly.

Troubleshooting (common issues)
Drive letter already in use
net use
net use L: /delete

Credential/session conflicts
net use * /delete

Name resolution issues

Try mapping using IP (lab-only) or verify DNS points to the DC:

Confirm client DNS is set to the domain controller

ping <SERVERNAME>

nslookup <SERVERNAME>

SMB/Firewall basics

Ensure File and Printer Sharing is allowed (Windows Firewall profiles)

Ensure the network profile isn’t blocking discovery/sharing unexpectedly

Evidence (optional later)

Screenshot: AD user created in ADUC (OU + username)

Screenshot: Share settings (Advanced Sharing + share name)

Screenshot: NTFS Security tab permissions

Screenshot: Client mapping drive in Explorer

Screenshot: PowerShell proof commands + net use output


---

### B) Update `LAB_INDEX.md`
Add a new bullet under **Windows**:

```md
- windows-server-file-share-map-drive (2026-01-06)
