# Lab — Home PC Refresh + Data Migration (Windows 10 → Windows 11)

## Issue
A family member’s ASUS all-in-one (Windows 10) had severe performance problems: constant 100% disk usage, extremely slow boot, and applications taking several minutes to launch. The user also needed a reliable migration path to a newer Windows 11 system.

## Environment
- Old device: ASUS all-in-one (Windows 10)
- New device: Dell desktop (Windows 11, 16GB RAM, 1TB storage, upgrade-friendly)
- Backup device: 2TB external SSD (USB)
- Peripherals: Dual monitors + USB printer

## Goal
1) Back up user data safely before making any changes  
2) Provision a replacement Windows 11 system  
3) Restore required files while keeping the external SSD as a long-term backup  
4) Configure dual monitors and printer with functional verification

---

## Troubleshooting / Actions Taken

### 1) Choose a safe migration strategy (backup-first)
- Recommended the user purchase a 2TB external SSD to:
  - hold a full copy of the existing data
  - remain available for ongoing backup after migration

### 2) Copy all files from the old PC to the external SSD (robust copy)
Performed a full data copy to the external SSD. The transfer ran for an extended period due to file volume.

Tool used: `robocopy` (copy, not move)

Example command (representative of what was used):

    robocopy "C:\Users\USERNAME" "E:\PC-Backup-ASUS-AIO\Users\USERNAME" /E /COPY:DAT /R:1 /W:1 /XJ /LOG:"E:\PC-Backup-ASUS-AIO\copy-log.txt"

Notes:
- `/E` copies subfolders including empty ones
- `/COPY:DAT` copies Data, Attributes, Timestamps
- `/R:1 /W:1` reduces retry time on problematic files
- `/XJ` avoids junction loops
- `/LOG` writes a copy log for review

### 3) Validate backup integrity
- Verified that the external SSD contained the user’s folders/files
- Asked the user to confirm critical files were present (user validation step)
- Powered off old PC after confirmation and successful migration

### 4) Provision the new Windows 11 device
- Completed Windows 11 initial setup:
  - Microsoft account sign-in
  - PIN creation for login
- Installed updates until fully current

### 5) Configure dual monitors to user preference
- Connected two monitors
- Adjusted display arrangement and primary monitor based on user preference

### 6) Restore files to the new device (keep SSD as backup)
- Connected external SSD to the new PC
- Copied required files onto the new computer
- Instructed the user to COPY files (not MOVE) so the SSD stays as a backup

### 7) Printer setup + verification
Problem: Printer was not detected initially after connecting.

Fix:
- Checked Windows Settings for device recognition
- Ensured the printer appeared under printers/devices once recognized
- Printed a test page to confirm successful installation

---

## Resolution
- User successfully migrated from Windows 10 ASUS AIO to Windows 11 Dell desktop
- Data was backed up to a 2TB external SSD and restored via copy
- Dual monitors configured to user preference
- Printer installed and validated by test print
- User continues using the system without recurring issues

## Prevention / Recommendations
- Maintain the external SSD as an ongoing backup target (periodic sync)
- Keep Windows Updates enabled and applied regularly
- If performance issues appear in the future: review startup apps, available storage, and device health

## Lessons Learned
- Backup-first migration prevents data-loss risk and reduces stress for the user
- “Copy, don’t move” keeps a safety net available after the migration
- Always verify peripherals with a functional test (test page for printers)
