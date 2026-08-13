<a name="english"></a>

🇬🇧 English Version
💻 Hardware & Device
Motherboard: Qitian M4330
CPU: Intel Core i7-3770K
Target Device: iPhone 5s (A7 Chip)
I. VMware Windows 10 (1903) Environment Attempts
1. Setup & Initial Try
Environment: VMware 17 + Kali Linux 2025.02 VM

Process:

Due to the network environment in China, a proxy was configured first:

export http_proxy="http://proxy"
export https_proxy="http://proxy"

# Test proxy connection
curl -I https://www.google.com

# Install dependencies and clone repository
sudo apt update && sudo apt install git -y
git clone https://github.com/LukeZGD/Legacy-iOS-Kit.git
cd Legacy-iOS-Kit
2. Issues & Strategy Shift
Issue Encountered:
Legacy-iOS-Kit got stuck at Stage: SETUP. Log analysis showed that the tool attempted to automatically execute the gaster exploit, which had poor compatibility with A7 devices.
Workaround — Jailbreak & Generator Lock:
I decided to jailbreak the device first, manually write and lock the Generator value using dimentio, and then restore the device using SHSH2 blobs.
Jailbreak Tools: 3uTools (爱思助手) + Sileo
Action: Installed OpenSSH through Sileo after jailbreaking and opened the SSH tunnel using 3uTools.
Result:
The process was still stuck at Stage: SETUP.
Suspecting USB passthrough latency and permission issues inside the Kali VM, I migrated to Ubuntu 22.04 VM.
The problem persisted because Legacy-iOS-Kit continued to prioritize gaster.
Eventually, I abandoned Legacy-iOS-Kit and switched to raw FutureRestore.
II. FutureRestore Attempts on Native Windows
Process:
Downloaded the iOS 10.3.3 .shsh2 ticket and .ipsw firmware using Legacy-iOS-Kit, then passed them to FutureRestore.
Issue Encountered:
FutureRestore returned Error -8.

Root Cause Analysis:
I noticed that FutureRestore reported:

Unable to enter recovery-mode

before the iPhone actually entered recovery mode.

I suspected that Apple device drivers on Windows were responding too slowly.
During the restore process, exploit triggering and device handshakes require extremely precise timing.
Driver latency may therefore cause the process to time out.
Decision:
I completely abandoned the Windows/VM setup and switched to a Kali Linux Live USB bare-metal boot.
III. Bare-Metal Kali Live USB Boot — Final Solution
1. Setup
Environment: Kali Linux 2025.02 booted directly from USB
Screen Recording Setup:
sudo apt update
sudo apt install ffmpeg obs-studio -y
2. Troubleshooting & Breakthrough
Issue Encountered:
Running Legacy-iOS-Kit on bare-metal Kali Linux 2025.02 still resulted in the process getting stuck at Stage: SETUP.

Workaround:
I suspected that the older Kali release contained outdated LibUSB or other dependency packages.

Therefore, I performed a full system upgrade to Kali 2026-W33 (Latest).

3. Final Result

After updating the system, I ran the script again.

Success! 🎉

The process successfully passed Stage: SETUP and proceeded to the next flashing stage.

💡 Takeaway:
The most likely reason for the successful attempt was the updated Linux kernel and driver stack in the latest Kali build, which may have improved USB enumeration and communication latency when handling A7 devices in DFU/Recovery mode.

<a name="中文"></a>

🇨🇳 中文版本
💻 硬件与设备信息
主板: Qitian M4330
CPU: Intel Core i7-3770K
目标设备: iPhone 5s（A7 芯片）
一、Windows 10 (1903) 环境下虚拟机
1. 环境搭建与初步尝试
测试环境: VMware 17 + Kali Linux 2025.02 虚拟机

操作过程:

由于处于国内网络环境，首先配置网络代理：

export http_proxy="http://proxy"
export https_proxy="http://proxy"

# 测试代理连接状态
curl -I https://www.google.com

# 安装依赖并拉取工具
sudo apt update && sudo apt install git -y
git clone https://github.com/LukeZGD/Legacy-iOS-Kit.git
cd Legacy-iOS-Kit
2. 踩坑与策略调整

遭遇问题:
Legacy-iOS-Kit 运行后卡在 Stage: SETUP。

分析日志发现，工具尝试自动调用 gaster 漏洞进行降级，而该漏洞对 A7 芯片的适配/兼容性并不理想。

应对策略——越狱与 Generator 固化:
决定先对 iPhone 进行越狱，然后使用 dimentio 手动写入并固定 Generator，以配合 SHSH2 票据进行刷机。
越狱工具: 爱思助手（3uTools）+ Sileo
操作: 越狱后通过 Sileo 安装 OpenSSH，并使用爱思助手打开 SSH 通道。
后续结果:
依然卡在 Stage: SETUP。
怀疑是 Kali 虚拟机环境下 USB 挂载权限以及通信延迟导致的问题，于是尝试将虚拟机系统更换为 Ubuntu 22.04。
结果依然卡住，因为 Legacy-iOS-Kit 仍然优先尝试调用 gaster。
最终决定放弃 Legacy-iOS-Kit，改用 FutureRestore 命令行直接进行刷机。
二、Windows 环境下的 FutureRestore
操作过程:
使用 Legacy-iOS-Kit 下载 iOS 10.3.3 的 .shsh2 票据文件与 .ipsw 固件，然后将它们交给 FutureRestore 执行刷机。
遭遇问题:
终端报错 Error -8。

原因分析:
观察发现，FutureRestore 在终端输出：

Unable to enter recovery-mode

之后，iPhone 才延迟响应并进入恢复模式。

猜测是 Windows 下 Apple 设备驱动响应速度过慢。
刷机过程中，漏洞触发以及设备握手的窗口期非常短，对 USB 通信延迟比较敏感。
Windows 驱动响应延迟可能导致超时。
最终决定:
彻底放弃 Windows / 虚拟机环境，改用 Kali Linux Live USB 实机物理引导。
三、物理机 Kali Live USB 引导——终局成功
1. 测试环境与准备
测试环境: Kali Linux 2025.02 USB Boot
录屏环境配置:
sudo apt update
sudo apt install ffmpeg obs-studio -y

结果:
在物理机 Kali Linux 2025.02 下运行 Legacy-iOS-Kit，依然卡在 Stage: SETUP。
应对策略:
怀疑 Kali 旧版本 Rolling 源中的 LibUSB 或其他底层软件依赖版本较旧，因此对系统执行全量更新，升级至 Kali 2026-W33 (Latest)。
3. 最终结果

系统更新完成后，重新运行脚本：

成功跨越 Stage: SETUP！ 🎉

随后顺利进入下一个刷机阶段。

💡 总结推测:
最终成功的核心因素很可能是最新版 Linux 内核与驱动栈的更新。新版系统可能改善了底层 USB 通信和设备枚举逻辑，从而降低了 A7 设备在 DFU / Recovery 模式下的通信延迟。
