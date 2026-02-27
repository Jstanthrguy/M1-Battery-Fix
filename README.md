# M1-Battery-Fix
# Monstatek M1 Battery Calibration & Fast Charge Fix (v0.800 Patch)

This repository contains a community-developed binary patch for the Monstatek M1. The factory firmware (v0.800) contains incorrect power management parameters that cause erratic battery percentage readings and prevent the device from reaching a true 100% charge.

## The Problem
* **Incorrect Capacity:** Factory firmware is hardcoded for a smaller battery, causing the fuel gauge to jump wildly.
* **Aggressive Taper Current:** The charge termination is set to 240mA. On the actual 2100mAh cell, this stops charging before the battery is actually full.
* **Limited Charge Rate:** The standard firmware limits input current, resulting in slow charging.

## The Fix
This is a binary hex-patch of the official `MonstaTek_M1_v0800.elf` which modifies the BQ27421 fuel gauge initialization:
* **Corrected Capacity:** Set to 2100mAh to match the physical cell.
* **Corrected Taper Current:** Lowered to 105mA (C/20) for a complete, healthy charge cycle.
* **Fast Charge Enabled:** Increased `ICHG` limits to allow up to 2A charging.

## Installation Instructions
1. Download `MonstaTek_M1_Custom.bin` from this repository.
2. Copy the file to the root of a FAT32-formatted MicroSD card.
3. Insert the SD card into your Monstatek M1.
4. Navigate to **Firmware Update** in the device menu.
5. Select `MonstaTek_M1_Custom.bin` and follow the on-screen prompts.

**Disclaimer:** Use this patch at your own risk. This is an unofficial community fix and is not affiliated with Monstatek.

## How to Revert
If you wish to return to the factory power settings:
1. Download the original v0.800 firmware from the MonstaTek official repository or website.
2. Follow the same installation steps above using the original `.bin` file.
3. The BQ27421 fuel gauge will reset to factory parameters upon the next full charge cycle.

### 🛠 Technical Details (for Verification)
Since the official source is currently uncompilable due to missing headers, this fix was applied via hex-patching the v0.800 binary.
- **Target Chip:** BQ27421 Fuel Gauge via STM32 I2C bus.
- **Modified Parameter 1 (Design Capacity):** Changed from `0xXXXX` (incorrect) to `0x0834` (2100mAh).
- **Modified Parameter 2 (Taper Current):** Changed from `0x00F0` (240mA) to `0x0069` (105mA).
- **Checksum:** Re-calculated using `srec_cat` to ensure the internal CRC matches the new data.
