# Radxa Cubie A7A — 完整硬件资产报告

> 调研日期: 2026-09-04 | 方法论: lyco 同类经验汲取 (板子实测 + 官方文档 + 社区资料)
> 板子: Radxa Cubie A7A | SoC: Allwinner A733 (sun60iw2) | 内核: 6.6.98-4-aw2511

---

## 1. SoC: Allwinner A733

| 项目 | 规格 | 实测确认 |
|------|------|----------|
| 制程 | 12nm | - |
| 封装 | ED-FCCSP, 15×15mm, 570 balls | - |
| 型号 | A733MX-HN3 (全功能版, 有NPU+HDMI+6BK) | `compatible: radxa,cubie-a7a / allwinner,sun60i-a733` |
| CPU | 2× Cortex-A76 (r4p1) @ **2.0GHz** + 6× Cortex-A55 (r2p0) @ **1.8GHz**, DynamIQ big.LITTLE | `cpu0-5: part=0xd05 @1794000` `cpu6-7: part=0xd0b @2002000` |
| MCU | 玄弦 RISC-V XuanTie E902 @ ~200MHz | SoC 内置, 独立实时核 |
| GPU | Imagination BXM-4-64 MC1 (PowerVR) | `DRIVER=pvrsrvkm`, `OF_COMPATIBLE=img,gpu` |
| NPU | Vivante VIP9000, **3 TOPS @INT8** | **`/dev/vipcore` 存在!** `cooling_device: devfreq-3600000.npu` |
| VPU | Allwinner Video Engine (VE) | 硬件编解码能力见下表 |
| PMIC | AXP8191 | `regulator: axp8191-*` (40+ 路电源输出) |
| 内存控制器 | LPDDR4/LPDDR4X/LPDDR5, 32-bit, up to 16GB | 板载 LPDDR5 4GB (实测 3.8Gi) |

### CPU 缓存层级

| 级别 | A76 (大核) | A55 (小核) |
|------|-----------|------------|
| L1 I | 64 KiB/core | 32 KiB/core |
| L1 D | 64 KiB/core | 32 KiB/core |
| L2 | 256 KiB/core | 128 KiB/core |
| L3 | 1 MiB (全核共享) | - |

### CPU 特性 (ARMv8-A)
`fp asimd aes pmull sha1 sha2 crc32 atomics fphp asimdhp cpuid asimdrdm lrcpc dcpop asimddp`
→ 支持硬件 AES/SHA 加速、FP16 半精度、点积指令 (DOTPROD)

---

## 2. 内存与存储

### 内存
| 项目 | 值 |
|------|-----|
| 类型 | LPDDR5 |
| 容量 | 4GB (板载, 不可升级) |
| 实测可用 | 3.8Gi (after reserved) |
| Swap | 1.9Gi (zram, 压缩内存) |
| 带宽 | LPDDR5 @ 2400MHz (SoC 支持, 实际取决于板子配置) |

### 存储
| 接口 | 实测 | 说明 |
|------|------|------|
| eMMC | 55GB (mmcblk1, 板载) | 分区: /config(16M) + /boot/efi(300M) + /(54.7G) |
| SPI NOR | 8MB (mtdblock0) | 用于 U-Boot/Bootloader |
| microSD | 有卡槽 | SD 3.0 |
| eMMC/UFS 模块口 | 有 (二合一) | 支持热替换 |
| NVMe SSD | 需 M.2 扩展板 | 通过 PCIe 3.0 FPC 接口引出 |
| UFS 3.0 | SoC 支持, 需 UFS 模块 | 板上有模块接口 |

---

## 3. 视频编解码 (VPU)

### SoC 标称能力 (来自 Allwinner 官方 + linux-sunxi)

| 操作 | 编解码器 | 最大规格 |
|------|---------|---------|
| **编码** | H.264/H.265 | **4K@30fps** |
| **编码** | MJPEG | 4K@15fps |
| 解码 | H.265/VP9/AVS2 | **8K@24fps** |
| 解码 | H.264 | 4K@30fps |
| 解码 | VP8 | 1080P@60fps |
| 解码 | H.263/MPEG-4 | 1080P@60fps |

### 实测状态 (当前内核 6.6.98)

| 组件 | 状态 | 说明 |
|------|------|------|
| `/dev/video*` | **不存在** | V4L2 M2M 设备节点未创建 |
| `/dev/cedrus*` | **不存在** | Cedrus 开源驱动未加载 |
| `/dev/media*` | **不存在** | 媒体控制器未注册 |
| ffmpeg `h264_v4l2m2m` | **编码器已编译但不可用** | ffmpeg 有此编码器, 但无 `/dev/video*` 设备 → 实测报错 "Could not find a valid device" |
| ffmpeg `hevc_v4l2m2m` | 同上 | 同样有编译但无设备 |
| ffmpeg 硬件加速 | `drm`, `vulkan`, `opencl`, `vaapi`, `vdpau`, `cuda` | 仅 soft encode 实际可用 (libx264/libx265) |

### 结论: VPU 硬件编解码 = 纸上谈兵

SoC 确实有硬件编解码引擎 (VE), 但当前 Radxa BSP 内核 (6.6.98-4-aw2511) **没有加载 V4L2 M2M 驱动**, 导致 ffmpeg 的 `h264_v4l2m2m`/`hevc_v4l2m2m` 编码器无法使用。

**现状: 只能软编 libx264** (1.58x 实时@1080×1920@30 veryfast)

**可能的解锁路径:**
1. 等待 Radxa/Allwinner BSP 更新补上 V4L2 M2M 驱动
2. 自行编译内核开启 `VIDEO_SUNXI` / `SUNXI_CEDRUS` 配置
3. Cedrus 开源驱动 (linux-sunxi.org/Cedrus) — 但目前支持列表最高到 H6, **A733 未被 Cedrus 项目确认支持**

---

## 4. GPU: Imagination BXM-4-64 MC1

| 项目 | 值 |
|------|-----|
| 驱动 | `pvrsrvkm` (Imagination 官方闭源驱动) |
| 设备节点 | `/dev/dri/card1`, `/dev/drm/renderD128` |
| DRM 节点 | card0=sunxi-drm (显示), card1=pvrsrvkm (GPU) |
| OpenGL ES | 3.2 (via PVR-MESA wrapper) |
| Vulkan | 1.3 |
| OpenCL | 3.0 (`libOpenCL.so` 已安装) |
| 用户态库 | `libGLESv2_PVR_MESA.so`, `libpvr_dri_support.so` (24.2.6603887) |
| 温度传感器 | `gpu_thermal_zone` (实测 33.5°C) |
| 散热 | PWM 风扇接口 (2-pin 1.25mm), cooling_device3, 当前档位 4/4 |

### GPU 可用 API
- **OpenGL ES 3.2** — 3D 渲染, 游戏, UI 合成
- **Vulkan 1.3** — 高性能渲染/计算
- **OpenCL 3.0** — GPU 通用计算 (可用于并行加速)

### GPU 计算 (OpenCL) 潜力
OpenCL 库已安装 (`libOpenCL.so.1`), 理论上可用于:
- 图像处理加速 (ffmpeg `opencl` 滤镜)
- 轻量并行计算
- 但 BXM-4-64 MC1 规格较小 (MC1 = 1 个着色器阵列), 计算能力有限

---

## 5. NPU: Vivante VIP9000 (3 TOPS)

### 硬件状态
| 项目 | 值 |
|------|-----|
| 设备节点 | **`/dev/vipcore`** (字符设备 199:0, 权限 0666) |
| 驱动 | 内核内置 (devfreq 管理频率) |
| 温度传感器 | `npu_thermal_zone` (实测 34.0°C) |
| 散热 | devfreq-cpu0 + devfreq-3600000.npu (2 档降频) |
| 算力 | 3 TOPS @INT8 |
| 支持精度 | INT8 / INT16 / FP16 / BF16 (多精度混合计算) |

### 软件栈
| 组件 | 状态 | 说明 |
|------|------|------|
| VIPLite 驱动 | **未安装** | `/usr/lib` 下无 `libvx*`/`libVip*`/`lib_tim*` 库 |
| ACUITY Toolkit | 未安装 | 需 Docker 环境 (ubuntu-npu:v2.0.10.1) |
| TIM-VX | 未安装 | VeriSilicon 开源运行时 |
| OpenVX | 未安装 | 标准化 API |

### NPU 能力实测 (来自 petayyyy/a733_npu_driver 项目)

| 模型 | 状态 | 性能 |
|------|------|------|
| SmolLM2-135M (INT16) | **可用** | 20.7 tok/s, 连贯输出 |
| SmolLM2-360M (INT16) | **可用** | 8.4 tok/s, 连贯输出 |
| MobileCLIP-S0 视觉编码 | **可用** | 22.6 ms/帧, 精度 0.99996 |
| SmolLM2-1.7B | **失败** | NBG 编译段错误 |
| Qwen2.5-0.5B | **失败** | INT16 精度崩 (0.236), BF16 无法编译 |
| Whisper (语音) | 未见测试 | 可能受限于无 KV-cache |

### NPU 关键限制
1. **无 KV-cache** (静态形状 NBG), 只能处理固定短窗口 (W≤64)
2. **Qwen 级别 LLM 无法在 NPU 上运行** — 激活值异常导致所有量化路径失败
3. **推荐混合架构**: NPU 跑视觉编码 + 小 LLM, CPU 跑 Qwen (llama.cpp, 18 tok/s @Q8_0)
4. ACUITY 维度上限 65536

### 激活 NPU 的路径
1. 从 Radxa 文档安装 VIPLite 2.0.3.2 用户态库
2. 从 GitHub ZIFENG278/ai-sdk 获取 SDK
3. 用 Docker ACUITY 工具链将 ONNX → NBG
4. 参考 petayyyy/a733_npu_driver 的完整工具链文档

---

## 6. 接口全览

### 网络
| 接口 | 实测 | 说明 |
|------|------|------|
| 千兆以太网 | `end0` (UP, 有线) | GMAC, 支持 IEEE 1588 PTP, 支持 PoE (需 PoE HAT) |
| Wi-Fi 6 | `wlan0` (DOWN, 未连接) | AIC8800 芯片 (`aic8800_fdrv` 驱动), USB 总线 |
| 蓝牙 5.4 | `hci0` (UP RUNNING) | AIC8800 芯片 (`aic_btusb` 驱动), USB 总线, 设备名 radxa-cubie-a7a |

### USB
| 端口 | 实测 | 说明 |
|------|------|------|
| USB 3.1 Type-A HOST | Bus 002 (xhci, 10Gbps) | SoC USB 3.1 Gen2 |
| USB 2.0 Type-A ×3 | Bus 001 (xhci, 480M) | 通过 4-port Hub, 下挂 WiFi/BT 复合设备 |
| USB Type-C OTG | 有 | 兼供电 |
| 已接设备 | AIC8800 WiFi/BT 复合芯片 (Port 004) | 一个物理芯片提供 WiFi+BT |

### 显示/摄像头
| 接口 | 说明 |
|------|------|
| HDMI 2.0b | 最高 4K@60fps, 支持 CEC/HDCP 1.4 |
| MIPI DSI | 4-lane, 用于 LCD 屏 |
| MIPI CSI | 4-lane ×1 (SoC 有 4+4+2 lane 共 3 个 CSI 口) |
| eDP 1.4b / DP 1.4 | SoC 支持, 板子通过 FPC 引出 |
| RGB/LVDS | SoC 支持, 用于老式屏 |
| E-ink | SoC 支持 (独特功能) |

### GPIO / 扩展
| 接口 | 实测 | 说明 |
|------|------|------|
| 40-pin GPIO 排针 | 支持 UART/SPI/I2C | 兼容树莓派 40-pin |
| I2C 总线 | `/dev/i2c-0/13/14/15/20` | SoC 有 16 个 TWI 控制器 |
| SPI | SoC 有 5 个 SPI | `spi0 @8MHz` 在时钟树中可见 |
| UART | SoC 有 9 个 UART | 板上未见 `/dev/ttyS*` (可能未使能) |
| PWM | 3 个 PWM 控制器 (pwmchip0/10/20) | SoC 有 30 个 PWM 通道 |
| PCIe 3.0 | **1× FPC 接口, 单通道** | SoC PCIe 3.0 RC/EP, combo PHY |
| RTC | `/sys/class/rtc/rtc0` | 外接 I2C RTC, **实测唤醒不可靠** |
| LEDC | SoC 内置 | 用于 LED 灯带 |
| ADC | GPADC ×7, LRADC ×1 | 模拟输入 |
| 3.5mm 耳机口 | 麦克输入 + 立体声输出 | 可驱动 32Ω 耳机 |
| 风扇接口 | 2-pin 1.25mm | PWM 控制, 4 档 |
| 电源按键 | 有 | |
| UBOOT 按键 | 有 | |

### 存储/启动
| 接口 | 说明 |
|------|------|
| microSD 卡槽 | SD 3.0 |
| eMMC/UFS 模块口 | 二合一, 可选 eMMC 或 UFS 3.0 模块 |
| 8MB SPI NOR | U-Boot Bootloader |
| 启动介质选择 | 通过 GPADC/eFuse |

### 供电
| 方式 | 说明 |
|------|------|
| USB Type-C 5V | 主要供电方式 |
| PoE | 需外接 PoE HAT |
| GPIO 5V | 引脚 2 与 4 |

---

## 7. 散热与温度

### 温度传感器 (8 个热区)
| 传感器 | 实测温度 | 说明 |
|--------|---------|------|
| cpub_thermal_zone | 36.4°C | A76 大核 |
| cpul_thermal_zone | 36.9°C | A55 小核 |
| gpu_thermal_zone | 33.5°C | GPU |
| npu_thermal_zone | 34.0°C | NPU |
| ddr_thermal_zone | 33.2°C | 内存 |
| skin_zone | 31.4°C | 表壳 |
| cpul_idle_zone | 36.4°C | 空闲基准 |
| cpub_idle_zone | 36.9°C | 空闲基准 |

### 散热控制
| cooling_device | 类型 | 档位 |
|----------------|------|------|
| cooling_device0 | cpufreq-cpu0 (A55) | 0/8 |
| cooling_device1 | cpufreq-cpu6 (A76) | 0/11 |
| cooling_device2 | devfreq-3600000.npu | 0/2 |
| cooling_device3 | pwm-fan | 4/4 (全速) |

→ PWM 风扇当前在全速运行 (4/4)

---

## 8. 安全特性
| 功能 | 支持 |
|------|------|
| AES/DES/3DES/XTS/SM4 | 硬件加速 |
| MD5/SHA/HMAC/SM3 | 硬件加速 |
| RSA/ECC/SM2 | 硬件加速 |
| TRNG (真随机数) | 有 |
| PRNG | 有 |
| TrustZone | CPU 支持 |
| Secure Boot | 支持 |
| 安全外设控制器 (SPC) | 有 |
| 安全内存控制器 (SMC/TZMA) | 有 |

---

## 9. 与同类 SBC 对比

| 规格 | Radxa Cubie A7A | Raspberry Pi 5 | Orange Pi 5 (RK3576) | Radxa Rock 5B (RK3588) |
|------|-----------------|----------------|----------------------|------------------------|
| SoC | Allwinner A733 (12nm) | BCM2712 (16nm) | RK3576 (8nm) | RK3588S (8nm) |
| CPU | 2×A76@2.0G + 6×A55@1.8G | 4×A76@2.4G | 4×A72@2.2G | 4×A76@2.4G |
| GPU | BXM-4-64 MC1 | VideoCore VII | Mali-G52 MC3 | Mali-G610 MP4 |
| NPU | **3 TOPS** (VIP9000) | 无 | **6 TOPS** | **6 TOPS** |
| VPU 编码 | 4K@30 (H.264/H.265) | 4K@30 (H.264/H.265) | 4K@60 (H.265) | 8K@30 (H.265) |
| VPU 解码 | 8K@24 (H.265) | 4K@60 (H.265) | 8K@60 (H.265) | 8K@60 (H.265) |
| 内存 | LPDDR5 4GB | LPDDR4X 8GB | LPDDR4X 4-16GB | LPDDR4X 4-16GB |
| PCIe | 3.0 ×1 (FPC) | 2.0 ×1 | 3.0 ×4 | 3.0 ×4 |
| HDMI | 2.0b 4K@60 | 2.0 4K@60 | 2.0 4K@60 | 2.1 8K@60 |
| 价格 | ~$25起 | $60-80 | $40-70 | $70-110 |
| 尺寸 | 85×56mm | 85×56mm | 60×60mm | 85×56mm |

### 关键差异
- **A7A 的优势**: 最便宜的 NPU SBC (3 TOPS/$ 性价比), 8 核规模, E-ink 支持
- **A7A 的劣势**: VPU 驱动未启用 (只能软编), NPU 算力较弱 (3T vs 6T), PCIe 只有 ×1
- **A7A 不适合跑大 LLM** (Qwen0.5B 都无法 NPU 量化), 但适合视觉推理 + 小模型

---

## 10. 当前板子配置实测

| 项目 | 值 |
|------|-----|
| OS | Debian 13 (trixie), aarch64 |
| 内核 | 6.6.98-4-aw2511 (BSP 内核, 非主线) |
| 可用内存 | 3.8Gi (used 2.3Gi, free 686Mi, buff/cache 953Mi) |
| Swap | 1.9Gi (zram 压缩) |
| eMMC | 55GB (54.7GB 根分区) |
| 网络 | 千兆有线 (end0, UP) + WiFi 6 (wlan0, DOWN) |
| 蓝牙 | hci0 UP RUNNING |
| CPU 频率策略 | ondemand |
| 当前温度 | 31-37°C (各传感器) |
| 风扇 | 全速 (4/4) |
| ffmpeg | 7.1.5-0+deb13u1 (soft encode only) |
| 已装关键软件 | ffmpeg, ImageMagick, rclone, yt-dlp, espeak-ng, faster-whisper |

---

## 11. 未榨干的硬件能力

| 能力 | 现状 | 潜在价值 | 激活难度 |
|------|------|---------|---------|
| **VPU 硬件编解码** | 驱动未加载, 只能软编 | 视频编码 4K@30fps, 可能 3-5× 加速 | 高 (需内核重编或等 BSP 更新) |
| **NPU 3 TOPS** | `/dev/vipcore` 存在但无用户态库 | YOLOv8/MobileCLIP 视觉推理, 小 LLM | 中 (安装 VIPLite SDK + ACUITY 工具链) |
| **GPU OpenCL** | 库已装但未实际使用 | 图像处理加速 (ffmpeg opencl 滤镜) | 低 (直接可用) |
| **GPU Vulkan** | 驱动已装 | 高效渲染/计算 | 低 |
| **RISC-V E902 MCU** | SoC 内置, 无法直接访问 | 超低功耗实时任务 | 高 (需 SDK, 通常仅 Android/Tina 用) |
| **硬件加密引擎** | 内核内置但未暴露用户接口 | TLS/VPN/存储加密加速 | 中 |
| **MIPI CSI 摄像头** | 未连接 | 摄像头输入 (安防/视觉) | 低 (接 CSI 模块) |
| **E-ink 输出** | SoC 支持 | 电子墨水屏显示 | 中 (需驱动配置) |
| **PoE 供电** | 需 PoE HAT | 单线网线供电+联网 | 低 (买 PoE HAT) |
| **PCIe 3.0 NVMe** | 需 M.2 扩展板 | 高速存储 | 低 (买扩展板+SSD) |

---

## 12. 已知限制与坑

1. **VPU 硬件编解码不可用** — 当前 BSP 内核未加载 V4L2 M2M 驱动, `/dev/video*` 不存在。Cedrus 开源驱动不支持 A733 (最高到 H6)。
2. **RTC 唤醒不可靠** — 实测 `rtcwake -m mem -s 100` 后板子无法自醒, I2C RTC 中断脚未接 PMIC 唤醒线路。
3. **NPU 无 KV-cache** — VIP9000 静态形状 NBG, 无法运行带 KV-cache 的 LLM (Qwen 级别全部失败)。
4. **NPU 不是 LLM 加速器** — 3 TOPS 适合 CNN/视觉, 不适合 Transformer (推荐 RK3588 跑 LLM)。
5. **WiFi/BT 走 USB 总线** — AIC8800 芯片挂在 USB 2.0 上 (480M), 不是 PCIe/Sdio, 带宽受限。
6. **PCIe 仅 ×1** — 单通道, NVMe 速度上限 ~8GT/s (PCIe 3.0 ×1)。
7. **内存不可升级** — LPDDR5 板载, 选 4GB 版就是 4GB, 最多到 16GB 需购买时选配。
8. **非主线内核** — 6.6.98-4-aw2511 是 Allwinner BSP, 不是 mainline Linux, 社区驱动兼容性存疑。

---

## 13. 数据来源

| 来源 | URL |
|------|-----|
| Allwinner 官方 A733 页面 | https://www.allwinnertech.com/index.php?c=product&a=index&id=139&solveid=34 |
| Radxa 官方 Cubie A7A 文档 | https://docs.radxa.com/cubie/a7a |
| Radxa 产品页面 | https://radxa.com/products/cubie/a7a/ |
| linux-sunxi A733 wiki | https://linux-sunxi.org/A733 |
| SBCwiki Cubie A7A | https://sbcwiki.com/docs/soc-manufacturers/allwinner/a733/boards/radxa-cubie-a7a/ |
| Notebookcheck A733 | https://www.notebookcheck.net/Allwinner-A733-Processor-Benchmarks-and-Specs.951751.0.html |
| NPU 实测项目 (petayyyy) | https://github.com/petayyyy/a733_npu_driver |
| Vivante NPU SDK 文档 | https://docs.radxa.com/cubie/a7a/app-dev/npu-dev/cubie-acuity-sdk |
| A733 Datasheet V0.93 | https://dl.radxa.com/cubie/a7a/docs/hw/datasheet/A733_Datasheet_V0.93.pdf |
| Cedrus 开源驱动 | https://linux-sunxi.org/Cedrus |
| 板子实测 (SSH) | 2026-09-04 21:xx 直接从板子读取 |

---

*报告完毕。这块板子的硬件底牌已经全部翻开：8核CPU/3T NPU/BXM GPU 都是实的，但VPU硬编和NPU软件栈还没榨干。*
