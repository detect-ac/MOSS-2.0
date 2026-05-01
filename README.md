# MOSS 2.0

**MOSS 2.0** is a complete rewrite of [MOSS](https://nohope.eu/) (Match Observation & Statistical System) built specifically for **Rainbow Six Siege**. Developed by [detect.ac](https://detect.ac/), it provides real-time integrity monitoring during competitive matches to ensure a fair playing environment.

MOSS 2.0 runs as a lightweight Windows application that monitors your system while you play. When the session ends, it generates a tamper-proof report file that can be shared with match organisers or opponents to prove the session was clean.

---

## Features

### Pre-Monitoring System Checks
Before monitoring begins, MOSS 2.0 verifies that your system meets security requirements:
- **Secure Boot** is enabled
- **TPM 2.0** is present and active
- **HVCI (Memory Integrity)** is enabled to prevent driver hijacking
- **Test Signing Mode** is disabled (commonly abused to load unsigned cheat drivers)
- **RainbowSix.exe** is not already running (must be launched during the monitored session)

### Process Monitoring
- Takes a snapshot of all running processes at session start
- Continuously monitors for **new processes** launching during the session
- Verifies **digital signatures** on all new executables - unsigned or suspiciously signed processes are flagged
- **RainbowSix.exe validation** - when Siege launches, the executable's certificate is verified against the official **UBISOFT ENTERTAINMENT INC.** signer. A fake or unsigned `RainbowSix.exe` is immediately flagged
- Logs a single start and end event for Siege, even though the process may restart internally

### Handle Protection (Not Much Point As Firstly Kernel Can Bypass This, And BattleEye Handles This Already By Using ObRegisterCallbacks And Several Others)
- Monitors all system handles opened to the `RainbowSix.exe` process
- **Strips dangerous access rights** (`VM_READ`, `VM_WRITE`, `VM_OPERATION`, `CREATE_THREAD`, `DUP_HANDLE`, `SUSPEND_RESUME`) from untrusted processes attempting to access Siege's memory
- Whitelists trusted sources by path (`C:\Windows\`, `C:\Program Files\`, etc.) and by name (e.g. `discord.exe`) to avoid interfering with legitimate software or BattlEye

### DMA Hardware Detection
- Scans the **PCI bus** for signs of spoofed DMA cheating hardware
- Runs at session start **and** whenever a new PCI device is connected mid-session

### Device Monitoring
- Tracks all device connections and disconnections throughout the session
- Groups rapid device events into a single clean log entry
- Reports device name, Vendor ID, and Product ID

### Session Reports
When you click **Stop Monitoring**, MOSS 2.0 automatically saves a `.moss` report file to your Desktop containing the full timestamped session log.

---

## Report Verification

Every `.moss` report includes a **SHA-256 HMAC integrity signature** at the bottom of the file. It computationally impossible to forge.

If anyone modifies even a single character of the report, the signature will no longer match.

### How to Verify a Report

**In the app (recommended):**
1. Open MOSS 2.0
2. Click the **Verify Report** button in the top-right corner
3. Select the `.moss` file you received
4. The console will display whether the report is **authentic** or **tampered with**
5. You can verify multiple reports in a row without restarting

**Via command line:**
```
MOSS2.0.exe /verify "C:\Path\To\Report.moss"
```
A message box will confirm whether the report passed or failed verification.

---

## Requirements

- Windows 10 or later
- Administrator privileges
- Secure Boot, TPM 2.0, and HVCI enabled
- Test Signing Mode disabled

---

## License

All rights reserved. MOSS 2.0 is developed and maintained by [detect.ac](https://detect.ac/).
