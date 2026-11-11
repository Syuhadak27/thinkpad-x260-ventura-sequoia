<div align="center">

# ThinkPad X260 · Ventura & Sequoia

EFI OpenCore untuk ThinkPad X260 (Skylake). Satu folder EFI, dipakai untuk dua versi macOS.

![macOS](https://img.shields.io/badge/macOS-Ventura%2013%20%7C%20Sequoia%2015-000000?style=flat-square&logo=apple&logoColor=white)
![OpenCore](https://img.shields.io/badge/bootloader-OpenCore-2f81f7?style=flat-square)
![ThinkPad](https://img.shields.io/badge/laptop-ThinkPad%20X260-e2231a?style=flat-square&logo=lenovo&logoColor=white)

</div>

---

## Apa ini?

Ini EFI OpenCore yang saya pakai di ThinkPad X260 untuk menjalankan **macOS Ventura 13** dan **macOS Sequoia 15**. Kext Ventura dan Sequoia dipisah lewat `MinKernel` / `MaxKernel`, jadi tidak perlu ganti `config.plist` tiap mau pindah sistem.

Yang sudah jalan: grafis, audio, keyboard, trackpad, baterai, brightness, USB, Ethernet, sleep/wake, dan card reader. Yang belum: **Bluetooth** (dua-duanya) dan **WiFi di Sequoia** yang harus lewat HeliPort.

## Spek

| Komponen | Detail |
|---|---|
| Laptop | Lenovo ThinkPad X260 |
| CPU | Intel Core i5-6300U, 2C/4T, 2.4–3.0 GHz (Skylake-U) |
| iGPU | Intel HD Graphics 520 |
| RAM | 8/16 GB DDR4-2133 SODIMM (single slot) |
| Storage | SSD SATA 2.5" / NVMe M.2 (tergantung unit) |
| Layar | 12.5" IPS FHD 1920×1080 (ada varian HD 1366×768) |
| WiFi | Intel Dual Band Wireless-AC 8260 |
| Ethernet | Intel I219-LM |
| Audio | Realtek ALC3245, layout-id 11 |
| Card reader | Realtek RTS522A |
| Kamera | HD 720p |
| Port | 3× USB 3.0, HDMI, mini DisplayPort, RJ45, audio 3.5 mm, SD |
| Baterai | Internal + external (47/68 Wh) |
| SMBIOS | MacBookPro15,2 |
| Bootloader | OpenCore |

Beberapa unit X260 keluar dengan i3-6100U atau i7-6600U. EFI-nya tetap sama, yang beda cuma hasil benchmark. :)

## Status

| Fitur | Ventura 13 | Sequoia 15 | Catatan |
|---|:---:|:---:|---|
| Grafis / QE-CI | ✅ | ✅ | HD 520, framebuffer patch |
| Audio | ✅ | ✅ | AppleALC, layout-id 11 |
| Keyboard | ✅ | ✅ | VoodooPS2 |
| Trackpad | ✅ | ✅ | VoodooRMI + VoodooSMBus |
| Baterai | ✅ | ✅ | SMCBatteryManager + ECEnabler |
| Brightness | ✅ | ✅ | AppleBacklightSmoother + BrightnessKeys |
| Sleep / Wake | ✅ | ✅ | HibernationFixup + RTCMemoryFixup |
| USB | ✅ | ✅ | USBMap |
| Ethernet | ✅ | ✅ | IntelMausi |
| SD Card | ✅ | ✅ | RealtekCardReader |
| Webcam | ✅ | ✅ | USB |
| WiFi | ✅ Native | ⚠️ HeliPort | Lihat bagian WiFi |
| Bluetooth | ❌ | ❌ | Kedetect, tapi tidak mau connect |
| Fingerprint | ❌ | ❌ | Tidak ada driver |
| AirDrop / Continuity | ❌ | ❌ | Keterbatasan WiFi Intel |

## WiFi

**Ventura** — pakai `AirportItlwm-Ventura.kext`. WiFi langsung muncul di menu bar dan connect seperti Mac asli. Tidak perlu aplikasi tambahan.

**Sequoia** — pakai `itlwm-Sequoia.kext` + [HeliPort](https://github.com/OpenIntelWireless/HeliPort). WiFi **tidak akan muncul di menu bar**; scan dan connect dilakukan dari HeliPort. Ini memang cara kerjanya, bukan bug.

> Kalau di Sequoia WiFi terlihat mati atau tidak ada jaringan, buka HeliPort(file terlampir)

## Bluetooth

Belum jalan di dua-duanya. Kext `IntelBluetoothFirmware`, `IntelBTPatcher`, dan `BlueToolFixup` sudah dimuat, adapter juga kedetect, tapi tetap tidak mau connect atau pairing. Sudah coba beberapa kombinasi, hasilnya sama.

Kalau butuh Bluetooth, pakai dongle USB yang didukung macOS. Untuk sekarang jangan buang waktu reinstall kext atau reset NVRAM terus-terusan.

## Screenshot

### Ventura 13

| | |
|:---:|:---:|
| ![Ventura 1](image/ventura/ventura_1.jpg) | ![Ventura 2](image/ventura/ventura_2.jpg) |
| ![Ventura 3](image/ventura/ventura_3.jpg) | ![Ventura 4](image/ventura/ventura_4.jpg) |

### Sequoia 15

| | |
|:---:|:---:|
| ![Sequoia 1](image/sequoia/sequoia_1.jpg) | ![Sequoia 2](image/sequoia/sequoia_2.jpg) |
| ![Sequoia 3](image/sequoia/sequoia_3.jpg) | ![Sequoia 4](image/sequoia/sequoia_4.jpg) |

## Isi EFI

```text
EFI/
├── BOOT/
│   └── BOOTx64.efi
└── OC/
    ├── ACPI/          # 14 SSDT: PLUG, EC, PNLF, BATX, XHCI, dll.
    ├── Drivers/       # HfsPlus, OpenRuntime, OpenCanopy, AudioDxe, ...
    ├── Kexts/
    ├── Resources/     # tema OpenCanopy Acidanthera GoldenGate
    ├── OpenCore.efi
    ├── config.plist
    └── oldConfig.plist
```

Kext utama yang dipakai:

| Fungsi | Kext |
|---|---|
| Core | Lilu 1.7.2, VirtualSMC 1.3.8 |
| Grafis | WhateverGreen 1.7.1 |
| Audio | AppleALC 1.9.8 |
| WiFi Ventura | AirportItlwm 2.3.0 |
| WiFi Sequoia | itlwm 2.3.0 |
| Ethernet | IntelMausi 1.0.8 |
| Bluetooth | IntelBluetoothFirmware + IntelBTPatcher + BlueToolFixup |
| Baterai | SMCBatteryManager + ECEnabler |
| Trackpad | VoodooRMI 1.4.3 + VoodooSMBus 3.0 |
| Keyboard | VoodooPS2Controller 2.3.7 |
| Card reader | RealtekCardReader 0.9.7 |
| CPU power | CPUFriend 1.3.1 + CPUFriendDataProvider |
| Sleep | HibernationFixup 1.4.9 + RTCMemoryFixup 1.0.8 |

## Catatan config

- `boot-args` cuma `keepsyms=1`.
- `SecureBootModel` diset `Disabled`.
- SIP aktif secara default (`csr-active-config` = `00000000`). Bisa di-toggle dari menu picker lewat `ToggleSipEntry.efi`.
- SMBIOS `MacBookPro15,2`. Serial, MLB, UUID, dan ROM di `config.plist` sebaiknya diganti punya sendiri sebelum pakai iCloud atau iMessage.
- Kext Ventura dibatasi kernel `22.x`, Sequoia `24.x`. Karena itu dua versi bisa tinggal di satu EFI.
- Picker pakai OpenCanopy dengan tema GoldenGate, timeout 5 detik.

## Sebelum dipakai

1. Backup EFI yang sekarang.
2. Mount partisi EFI, copy folder `EFI` ke root partisi.
3. Generate SMBIOS baru kalau perlu iCloud/iMessage.
4. Di BIOS: UEFI only, Secure Boot off, VT-d off. Kalau CFG Lock masih aktif, biarkan `AppleXcpmCfgLock` menyala.
5. Khusus Sequoia, install HeliPort dulu sebelum tes WiFi.

## Credit

Terima kasih ke Acidanthera (OpenCore, Lilu, VirtualSMC, WhateverGreen, AppleALC), OpenIntelWireless (AirportItlwm, itlwm, HeliPort), OpenCore Legacy Patcher, dan semua developer kext yang dipakai di sini.

---
