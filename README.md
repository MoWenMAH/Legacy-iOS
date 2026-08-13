
# 📱 iPhone 5s Downgrade | iPhone 5s 

> **Note / 说明**: Click the sections below to toggle between English and Chinese content.
> 点击下方展开栏即可切换中/英文内容。

---

<details open>
<summary><b>🇺🇸 English Version</b></summary>

<br>

### 💻 Hardware Specifications

| Hardware | Model / Specifications |
| :--- | :--- |
| **Motherboard** | Qitian M4330 |
| **CPU** | Intel Core i7-3770K |
| **Target Device** | iPhone 5s (A7 Chip) |

---

### 1. VM Attempts under Windows 10 (1903)

* **Environment**: VMware 17 + Kali Linux 2025.02 (VM)
* **Execution**:
* curl -I https://www.google.com
sudo apt update
sudo apt install git -y
git clone https://github.com/LukeZGD/Legacy-iOS-Kit.git
cd Legacy-iOS-Kit
  * Attempted to run Legacy-iOS-Kit inside the virtual machine.
* **Issue Encountered**: 
  * Stuck infinitely at `Stage: SETUP`.
  * **Log Analysis**: The tool attempted to invoke the `gaster` exploit for downgrading, which is incompatible/unsupported on the A7 chip.
* **Workaround (Jailbreak & Set Generator)**: 
  * Decided to jailbreak the iPhone and use `dimentio` to fix/set the Generator value to restore with SHSH blobs.
  * **Tools Used**: 3uTools (爱思助手), Sileo.
  * **Steps**: Installed OpenSSH via Sileo after jailbreaking, then opened the SSH tunnel using 3uTools.
* **Result**: 
  * Still stuck at `Stage: SETUP`.
  * **Troubleshooting**: Suspected USB passthrough/permission issues in Kali VM. Switched the VM to **Ubuntu 22.04**, but the issue persisted because Legacy-iOS-Kit continued attempting `gaster`.
  * **Decision**: Abandoned Legacy-iOS-Kit and switched to **FutureRestore**.

---

### 2. FutureRestore Attempts under Windows

* **Execution**: 
  1. Used Legacy-iOS-Kit to fetch/download the iOS 10.3.3 `.shsh2` blobs and `.ipsw` firmware.
  2. Fed both files into FutureRestore.
* **Issue Encountered**: Threw `Error -8`.
* **Root Cause Analysis**: 
  * Noticed that the iPhone entered recovery mode *after* FutureRestore already logged `"Failed to enter recovery-mode"`.
  * The Windows Apple driver response time was too sluggish (exploit payload injection requires millisecond-level precision).
  * **Decision**: Completely abandoned Windows and moved to bare-metal **Kali USB Boot**.

---

### 3. Bare-Metal Kali USB Boot

* **Environment**: Kali USB Boot 2025.02 (Live OS on bare metal)
* **Preparation**: Configured screen recording environment to capture logs.
* sudo apt update
sudo apt install ffmpeg
sudo apt install obs-studio
* **Issue Encountered**: Running Legacy-iOS-Kit was *still* stuck at `Stage: SETUP`.
* **Workaround**: 
  * Suspected outdated repositories or broken USB library dependencies in the Kali image.
  * Upgraded the system packages to **Kali Linux 2026-W33 (Latest)**.
* **Final Result**: 
  * 🎉 **Successfully passed `Stage: SETUP` and proceeded to the next stage!**
  * *Speculated Cause*: Likely resolved by updated USB stack/drivers in the latest Kali kernel release combined with native physical USB controller access.

</details>

---

<details>
<summary><b>🇨🇳 中文版 (Chinese Version)</b></summary>

<br>

### 💻 硬件设备 (Hardware)

| 设备名称 (Device) | 型号 / 规格 (Model / Specs) |
| :--- | :--- |
| **主板 (Motherboard)** | Qitian M4330 |
| **CPU** | Intel Core i7-3770K |
| **目标设备 (Target Device)** | iPhone 5s (A7 Chip) |

---

### 一、 Windows 10 (1903) 环境下的虚拟机尝试

* **测试环境**: VMware 17 + 虚拟机 Kali Linux 2025.02
* **操作过程**:
* 由于处于国内网络环境，先配置个代理
export http_proxy="http://proxy" export https_proxy="http://proxy" 
ping一下google看看有没有成功
curl -I https://www.google.com
sudo apt update
sudo apt install git -y
git clone https://github.com/LukeZGD/Legacy-iOS-Kit.git
cd Legacy-iOS-Kit
  * 试图通过虚拟机运行 Legacy-iOS-Kit 进行降级操作。
* **遭遇问题**: 
  * 过程卡在 `Stage: SETUP`。
  * **日志分析**: 工具试图调用 `gaster` 漏洞去降级，但该漏洞对 A7 芯片不适配/兼容。
* **应对策略 (越狱固化)**: 
  * 决定通过 iPhone 越狱，使用 `dimentio` 固定 Generator，从而实现配合 SHSH 刷机。
  * **越狱工具**: 爱思助手, Sileo
  * **具体操作**: 越狱后在 Sileo 中安装 SSH，用爱思助手打开 SSH 通道。
* **结果**: 
  * 依然卡在 `Stage: SETUP`。
  * **原因排查**: 怀疑是 Kali 虚拟机系统的 USB 权限问题，随后将虚拟机换成 **Ubuntu 22.04**。但结果依然卡在 `Stage: SETUP`（因为 Legacy-iOS-Kit 仍在强行尝试调用 `gaster` 漏洞）。
  * **最终决定**: 放弃 Legacy-iOS-Kit，改用 **FutureRestore**。

---

### 二、 Windows 环境下的 FutureRestore 尝试

* **操作过程**: 
  1. 使用 Legacy-iOS-Kit 下载 iOS 10.3.3 的 `.shsh2` 签名文件和 `.ipsw` 固件。
  2. 将文件注入 FutureRestore 执行刷机。
* **遭遇问题**: 提示报错 `Error -8`。
* **原因分析**: 
  * 观察发现：FutureRestore 在终端提示 *"无法进入 recovery-mode"* **之后**，iPhone 才迟钝地进入恢复模式。
  * 怀疑是 Windows 系统对 Apple 驱动的响应过慢。刷机抓取漏洞的时间窗口是毫秒级的，因此彻底放弃 Windows，改用 **Kali USB Boot 实机物理系统**。

---

### 三、 物理机 Kali USB Boot 尝试

* **测试环境**: Kali USB Boot 2025.02 (物理机原生运行)
* **前期准备**: 配置录屏环境记录刷机过程。
* sudo apt update
sudo apt install ffmpeg
sudo apt install obs-studio
* **遭遇问题**: 运行 Legacy-iOS-Kit 依然卡在 `Stage: SETUP`。
* **应对策略**: 
  * 怀疑是 Kali 系统软件源或底层依赖版本过旧。
  * 执行系统更新，将系统升至 **Kali Linux 2026-W33 (Latest)**。
* **最终结果**: 
  * 🎉 **成功越过 `Stage: SETUP`，顺利进入下一阶段！**
  * *原因推测*: 可能与新版系统底层的 USB 驱动更新及物理机直连 USB 权限修复有关。

</details>
