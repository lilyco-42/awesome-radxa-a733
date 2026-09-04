# Awesome Radxa Cubie A7A / A7Z (Allwinner A733)

> A curated index of open-source projects, drivers, and tools that work on the **Radxa Cubie A7A / A7Z** SBC powered by the **Allwinner A733** SoC (sun60iw2).

The Allwinner A733 is a 12nm SoC featuring 2x Cortex-A76 @ 2.0GHz + 6x Cortex-A55 @ 1.8GHz (DynamIQ big.LITTLE), Imagination BXM-4-64 MC1 GPU, Vivante VIP9000 NPU (3 TOPS @ INT8), and Allwinner Video Engine (H.264/H.265 encode 4K@30, decode 8K@24).

## Table of Contents

- [Kernels & Firmware](#kernels--firmware)
- [OS Images](#os-images)
- [NPU (Neural Processing Unit)](#npu-neural-processing-unit)
- [GPU (PowerVR BXM-4-64)](#gpu-powervr-bxm-4-64)
- [VPU / Video Engine](#vpu--video-engine)
- [GPIO & Hardware Interfaces](#gpio--hardware-interfaces)
- [WiFi & Bluetooth (AIC8800)](#wifi--bluetooth-aic8800)
- [Camera & Multimedia](#camera--multimedia)
- [Community Projects](#community-projects)
- [Tools & Utilities](#tools--utilities)
- [Hardware Asset Report](#hardware-asset-report)

---

## Kernels & Firmware

| Repo | Stars | Description |
|------|-------|-------------|
| [Rabs9/radxa-cubie-a7a-kernel](https://github.com/Rabs9/radxa-cubie-a7a-kernel) | 0 | Debian 13 kernel for Cubie A7A with overclocking support |
| [Numerio/radxa-cubie-a7a-maximum-overclock](https://github.com/Numerio/radxa-cubie-a7a-maximum-overclock) | 0 | Max overclock config: NPU 2520MHz, GPU 1488MHz, CPU 2080MHz |
| [radxa/kernel](https://github.com/radxa/kernel) | - | Radxa official kernel repository (includes A733 BSP) |

## OS Images

| Repo | Stars | Description |
|------|-------|-------------|
| [radxa-images/radxa-cubie-a7a](https://github.com/radxa-images/radxa-cubie-a7a) | - | Official Radxa Cubie A7A system images |
| [chainsx/openwrt-sunxi-aiot](https://github.com/chainsx/openwrt-sunxi-aiot) | 8 | OpenWrt for Allwinner sunxi AIoT boards (A733 compatible) |

## NPU (Neural Processing Unit)

The A733 features a Vivante VIP9000 NPU with 3 TOPS @ INT8. The device node is `/dev/vipcore` (char 199:0). The NPU supports INT8, INT16, FP16, and BF16 data types.

| Repo | Stars | Description |
|------|-------|-------------|
| [petayyyy/a733_npu_driver](https://github.com/petayyyy/a733_npu_driver) | 5 | NPU driver + toolchain for A733. Includes SmolLM2-135M (21 tok/s), MobileCLIP-S0 (22.6ms/frame) benchmarks. Qwen2.5-0.5B fails on all quantization paths. |
| [VeriSilicon/TIM-VX](https://github.com/VeriSilicon/TIM-VX) | 261 | Official VeriSilicon TIM-VX NPU runtime — the upstream framework for Vivante NPU programming |
| [VeriSilicon/vsi-pjrt-plugin](https://github.com/VeriSilicon/vsi-pjrt-plugin) | 7 | PJRT plugin for TensorFlow/JAX NPU acceleration |
| [mashiqi/A733-Cedarc](https://github.com/mashiqi/A733-Cedarc) | 2 | Cedarc/VE2 exploration for A733 NPU access |
| [Ronin-1124/cubie-a7a-voice-assistant](https://github.com/Ronin-1124/cubie-a7a-voice-assistant) | 1 | Offline Chinese voice assistant running on A7A NPU |
| [Haidegger22/orangepi-zero3w-gpu-npu-vpu-debian11](https://github.com/Haidegger22/orangepi-zero3w-gpu-npu-vpu-debian11) | 1 | GPU/NPU/VPU setup guide (applicable to A733 boards) |

### NPU Software Stack

| Component | Status | Notes |
|-----------|--------|-------|
| VIPLite 2.0.3.2 | Not installed | Allwinner's NPU SDK, ships with BSP |
| ACUITY Toolkit | Not installed | Model conversion tool (ONNX → NPU binary) |
| TIM-VX | Available upstream | Open-source VeriSilicon runtime |
| `/dev/vipcore` | Exists on board | Char device 199:0, mode 0666 |

## GPU (PowerVR BXM-4-64)

The A733 uses an Imagination BXM-4-64 MC1 GPU (PowerVR architecture). Driver: `pvrsrvkm`. Supports OpenGL ES 3.2, Vulkan 1.3, OpenCL 3.0.

| Repo | Stars | Description |
|------|-------|-------------|
| [ayiejosh/a733-powervr-fex](https://github.com/ayiejosh/a733-powervr-fex) | 5 | GPU Vulkan driver + FEX x86 emulation bring-up on A733 |
| [TblP/orangepi-gpu-exp](https://github.com/TblP/orangepi-gpu-exp) | 1 | GPU GEMM / Whisper acceleration research on PowerVR BXM |

## VPU / Video Engine

The Allwinner Video Engine (VE) on A733 supports H.264/H.265 encode @ 4K30fps and decode @ 8K24fps. **Note**: The V4L2 M2M driver is NOT loaded on current board — only software encode (libx264) works. The Cedrus open-source driver supports up to Allwinner H6; A733 support is unconfirmed.

| Repo | Stars | Description |
|------|-------|-------------|
| [skamagedon/a733-zero-copy](https://github.com/skamagedon/a733-zero-copy) | 3 | Hardware video playback via DRM PRIME zero-copy on A733 |
| [linux-sunxi/sunxi-cedrus](https://github.com/linux-sunxi/sunxi-cedrus) | 3 | Cedrus V4L2 stateful video decoder driver for Allwinner SoCs |
| [linux-sunxi/libvdpau-sunxi](https://github.com/linux-sunxi/libvdpau-sunxi) | 169 | VDPAU backend for Allwinner sunxi hardware video decoding |
| [mashiqi/A733-Cedarc](https://github.com/mashiqi/A733-Cedarc) | 2 | Cedarc/VE2 H.264 hardware encoder exploration for A733 |

## GPIO & Hardware Interfaces

| Repo | Stars | Description |
|------|-------|-------------|
| [orangepi-xunlong/wiringOP](https://github.com/orangepi-xunlong/wiringOP) | 530 | GPIO library for Allwinner boards (WiringPi fork, A733 compatible) |
| [orangepi-xunlong/wiringOP-Python](https://github.com/orangepi-xunlong/wiringOP-Python) | 82 | Python bindings for WiringOP GPIO control |

## WiFi & Bluetooth (AIC8800)

The Cubie A7A uses an AIC8800 WiFi/BT chip (USB bus, not PCIe/SDIO). Drivers: `aic8800_fdrv` + `aic_btusb`.

| Repo | Stars | Description |
|------|-------|-------------|
| [orangepi-xunlong/aic8800-wifi](https://github.com/orangepi-xunlong/aic8800-wifi) | 13 | AIC8800 WiFi/BT driver source |
| [alpha-TH/aic8800-dkms](https://github.com/alpha-TH/aic8800-dkms) | 12 | DKMS package for AIC8800 WiFi driver (auto-rebuild on kernel update) |

## Camera & Multimedia

| Repo | Stars | Description |
|------|-------|-------------|
| [petayyyy/a733_csi_cam_ros2](https://github.com/petayyyy/a733_csi_cam_ros2) | 1 | ROS2 CSI camera driver for A733 SoC |

## Community Projects

| Repo | Stars | Description |
|------|-------|-------------|
| [ayiejosh/a733-powervr-fex](https://github.com/ayiejosh/a733-powervr-fex) | 5 | FEX x86 emulation + GPU Vulkan on A733 |
| [TblP/orangepi-gpu-exp](https://github.com/TblP/orangepi-gpu-exp) | 1 | GPU compute experiments (GEMM, Whisper) on PowerVR BXM |
| [Ronin-1124/cubie-a7a-voice-assistant](https://github.com/Ronin-1124/cubie-a7a-voice-assistant) | 1 | Offline Chinese voice assistant using A7A NPU |
| [chainsx/openwrt-sunxi-aiot](https://github.com/chainsx/openwrt-sunxi-aiot) | 8 | OpenWrt router/edge-gateway build for sunxi AIoT |

## Tools & Utilities

| Repo | Stars | Description |
|------|-------|-------------|
| [linux-sunxi/sunxi-tools](https://github.com/linux-sunxi/sunxi-tools) | 679 | Allwinner SoC command-line tools (fel, bootinfo, nand-part, etc.) |
| [linux-sunxi/sunxi-boards](https://github.com/linux-sunxi/sunxi-boards) | - | Board configurations for U-Boot and kernel |

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

Found a project that works on Radxa Cubie A7A/A7Z? Submit a PR!

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
- All the community contributors building the A733 ecosystem
