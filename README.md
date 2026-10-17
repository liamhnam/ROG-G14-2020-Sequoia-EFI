# ROG Zephyrus G14 2020 (GA401IHR) – OpenCore EFI for macOS Sequoia

OpenCore EFI running macOS **Sequoia 15.1** on the **ASUS ROG Zephyrus G14 2020 – GA401IHR**.

Based on [PIut02/ROG-Zephyrus-G14-GA401-Hackintosh](https://github.com/PIut02/ROG-Zephyrus-G14-GA401-Hackintosh) (`2020/EFI` folder, commit `37edc25`). All of the original work belongs to PIut02 and the authors of the kexts and OpenCore. This repo only adds the changes proven necessary on a GA401IHR running Sequoia.

## Hardware

| Component | Model |
| :-- | :-- |
| Laptop | ASUS ROG Zephyrus G14 GA401IHR (BIOS 416) |
| CPU | AMD Ryzen 7 4800HS (Renoir, 8 cores) |
| iGPU | AMD Radeon Vega (NootedRed), 2 GB UMA |
| dGPU | NVIDIA GTX 1650 (not usable on macOS) |
| Audio | Realtek ALC285 (layout-id 33) |
| Wi-Fi / Bluetooth | Intel Dual Band Wireless-AC 8265 |
| SSD | Micron 3400 512 GB NVMe |
| Touchpad | ELAN1201 (I2C HID) |

## Software

- OpenCore **1.0.4** (unchanged from the original repo)
- macOS **Sequoia 15.1**
- SMBIOS: `MacBookPro16,3`

## Changes from PIut02 `2020/EFI`

Apart from the 5 changes below, every file is **byte-for-byte identical** to the original repo.

| # | Change | Reason (verified on this machine) |
| :-- | :-- | :-- |
| 1 | `Kernel → Patch`: replaced all AMD patches with [AMD_Vanilla](https://github.com/AMD-OSX/AMD_Vanilla) (commit `eaf52ef`), core count = `8`, kept the author's PAT choice (Shaneee) | The original patches have `MaxKernel 23.99.99` (Sonoma only), so Sequoia (Darwin 24) hangs at `EB\|LOG:EXITBS:START` |
| 2 | `ACPI → Add`: disabled `SSDT-NoHybGfx.aml` (file kept) | Hang at `[ PCI configuration begin ]`; disabling this SSDT was the only change before the first successful boot |
| 3 | `Kernel → Add`: disabled `AirportItlwm.kext` (file kept) | OpenCore reports `Prelinked injection AirportItlwm.kext - Invalid Parameter`; v2.3.0 has no Sequoia build |
| 4 | `Kernel → Add`: added `itlwm.kext` v2.3.0 | Wi-Fi on Sequoia, used together with the [HeliPort](https://github.com/OpenIntelWireless/HeliPort/releases) app |
| 5 | `PlatformInfo → Generic`: SMBIOS filled in | The original repo leaves it empty for users to generate |

> ⚠️ **The SMBIOS in `config.plist` belongs to this machine.** If you use this EFI on another machine, **you must generate a new Serial / MLB / UUID / ROM** (GenSMBIOS / macserial, model `MacBookPro16,3`). Sharing an SMBIOS causes iCloud / iMessage problems on both machines.

## BIOS (set via [Smokeless UMAF](https://github.com/DavidS95/Smokeless_UMAF))

- `AMD CBS → NBIO → GFX Configuration`: iGPU `UMA_SPECIFIED`, **UMA Frame Buffer Size = 2G**
- `PCI Subsystem Settings`: **Above 4G Decoding = Enabled** (do not combine with `npci=` in boot-args)
- **Secure Boot = Off**, **Fast Boot = Off**
- After replacing the EFI: select **Reset NVRAM** in the OpenCore picker once

## Known issues

- Wi-Fi through itlwm needs the HeliPort app; macOS treats the connection as Ethernet
- The GTX 1650 is always on (SSDT-NoHybGfx is not used), so battery drains faster
- The USB-C port cannot output video (it is wired to the dGPU); use HDMI
- Not yet fully verified on this machine: Wi-Fi (itlwm), Bluetooth, ALC285 audio, sleep/wake

## Credits

- [PIut02](https://github.com/PIut02/ROG-Zephyrus-G14-GA401-Hackintosh) – original EFI for the G14 GA401
- [Acidanthera](https://github.com/acidanthera) – OpenCore, Lilu, VirtualSMC, AppleALC, …
- [AMD-OSX](https://github.com/AMD-OSX/AMD_Vanilla) – AMD kernel patches
- [ChefKissInc](https://github.com/ChefKissInc) / [htmambo](https://github.com/htmambo) – NootedRed
- [OpenIntelWireless](https://github.com/OpenIntelWireless) – itlwm, HeliPort, IntelBluetoothFirmware
- [Dortania](https://dortania.github.io/) – OpenCore guides
