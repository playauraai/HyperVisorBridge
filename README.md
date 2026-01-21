# HyperVisorBridge

An in-memory AMD SVM (Ring -1) execution bridge launched via a Windows kernel driver.  
The hypervisor core is intentionally minimal and frozen; all logic is driven externally through a shared **VMCB mailbox**.

> **Educational / research use only.** This is not intended for production, cheating, or any malicious activity. Use at your own risk — kernel-mode code can cause system instability, data loss, or hardware damage.

## Features
- Driver-based launch of a lightweight AMD SVM hypervisor (Ring -1)
- In-memory execution (no persistent files on disk)
- Shared VMCB mailbox for external command dispatch (read/write physical memory, hypercalls, etc.)
- Minimal frozen core — logic lives outside the hypervisor

## Architecture Overview
Uses a signed/unsigned kernel driver to set up and enter VMX/SVM root mode, then bridges commands via VMCB guest-host shared area.

![Architecture Diagram](https://github.com/user-attachments/assets/619dcc23-660a-466d-8a14-4fa437ce6dcf)
*High-level flow: Driver → VMRUN → Ring -1 mailbox handling*

![Screenshot / Demo](https://github.com/user-attachments/assets/33eb81a0-888a-470a-897a-0a66979134d1)
*Example execution log / VMCB state*

## Requirements
- AMD CPU with **SVM** enabled in BIOS (most Zen 2+ CPUs)
- Windows 10 / 11 (64-bit)
- Visual Studio 2022+ with WDK (Windows Driver Kit) for building the driver
- Administrator privileges
- **HVCI / Memory Integrity disabled** for testing (this is a hypervisor — it conflicts by design)

## Build & Setup

1. Clone the repo:
   ```bash
   git clone https://github.com/playauraai/HyperVisorBridge.git
   cd HyperVisorBridge
   ```
2. Open the solution in Visual Studio 2022+ (with WDK installed).

3. Build the driver project (x64 Release recommended).

4. Load and test (requires admin privileges + test signing or valid cert):
   - Enable test signing: `bcdedit /set testsigning on` (reboot)
   - Create/start service: `sc create HyperVisorBridge binPath= "C:\path\to\HyperVisorBridge.sys" type= kernel`  
     `sc start HyperVisorBridge`

**Notes on stability & release status:**
- This is a private/research prototype — **no public release planned**. The core logic and mailbox bridge are fully implemented and available here for study.
- **Fully stable in testing**: No BSODs, no watchdog timeouts, no kernel-mode crashes/issues observed. Runs cleanly in memory for hours until Windows shutdown.
- Designed as an in-memory bridge — no disk writes after load.

**Warning:** Kernel drivers and hypervisors are inherently risky. Test in a VM first, disable HVCI/Memory Integrity, and be prepared for instability or hardware conflicts.
```
