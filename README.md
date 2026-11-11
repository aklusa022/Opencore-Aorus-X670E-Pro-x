# OpenCore EFI: Gigabyte X670E AORUS PRO X + AMD Ryzen 9 7900X

Hackintosh EFI documentation and configuration details for running **macOS Sequoia (15.3+)** on the **Gigabyte X670E AORUS PRO X**.

---

## ⚠️ Critical Compatibility Notes & Blacklist

### ❌ SK Hynix NVMe SSD Blacklist
* **STRICTLY BLACKLISTED:** SK Hynix NVMe drives (including PC711, Gold P31, Platinum P41) and OEM drives using Hynix controllers (e.g., certain Alienware/Dell OEM drives).
* **Reason:** Hynix controllers have severe incompatibility with macOS storage drivers, causing trim crashes, kernel panics, degraded I/O speeds, and boot loops.
* **Support Notice:** **Zero support will be provided if you attempt to install or boot this EFI on a system containing an SK Hynix SSD.** Replace with a compatible drive (e.g., Western Digital Black SN770/SN850X, Samsung 980 Pro with updated firmware, or Kingston).

### ❌ Native Wi-Fi 7 / Onboard Bluetooth
* The onboard Wi-Fi 7 module (MediaTek MT7925 / Qualcomm FastConnect 7800 depending on board revision) is **completely unsupported** in macOS due to missing drivers and kernel infrastructure for Wi-Fi 7 chips.
* Native onboard Bluetooth is disabled in `config.plist`.

### 🔌 Bluetooth Solution (ASUS USB-BT400)
* **Replacement:** Onboard Bluetooth is replaced with an **ASUS USB-BT400** dongle (Broadcom BCM20702A0 chipset).
* **Kext Setup:** Driven natively via `BlueToolFixup.kext` (included in `BrcmPatchRAM`).

---

## 💻 System Hardware Specifications

| Component | Hardware Model | Status / Notes |
| :--- | :--- | :--- |
| **CPU** | AMD Ryzen 9 7900X (12-Core / 24-Thread) | Supported (`AMD-Vanilla` patches applied) |
| **Motherboard** | Gigabyte X670E AORUS PRO X | AM5 Socket / X670E Chipset |
| **GPU** | AMD Radeon RX 6800 XT (16GB) | Natively Supported (`agdpmod=pikera`) |
| **RAM** | DDR5 6000MHz | Fully supported |
| **Storage** | NVMe (Non-Hynix) | Supported |
| **Audio** | Realtek ALC1220-VB | Driven via `AppleALC.kext` |
| **Ethernet** | Realtek RTL8125 2.5GbE | Driven via `LucyRTL8125Ethernet.kext` |
| **Wi-Fi** | Onboard Wi-Fi 7 | **Disabled** (Unsupported) |
| **Bluetooth** | ASUS USB-BT400 (USB Dongle) | Supported via `BlueToolFixup.kext` |
| **Target OS** | macOS Sequoia (15.3+) | Tested & Verified |

---

## ⚙️ Kext Configuration & Order

Ensure kexts are loaded in the following logical order within your `config.plist` under `Kernel -> Add`:

1. `Lilu.kext` (Arbitrator)
2. `VirtualSMC.kext` (SMC Emulator)
3. `SMCAMDProcessor.kext` (AMDRyzenCPUPowerManagement)
4. `AMDRyzenCPUPowerManagement.kext`
5. `WhateverGreen.kext` (GPU driver patcher)
6. `AppleALC.kext` (Audio driver)
7. `LucyRTL8125Ethernet.kext` (2.5GbE LAN)
8. `BlueToolFixup.kext` (Bluetooth enabler for macOS Monterey & newer)
9. `BrcmFirmwareData.kext` (Firmware injection for ASUS BT-400)
10. `BrcmPatchRAM3.kext` (RAM patcher for ASUS BT-400)
11. `USBMap.kext` (Custom USB Port Map)

---

## 🔌 USB Port Mapping (Strict 15-Port Limit)

macOS enforces a **15-port limit per controller**. AM5 platforms feature multiple USB controllers (CPU internal + X670E Promontory chipsets).

* Custom USB mapping has been compiled using `USBToolBox` / `USBMap`.
