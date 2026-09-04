# Awesome Radxa Cubie A7A / A7Z (Allwinner A733)

> A curated index of open-source projects, drivers, and tools that work on the **Radxa Cubie A7A / A7Z / A7S** and other A733-based SBCs (Orange Pi 4 Pro, Orange Pi Zero 3W) powered by the **Allwinner A733** SoC (sun60iw2).

The Allwinner A733 is a 12nm SoC featuring 2x Cortex-A76 @ 2.0GHz + 6x Cortex-A55 @ 1.8GHz (DynamIQ big.LITTLE), Imagination BXM-4-64 MC1 GPU, Vivante VIP9000 NPU (3 TOPS @ INT8), and Allwinner Video Engine (H.264/H.265 encode 4K@30, decode 8K@24).

## Table of Contents

- [Kernels & Firmware](#kernels--firmware)
- [OS Images & Distros](#os-images--distros)
- [NPU (Neural Processing Unit)](#npu-neural-processing-unit)
- [GPU (PowerVR BXM-4-64)](#gpu-powervr-bxm-4-64)
- [VPU / Video Engine](#vpu--video-engine)
- [x86 Emulation](#x86-emulation)
- [GPIO & Hardware Interfaces](#gpio--hardware-interfaces)
- [WiFi & Bluetooth (AIC8800)](#wifi--bluetooth-aic8800)
- [Camera & Multimedia](#camera--multimedia)
- [PCIe & NVMe](#pcie--nvme)
- [Display & LCD](#display--lcd)
- [Gaming & Retro](#gaming--retro)
- [AI / Edge Applications](#ai--edge-applications)
- [Bootloader & U-Boot](#bootloader--u-boot)
- [Mainline Linux](#mainline-linux)
- [Community Projects](#community-projects)
- [Tools & Utilities](#tools--utilities)
- [3D Printed Cases](#3d-printed-cases)
- [Hardware Asset Report](#hardware-asset-report)

---

## Kernels & Firmware

| Repo | Stars | Description |
|------|-------|-------------|
| [Rabs9/radxa-cubie-a7a-kernel](https://github.com/Rabs9/radxa-cubie-a7a-kernel) | 0 | Debian 13 kernel for Cubie A7A with gigabit ethernet fix, validated CPU/RAM/GPU overclocking, GPU colour fix, local LLM and clock control |
| [Aseel1206/radxa-cubie-a7a-kernel](https://github.com/Aseel1206/radxa-cubie-a7a-kernel) | 0 | Custom Linux 6.6.98 kernel with full hardware support & overclocking for Cubie A7A |
| [Numerio/radxa-cubie-a7a-maximum-overclock](https://github.com/Numerio/radxa-cubie-a7a-maximum-overclock) | 0 | Max overclock config: NPU 2520MHz, GPU 1488MHz, CPU 2080MHz |
| [SamulKyull/linux-cubie-a7a](https://github.com/SamulKyull/linux-cubie-a7a) | 2 | Linux 6.6 kernel for Radxa Cubie A7A |
| [zuyih/linux-cubie](https://github.com/zuyih/linux-cubie) | 5 | Linux kernel for Radxa Cubie A5E/A7A/A7Z boards |
| [GameOctane/allwinner-a733-linux](https://github.com/GameOctane/allwinner-a733-linux) | 1 | Allwinner A733 Linux 6.6 BSP kernel, pre-merged for Batocera build (Radxa kernel + allwinner-bsp) |
| [skitzo2000/a7s-linux-drivers](https://github.com/skitzo2000/a7s-linux-drivers) | 1 | Out-of-tree kernel drivers, patches and device-tree overlays for Cubie A7S on Linux 6.18 |
| [opentina-org/build](https://github.com/opentina-org/build) | 0 | OpenTina build config for Allwinner A733 series SoC |
| [opentina-org/awbin](https://github.com/opentina-org/awbin) | 0 | Allwinner A733 related binary and firmware |
| [vamrs-feng/sunxi-ddrbin](https://github.com/vamrs-feng/sunxi-ddrbin) | 0 | Extract and modify embedded DDR parameter DTBs in Allwinner ddr.bin files (tested on A733/sun60iw2) |
| [parker-int64/sun60i-a733-dtoverlays](https://github.com/parker-int64/sun60i-a733-dtoverlays) | 0 | Collection of device tree overlays for Allwinner A733 platform |

## OS Images & Distros

| Repo | Stars | Description |
|------|-------|-------------|
| [radxa-build/radxa-cubie-a7a](https://github.com/radxa-build/radxa-cubie-a7a) | 14 | Official Radxa Cubie A7A system images (now under `radxa-a733`) |
| [radxa-build/radxa-cubie-a7z](https://github.com/radxa-build/radxa-cubie-a7z) | 14 | Official Radxa Cubie A7Z system images (now under `radxa-a733`) |
| [cuihuir/radxa-a7z-debian12](https://github.com/cuihuir/radxa-a7z-debian12) | 10 | Ready-to-flash Debian 12 KDE images with PowerVR acceleration, Wayland, touchscreen, Wi-Fi, stable SD, and recovery tools for Cubie A7Z/A733 |
| [dok2d/cubie-a7z-debian](https://github.com/dok2d/cubie-a7z-debian) | 1 | Reproducible Debian Trixie on linux 6.6.98+ (arm64) image builder for Cubie A7Z |
| [makrelbka/orangepi4pro-image](https://github.com/makrelbka/orangepi4pro-image) | 3 | Orange Pi 4 Pro (A733) image with tun |
| [chainsx/openwrt-sunxi-aiot](https://github.com/chainsx/openwrt-sunxi-aiot) | 8 | OpenWrt for Allwinner sunxi AIoT boards (supports A733: Radxa-Cubie-A7A, Radxa-Cubie-A7Z) |
| [GameOctane/OctaneOS](https://github.com/GameOctane/OctaneOS) | 10 | Custom Batocera Linux fork for the Radxa Cubie A7S (retro gaming OS) |
| [ritiek/nixos-cubie-a7s](https://github.com/ritiek/nixos-cubie-a7s) | 0 | NixOS on Radxa Cubie A7S |
| [open-astro/openastro-orangepi4pro](https://github.com/open-astro/openastro-orangepi4pro) | 0 | OpenAstro OS image for Orange Pi 4 Pro — WiFi AP + AlpacaBridge-ready, custom kernel with ZWO EAF/EFW support |

## NPU (Neural Processing Unit)

The A733 features a Vivante VIP9000 NPU with 3 TOPS @ INT8. The device node is `/dev/vipcore` (char 199:0). The NPU supports INT8, INT16, FP16, and BF16 data types.

| Repo | Stars | Description |
|------|-------|-------------|
| [petayyyy/a733_npu_driver](https://github.com/petayyyy/a733_npu_driver) | 5 | NPU driver + toolchain for A733. SmolLM2-135M (21 tok/s), MobileCLIP-S0 (22.6ms/frame). Qwen2.5-0.5B fails on all quantization paths |
| [ZIFENG278/ai-sdk](https://github.com/ZIFENG278/ai-sdk) | 25 | Radxa Cubie series NPU AI-SDK — Allwinner's official NPU SDK for the Cubie board family |
| [VeriSilicon/TIM-VX](https://github.com/VeriSilicon/TIM-VX) | 261 | Official VeriSilicon TIM-VX NPU runtime — the upstream framework for Vivante NPU programming |
| [VeriSilicon/tflite-vx-delegate](https://github.com/VeriSilicon/tflite-vx-delegate) | 48 | TensorFlow Lite external delegate based on TIM-VX — run TFLite models on Vivante NPU |
| [VeriSilicon/vsi-pjrt-plugin](https://github.com/VeriSilicon/vsi-pjrt-plugin) | 7 | PJRT plugin for TensorFlow/JAX NPU acceleration via VeriSilicon IP |
| [MaverickLong/MLIR-TIM-VX](https://github.com/MaverickLong/MLIR-TIM-VX) | 3 | MLIR lowering path to TIM-VX backend for VeriSilicon NPUs |
| [unnamedwild-ux/frigate_npu_vivante](https://github.com/unnamedwild-ux/frigate_npu_vivante) | 4 | Frigate (NVR/surveillance) running on Vivante VIP9000 NPU |
| [msazanov/vivante-vip9000-llm-lab](https://github.com/msazanov/vivante-vip9000-llm-lab) | 0 | LLM experiments on Vivante VIP9000 NPU |
| [Haidegger22/orangepi-zero3w-gpu-npu-vpu-debian11](https://github.com/Haidegger22/orangepi-zero3w-gpu-npu-vpu-debian11) | 1 | Orange Pi Zero 3W (A733) GPU/NPU/VPU Debian 11 setup guide |
| [HBConline/orangepi4pro-skill](https://github.com/HBConline/orangepi4pro-skill) | 3 | Agent skill for Orange Pi 4 Pro (A733): embedded Linux, edge AI (3 TOPS NPU), GPIO/wiringOP, Android 13 AOSP. Encodes 263-page official manual |

### NPU Software Stack

| Component | Status | Notes |
|-----------|--------|-------|
| VIPLite 2.0.3.2 | Not installed | Allwinner's NPU SDK, ships with BSP |
| ACUITY Toolkit | Not installed | Model conversion tool (ONNX -> NPU binary) |
| TIM-VX | Available upstream | Open-source VeriSilicon runtime |
| TFLite VX Delegate | Available upstream | TFLite -> TIM-VX bridge |
| `/dev/vipcore` | Exists on board | Char device 199:0, mode 0666 |

## GPU (PowerVR BXM-4-64)

The A733 uses an Imagination BXM-4-64 MC1 GPU (PowerVR architecture). Driver: `pvrsrvkm`. Supports OpenGL ES 3.2, Vulkan 1.3, OpenCL 3.0.

| Repo | Stars | Description |
|------|-------|-------------|
| [ayiejosh/a733-powervr-fex](https://github.com/ayiejosh/a733-powervr-fex) | 5 | GPU Vulkan driver + FEX x86 bring-up: kernel DRM-PRIME patch, x86->GPU Vulkan thunk, Zink-on-Vulkan, what-works/what-doesn't doc |
| [TblP/orangepi-gpu-exp](https://github.com/TblP/orangepi-gpu-exp) | 1 | GPU GEMM / Whisper acceleration research on PowerVR BXM |
| [Haidegger22/orangepi-zero3w-gpu-pcie](https://github.com/Haidegger22/orangepi-zero3w-gpu-pcie) | 3 | GPU OpenGL ES 3.2 + Vulkan 1.3 + PCIe 3.0 on Orange Pi Zero 3W (A733) under Debian 13 |
| [Rabs9/radxa-a7a-toolkit](https://github.com/Rabs9/radxa-a7a-toolkit) | 0 | Fixes, tools and game launchers for Cubie A7A (A733 / PowerVR BXM-4-64) on Debian 13 |

## VPU / Video Engine

The Allwinner Video Engine (VE) on A733 supports H.264/H.265 encode @ 4K30fps and decode @ 8K24fps. **Note**: The V4L2 M2M driver is NOT loaded on current board — only software encode (libx264) works. The Cedrus open-source driver supports up to Allwinner H6; A733 support is unconfirmed.

| Repo | Stars | Description |
|------|-------|-------------|
| [skamagedon/a733-zero-copy](https://github.com/skamagedon/a733-zero-copy) | 3 | Zero-copy hardware video playback on A733 via libvdecoder + DRM PRIME. Bypasses broken vendor OMX layer. 174% CPU -> ~8%. Includes GStreamer element |
| [mashiqi/A733-Cedarc](https://github.com/mashiqi/A733-Cedarc) | 2 | Cedarc/VE2 H.264 hardware encoder for A733. Reads NV12 from stdin, outputs Annex-B H.264. No GStreamer required |
| [linux-sunxi/sunxi-cedrus](https://github.com/linux-sunxi/sunxi-cedrus) | 3 | Cedrus V4L2 stateful video decoder driver for Allwinner SoCs (out-of-tree, up to H6) |
| [linux-sunxi/libvdpau-sunxi](https://github.com/linux-sunxi/libvdpau-sunxi) | 169 | Experimental VDPAU for Allwinner sunxi SoCs (obsolete, see Sunxi-Cedrus) |
| [FlorentRevest/sunxi-cedrus-drv-video](https://github.com/FlorentRevest/sunxi-cedrus-drv-video) | 10 | Libva backend for sunxi-cedrus V4L2 M2M driver (upstream moved to free-electrons/cedrus) |
| [noblock/sunxi-cedar-mainline](https://github.com/noblock/sunxi-cedar-mainline) | 7 | Sunxi cedar kernel module (`/dev/cedar_dev`) targeting mainline kernel 4.11+ |

## x86 Emulation

A733's Cortex-A76 + PowerVR Vulkan make it a capable x86 emulation host. These projects let you run x86/x86-64 binaries on ARM64 Linux.

| Repo | Stars | Description |
|------|-------|-------------|
| [FEX-Emu/FEX](https://github.com/FEX-Emu/FEX) | 7906 | Fast usermode x86 and x86-64 emulator for Arm64 Linux. Already tested on A733 by ayiejosh/a733-powervr-fex |
| [FEX-Emu/RootFS](https://github.com/FEX-Emu/RootFS) | 14 | RootFS generation for FEX x86 emulation |
| [ptitSeb/box86](https://github.com/ptitSeb/box86) | 3800 | Linux userspace x86 emulator targeted at ARM Linux devices |
| [ptitSeb/box64](https://github.com/ptitSeb/box64) | 5623 | Linux userspace x86_64 emulator for ARM64, RV64 and LoongArch Linux devices |
| [ryanfortner/box86-debs](https://github.com/ryanfortner/box86-debs) | 40 | Automated box86 Debian/apt repository |
| [ryanfortner/box64-debs](https://github.com/ryanfortner/box64-debs) | 111 | Automated box64 Debian/apt repository |

## GPIO & Hardware Interfaces

| Repo | Stars | Description |
|------|-------|-------------|
| [orangepi-xunlong/wiringOP](https://github.com/orangepi-xunlong/wiringOP) | 530 | GPIO library for Allwinner boards (WiringPi fork, A733 compatible) |
| [orangepi-xunlong/wiringOP-Python](https://github.com/orangepi-xunlong/wiringOP-Python) | 82 | Python bindings for WiringOP GPIO control |

## WiFi & Bluetooth (AIC8800)

The Cubie A7A uses an AIC8800 WiFi/BT chip (USB bus, not PCIe/SDIO). Drivers: `aic8800_fdrv` + `aic_btusb`.

| Repo | Stars | Description |
|------|-------|-------------|
| [orangepi-xunlong/aic8800-wifi](https://github.com/orangepi-xunlong/aic8800-wifi) | 13 | AIC8800 WiFi/BT driver source (used on A733 boards) |
| [alpha-TH/aic8800-dkms](https://github.com/alpha-TH/aic8800-dkms) | 12 | DKMS package for AIC8800 WiFi driver (auto-rebuild on kernel update) |
| [BLUEMOON233/AIC8800-Linux-Driver](https://github.com/BLUEMOON233/AIC8800-Linux-Driver) | 21 | Fixed AIC8800 driver for Linux kernel 6.17+ (newer kernel compatibility) |
| [batocera-linux/aic8800](https://github.com/batocera-linux/aic8800) | 2 | AIC8800 driver as used in Batocera Linux (gaming-focused) |
| [rezaistoni-cloud/mt7921u-radxa-cubie-a7a](https://github.com/rezaistoni-cloud/mt7921u-radxa-cubie-a7a) | 0 | MT7921U (MediaTek WiFi USB) driver for Cubie A7A kernel 5.15.147-21-a733 |
| [Haidegger22/orangepi-zero3w-bluetooth-hid](https://github.com/Haidegger22/orangepi-zero3w-bluetooth-hid) | 0 | Bluetooth HID keyboard connection on Orange Pi Zero 3W (A733): uhid module build, pairing, autostart |

## Camera & Multimedia

| Repo | Stars | Description |
|------|-------|-------------|
| [petayyyy/a733_csi_cam_ros2](https://github.com/petayyyy/a733_csi_cam_ros2) | 1 | ROS 2 Humble CSI camera driver for IMX219 on A733: direct V4L2 capture, CameraInfo publishing, optional Allwinner ISP 3A via AWIspApi |

## PCIe & NVMe

The A733 has a PCIe 3.0 x1 lane (FPC connector). These projects address PCIe compatibility and NVMe boot.

| Repo | Stars | Description |
|------|-------|-------------|
| [CarterPerez-dev/orangepi-4-pro-nvme-fix](https://github.com/CarterPerez-dev/orangepi-4-pro-nvme-fix) | 6 | Fix for NVMe drives not detected on Orange Pi 4 Pro (A733) — device tree workaround for PCIe Gen3 speed-change bug affecting Phison-based SSDs |
| [MitkoMD/ALLWINNER_A733_PCIE_CORRECTION](https://github.com/MitkoMD/ALLWINNER_A733_PCIE_CORRECTION) | 0 | PCIe correction/fix for A733 |
| [28andrew/orangepi-4-pro-sas3008-hba](https://github.com/28andrew/orangepi-4-pro-sas3008-hba) | 0 | LSI SAS3008 (9300-8i) HBA on Orange Pi 4 Pro (A733): PCIe Gen1 link-training fix, DKMS driver, mpt3sas MSI patch |
| [Techburst36/orangepi-zero3w-m2-carrier](https://github.com/Techburst36/orangepi-zero3w-m2-carrier) | 2 | Open-source KiCad PCIe-to-M.2 carrier board for Orange Pi Zero 3W (A733) |
| [Haidegger22/orangepi-zero3w-nvme-boot](https://github.com/Haidegger22/orangepi-zero3w-nvme-boot) | 0 | Boot Orange Pi Zero 3W from microSD (U-Boot) + NVMe via JMS583 USB 3.0 adapter |
| [Haidegger22/orangepi-zero3w-nvme-hybrid-boot](https://github.com/Haidegger22/orangepi-zero3w-nvme-hybrid-boot) | 0 | Hybrid SD + NVMe boot via USB (RTL9210) on Orange Pi Zero 3W |

## Display & LCD

| Repo | Stars | Description |
|------|-------|-------------|
| [check456-beep/orangepi-hdmi-fix](https://github.com/check456-beep/orangepi-hdmi-fix) | 0 | HDMI boot and hotplug fixes for Orange Pi 4 Pro (A733) |
| [Haidegger22/orangepi-zero3w-hdmi-resolution](https://github.com/Haidegger22/orangepi-zero3w-hdmi-resolution) | 0 | HDMI resolution fix (1360x768 for LG TV): modeline, Xorg config, auto-detection |
| [nickyg666/orangepi-zero3w-ili9486-display](https://github.com/nickyg666/orangepi-zero3w-ili9486-display) | 0 | ILI9486 SPI 3.5" display support with TinyDRM, framebuffer, X11, and touch |
| [Anothernear/radxa-display-ili9486](https://github.com/Anothernear/radxa-display-ili9486) | 0 | Optimized ILI9486 3.5" LCD controller for Radxa Cubie A7A, overcoming performance and colour sync limitations |
| [nickyg666/orangepi-zero3w-spotpear-3.5-lcd](https://github.com/nickyg666/orangepi-zero3w-spotpear-3.5-lcd) | 0 | ST7796S SPI display driver: full init, correct colors (INVON), console panel, systemd auto-start |

## Gaming & Retro

The A733's 8-core CPU + PowerVR GPU make it a capable retro gaming machine.

| Repo | Stars | Description |
|------|-------|-------------|
| [GameOctane/OctaneOS](https://github.com/GameOctane/OctaneOS) | 10 | Custom Batocera Linux fork for Radxa Cubie A7S — retro gaming OS with emulator suite |
| [Haidegger22/orangepi-zero3w-vcmi-heroes3](https://github.com/Haidegger22/orangepi-zero3w-vcmi-heroes3) | 0 | Heroes of Might and Magic III (VCMI 1.7.5) on A733: installation, performance tuning, joystick control |

## AI / Edge Applications

Real-world AI applications built on A733's NPU and CPU.

| Repo | Stars | Description |
|------|-------|-------------|
| [Ronin-1124/cubie-a7a-voice-assistant](https://github.com/Ronin-1124/cubie-a7a-voice-assistant) | 1 | Offline Chinese voice assistant for Cubie A7A (A733 NPU): KWS + ASR + TTS |
| [Dan-CV-Edge/radxa-stereo-gesture-interface](https://github.com/Dan-CV-Edge/radxa-stereo-gesture-interface) | 0 | Embedded stereo-vision interface for real-time hand gesture recognition and device control on Cubie A7A |
| [Anothernear/CyberChest_Agronomico](https://github.com/Anothernear/CyberChest_Agronomico) | 0 | Wearable AI assistant on Cubie A7A for agricultural use — offline LLM for farming Q&A |
| [Anothernear/BMOAgente_agricultor](https://github.com/Anothernear/BMOAgente_agricultor) | 0 | Animated face + TTS/STT + LLM for offline horticulture assistance on Cubie A7A (wearable format) |
| [Haidegger22/orangepi-zero3w-qwen-deepseek-bridge](https://github.com/Haidegger22/orangepi-zero3w-qwen-deepseek-bridge) | 0 | Qwen (local via Ollama) <-> DeepSeek (cloud) router via CDP, with webchat + auto Chromium on A733 |

## Bootloader & U-Boot

| Repo | Stars | Description |
|------|-------|-------------|
| [well0nez/sunxi-env-patcher](https://github.com/well0nez/sunxi-env-patcher) | 1 | Allwinner/Sunxi U-Boot environment patcher with CRC32 fixup. Read, verify, modify env_a partitions on H713, H616, H6, D1 and other sunxi SoCs |
| [Guation/radxa-cubie-a5e-u-boot](https://github.com/Guation/radxa-cubie-a5e-u-boot) | 0 | Export DRAM parameters of Radxa Cubie A5E (u-boot tool) |
| [skitzo2000/esp32-a7s-fel](https://github.com/skitzo2000/esp32-a7s-fel) | 0 | ESP32-S3 firmware that FEL-boots a bricked Cubie A7S off a USB stick — no PC in the loop |
| [beg0fthend/edk2-a733](https://github.com/beg0fthend/edk2-a733) | 3 | WIP EDK2 (UEFI) port for Allwinner A733 / Orange Pi 4 Pro |

## Mainline Linux

Projects working on mainline Linux kernel support for A733.

| Repo | Stars | Description |
|------|-------|-------------|
| [nickyg666/orangepi-zero3w-mainline](https://github.com/nickyg666/orangepi-zero3w-mainline) | 1 | Mainline Linux 6.18.19+ hybrid kernel bring-up for Orange Pi Zero 3W (A733): boot to Ubuntu 26.04, AIC8800 SDIO WiFi, mainline PowerVR (BXM-4-64) GPU |
| [crescenzo77/radxa_cubie_a7s_allwinner_a733](https://github.com/crescenzo77/radxa_cubie_a7s_allwinner_a733) | 1 | Public evidence and staging workspace for bringing mainline Linux support to Cubie A7S / A733 |

## Community Projects

| Repo | Stars | Description |
|------|-------|-------------|
| [ayiejosh/a733-powervr-fex](https://github.com/ayiejosh/a733-powervr-fex) | 5 | FEX x86 emulation + GPU Vulkan on A733 — multi-project bring-up |
| [chainsx/openwrt-sunxi-aiot](https://github.com/chainsx/openwrt-sunxi-aiot) | 8 | OpenWrt router/edge-gateway build for sunxi AIoT (A733 compatible) |
| [JerrettDavis/orangepi4pro-cyberdeck](https://github.com/JerrettDavis/orangepi4pro-cyberdeck) | 1 | Orange Pi 4 Pro A733 cyberdeck planning and runbooks |
| [aricshow/orangepi-zero3w-armbian-cyberdeck-mods](https://github.com/aricshow/orangepi-zero3w-armbian-cyberdeck-mods) | 0 | Provisioning scripts turning Armbian for Orange Pi Zero 3W (A733) into a headless cyberdeck: BLE keyboard, HDMI, USB-C display glasses |
| [Haidegger22/orangepi-zero3w-mihomo-setup](https://github.com/Haidegger22/orangepi-zero3w-mihomo-setup) | 0 | Mihomo (Clash Meta) + MetaCubeXD dashboard + proxy Chromium setup on Debian 11 / A733 |
| [Haidegger22/orangepi-zero3w-chromium-cache-ram](https://github.com/Haidegger22/orangepi-zero3w-chromium-cache-ram) | 0 | SD card protection: Chromium cache in tmpfs, symlinks, swappiness, dirty_ratio tuning |
| [Haidegger22/orangepi-zero3w-cpu-temperature-panel](https://github.com/Haidegger22/orangepi-zero3w-cpu-temperature-panel) | 0 | CPU temperature display on Xfce panel via genmon |
| [atharvap8/radxa-cubie-embedded](https://github.com/atharvap8/radxa-cubie-embedded) | 0 | Embedded Linux development projects using the Radxa Cubie A7A |

## Tools & Utilities

| Repo | Stars | Description |
|------|-------|-------------|
| [linux-sunxi/sunxi-tools](https://github.com/linux-sunxi/sunxi-tools) | 679 | Allwinner SoC command-line tools (fel, bootinfo, nand-part, etc.) |
| [linux-sunxi/sunxi-boards](https://github.com/linux-sunxi/sunxi-boards) | - | Board configurations for U-Boot and kernel |
| [Rabs9/radxa-a7a-toolkit](https://github.com/Rabs9/radxa-a7a-toolkit) | 0 | Fixes, tools and game launchers for Cubie A7A on Debian 13 |
| [Razican/aw-fel-rs](https://github.com/Razican/aw-fel-rs) | 4 | Allwinner ARM FEL mode library for Rust, based on sunxi-tools |
| [hglm/a10disp](https://github.com/hglm/a10disp) | 10 | Program to change display mode of Allwinner devices running linux-sunxi |

## 3D Printed Cases

| Repo | Stars | Description |
|------|-------|-------------|
| [fcjr/cubie-a7z-case](https://github.com/fcjr/cubie-a7z-case) | 0 | Snap-fit case for the Radxa Cubie A7Z |
| [itzshubhamdev/radxa-cubie-a7a-case](https://github.com/itzshubhamdev/radxa-cubie-a7a-case) | 1 | 3D-printable case for the Radxa Cubie A7A |

---

## Hardware Asset Report

The complete hardware asset report is in [`radxa_a7a_hardware_assets.md`](./radxa_a7a_hardware_assets.md). It covers:

- SoC architecture (Cortex-A76/A55, DynamIQ, L3 cache)
- Memory & storage (LPDDR5 4GB, eMMC 55GB, SPI NOR 8MB)
- VPU capabilities and driver status
- GPU (PowerVR BXM-4-64) features and OpenCL readiness
- NPU (Vivante VIP9000) 3 TOPS capabilities and software stack
- Thermal management (8 zones, PWM fan)
- Hardware security (AES/DES/SM4/RSA/ECC/SM2 + TRNG + TrustZone)
- SBC comparison (A7A vs RPi 5 vs Orange Pi 5 vs Rock 5B)
- Unexploited capabilities and known limitations

---

## Contributing

Found a project that works on Radxa Cubie A7A/A7Z/A7S or any A733-based board? Submit a PR!

1. Fork this repo
2. Add the project to the appropriate category
3. Include: repo URL, star count, and a one-line description
4. Submit a pull request

## License

MIT

## Acknowledgments

- [Radxa](https://radxa.com/) — hardware and official BSP
- [linux-sunxi community](https://linux-sunxi.org/) — open-source Allwinner documentation
- [VeriSilicon](https://github.com/VeriSilicon) — TIM-VX NPU runtime
- [Orange Pi](http://www.orangepi.org/) — A733-based boards (4 Pro, Zero 3W)
- All the community contributors building the A733 ecosystem
