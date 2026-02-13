# Lab - Windows 11 Network Printer Offline After DHCP Change

## Issue
User reported that printing suddenly stopped after a router reboot. Printer appeared "Offline" in Windows even though it was powered on and connected to Wi-Fi.

## User Impact
- User could not print invoices and shipping labels.
- Repeated failed print attempts caused workflow delays and confusion.

## Business Impact
- Time-sensitive documents could not be printed on schedule.
- IT support time increased due to repeated queue failures and retries.

## Environment
- Client OS: Windows 11 Pro
- Printer: Brother network printer (Wi-Fi)
- Network: Home/SMB LAN with DHCP from router
- Print path: Standard TCP/IP port in Windows
- Trigger event: Router reboot and DHCP lease refresh

## Troubleshooting
1. Validated basic connectivity:
   - Confirmed printer powered on and connected to Wi-Fi.
   - `ping <printer-ip>` initially failed from the Windows client.
2. Checked printer IP on device panel:
   - Printer had a new DHCP address after reboot.
3. Checked Windows printer port settings:
   - Existing TCP/IP port still pointed to the old IP.
4. Updated printer port in Windows:
   - Devices and Printers -> Printer Properties -> Ports
   - Reconfigured Standard TCP/IP port to the printer's current IP.
5. Cleared stale print queue:
   - Cancelled stuck jobs.
   - Restarted Print Spooler:
     - `net stop spooler`
     - `net start spooler`
6. Validation:
   - `ping <new-printer-ip>` successful.
   - Test page printed successfully.

## Resolution
- Updated Windows printer TCP/IP port to the printer's current DHCP address.
- Cleared queued failed jobs and restarted spooler.
- Printing restored immediately.

## Prevention / Notes
- Assign DHCP reservation for the printer in router DHCP settings.
- Keep printer documentation with hostname/IP, model, and driver version.
- During printer incidents, always verify:
  1) network connectivity, 2) current printer IP, 3) Windows port mapping, 4) spooler health.
