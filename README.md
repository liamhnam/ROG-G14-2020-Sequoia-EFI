# ROG Zephyrus G14 2020 (GA401IHR) – OpenCore EFI for macOS Sequoia

EFI chạy macOS **Sequoia 15.1** trên **ASUS ROG Zephyrus G14 2020 – GA401IHR**.

Dựa trên [PIut02/ROG-Zephyrus-G14-GA401-Hackintosh](https://github.com/PIut02/ROG-Zephyrus-G14-GA401-Hackintosh) (thư mục `2020/EFI`, commit `37edc25`). Toàn bộ công sức gốc thuộc về PIut02 và các tác giả kext/OpenCore. Repo này chỉ thêm những thay đổi đã được chứng minh là cần thiết trên máy GA401IHR chạy Sequoia.

## Phần cứng

| Linh kiện | Model |
| :-- | :-- |
| Máy | ASUS ROG Zephyrus G14 GA401IHR (BIOS 416) |
| CPU | AMD Ryzen 7 4800HS (Renoir, 8 nhân) |
| iGPU | AMD Radeon Vega (NootedRed), UMA 2 GB |
| dGPU | NVIDIA GTX 1650 (không dùng được trên macOS) |
| Âm thanh | Realtek ALC285 (layout-id 33) |
| Wi-Fi / Bluetooth | Intel Dual Band Wireless-AC 8265 |
| SSD | Micron 3400 512 GB NVMe |
| Touchpad | ELAN1201 (I2C HID) |

## Phần mềm

- OpenCore **1.0.4** (giữ nguyên như repo gốc)
- macOS **Sequoia 15.1**
- SMBIOS: `MacBookPro16,3`

## Các thay đổi so với PIut02 `2020/EFI`

Ngoài 5 thay đổi dưới đây, mọi file đều **giống hệt từng byte** với repo gốc.

| # | Thay đổi | Lý do (đã kiểm chứng trên máy này) |
| :-- | :-- | :-- |
| 1 | `Kernel → Patch`: thay toàn bộ bản vá AMD bằng [AMD_Vanilla](https://github.com/AMD-OSX/AMD_Vanilla) (commit `eaf52ef`), số nhân = `8`, giữ lựa chọn PAT (Shaneee) của tác giả | Bản vá gốc có `MaxKernel 23.99.99` (chỉ tới Sonoma) → Sequoia (Darwin 24) treo tại `EB\|LOG:EXITBS:START` |
| 2 | `ACPI → Add`: tắt `SSDT-NoHybGfx.aml` (file vẫn giữ) | Treo tại `[ PCI configuration begin ]`; tắt SSDT này là thay đổi duy nhất trước lần boot thành công |
| 3 | `Kernel → Add`: tắt `AirportItlwm.kext` (file vẫn giữ) | OpenCore báo `Prelinked injection AirportItlwm.kext - Invalid Parameter`; v2.3.0 không có bản cho Sequoia |
| 4 | `Kernel → Add`: thêm `itlwm.kext` v2.3.0 | Wi-Fi cho Sequoia, dùng kèm app [HeliPort](https://github.com/OpenIntelWireless/HeliPort/releases) |
| 5 | `PlatformInfo → Generic`: đã điền SMBIOS | Repo gốc để trống, người dùng tự tạo |

> ⚠️ **SMBIOS trong `config.plist` là của máy này.** Nếu bạn dùng EFI này cho máy khác, **phải tạo Serial / MLB / UUID / ROM mới** (GenSMBIOS / macserial, model `MacBookPro16,3`). Dùng trùng SMBIOS sẽ gây lỗi iCloud / iMessage cho cả hai máy.

## BIOS (thiết lập qua [Smokeless UMAF](https://github.com/DavidS95/Smokeless_UMAF))

- `AMD CBS → NBIO → GFX Configuration`: iGPU `UMA_SPECIFIED`, **UMA Frame Buffer Size = 2G**
- `PCI Subsystem Settings`: **Above 4G Decoding = Enabled** (không dùng kèm `npci=` trong boot-args)
- **Secure Boot = Off**, **Fast Boot = Off**
- Sau khi thay EFI: chọn **Reset NVRAM** trong menu OpenCore một lần

## Đã biết

- Wi-Fi qua itlwm cần app HeliPort; macOS sẽ coi kết nối như Ethernet
- GTX 1650 luôn bật (do không dùng SSDT-NoHybGfx) → hao pin hơn
- Cổng USB-C không xuất hình được (nối thẳng vào dGPU), dùng HDMI
- Các mục chưa kiểm chứng đầy đủ trên máy này: Wi-Fi (itlwm), Bluetooth, âm thanh ALC285, sleep/wake

## Credits

- [PIut02](https://github.com/PIut02/ROG-Zephyrus-G14-GA401-Hackintosh) – EFI gốc cho G14 GA401
- [Acidanthera](https://github.com/acidanthera) – OpenCore, Lilu, VirtualSMC, AppleALC, …
- [AMD-OSX](https://github.com/AMD-OSX/AMD_Vanilla) – bản vá kernel AMD
- [ChefKissInc](https://github.com/ChefKissInc) / [htmambo](https://github.com/htmambo) – NootedRed
- [OpenIntelWireless](https://github.com/OpenIntelWireless) – itlwm, HeliPort, IntelBluetoothFirmware
- [Dortania](https://dortania.github.io/) – hướng dẫn OpenCore
