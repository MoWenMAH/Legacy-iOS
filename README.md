| Hardware        | Model / Specifications |
| :-------------- | :--------------------- |
| **Motherboard** | Qitian M4330           |
| **CPU**         | Intel Core i7-3770K    |

[English](#english) | [中文](#chinese)

---

<a name="english"></a>
## English
### I. Nonsense
By the time you see this project, I will have already uninstalled QQ and Discord, transferred all non-study-related files on my computer to an external hard drive, and handed it over to my parents for safekeeping. Today is August 9, 2026, with less than 10 months to go before the Gaokao (June 5, 2027). Time is flying by so fast. My high school days are coming to an end soon—it makes me so sad QwQ.

(For those unfamiliar with the Gaokao, you can think of it as an SAT taken by over 600,000 students, the New York Times wasn't exaggerating when it described it as 'thousands of troops crossing a single-log bridge. XD The Gaokao is immensely important to me—it not only determines whether I can get into my favotite university, but also defines the future of both myself and my family. Plus, for me, I only get one shot at this.)

So I must put everything else on hold for the next 10 months to focus entirely on prep.
I've temporarily pushed all my projects to GitHub (this is one of them)so that I can pick them back up after the Gaokao is over, starting after June 10, 2027.

# Goal: Downgrade an iPhone 5s (A7 chip, running iOS 12.5.7) to iOS 10.3.3.

Friends from the jailbreak community recommended Legacy-iOS-Kit (referred to as kits below). Since kits is a Linux tool, I decided to use a Kali Linux (2025.02) virtual machine I already had set up in VMware from my network security self-study. Being Debian-based, Kali should theoretically run kits fine.

Since my host machine (Windows) is in Mainland China, I needed to route the VM's traffic through a proxy to avoid network blocks. I used V2RayN:

Opened Command Prompt on the Windows host and ran ipconfig to find its local IPv4 address (192.168....).

Enabled "Allow LAN Connection" in V2RayN, which assigned port 10809.

In the VM terminal, I configured the proxy and tested the connection:

```bash
export http_proxy="http://192.168....:10809"	#在虚拟机的终端连接代理
export https_proxy="http://192.168....:10809"
curl -I https://www.google.com	#ping一下google看看有没有成功
```

It returned a proper HTTP response, confirming the proxy was working. Then I proceeded with the setup:
```bash
sudo apt update	#更新一下kali的apt源
sudo apt install git -y	#下载安装git
git clone https://github.com/LukeZGD/Legacy-iOS-Kit.git	#下载安装Legacy-iOS-Kit
启动kits
cd Legacy-iOS-Kit
./restore.sh
```
However, Legacy-iOS-Kit got stuck at Stage: SETUP. Looking through the logs, I realized it hung when trying to invoke gaster for injection. gaster requires millisecond-level timing over USB, and the USB passthrough latency between the Windows host and the VM was causing packet injection to fail consistently.

Continuing with gaster meant I had to move away from virtual machines and run native Linux. But without a spare external hard drive to install Linux on, Gemini suggested a workaround: jailbreak the iPhone, install dimentio to set the generator, and let kits bypass Apple's checks without relying on gaster.

So I jailbroke the phone using 3uTools (Sileo was installed as the package manager), installed OpenSSH and dimentio via Sileo, connected over SSH using 3uTools, and set the generator:

```bash
su  #赋予root权限
dimentio 	0x1111111111111111  #固定generator
```

It succeeded, but when I ran kits again, it still froze at Stage: SETUP. The logs showed that kits was still trying to invoke gaster. It seems kits strictly relies on gaster for this check (or at least that's my guess; only the developer knows for sure).

I shared the situation with my friend, who suggested trying FutureRestore since it runs natively on Windows, eliminating VM USB passthrough latency. I downloaded the iOS 10.3.3 .shsh2 blob and the matching .ipsw firmware using Legacy-iOS-Kit, then fed them into FutureRestore.

It threw Error -8. I noticed an interesting pattern, though: FutureRestore reported "failed to enter recovery mode" before the iPhone actually entered recovery mode. This suggested Windows itself was experiencing USB communication delay, meaning a pure Windows setup was out of the question, too.

I bit the bullet and bought a 64GB Kingston USB drive, flashed Kali USB Live Boot (2025.02) onto it, and downloaded V2RayN for Linux on GitHub. After disabling Legacy Mode in my older motherboard's BIOS, I booted straight into live Kali:

```bash
sudo apt install ffmpeg   # Prerequisite for OBS
sudo apt install obs-studio	 # To record the process
```

I repeated the same restore steps as before.

Unbelievably, Legacy-iOS-Kit got stuck at Stage: SETUP yet again. At this point, I was out of ideas and suspected it might be an issue with that specific Kali build. As a last resort, I grabbed the latest weekly build (2026-W33) from kali.org.

I ran kits once more on the new build, and it worked—it smoothly passed the setup stage and moved to the next step. I still don't know the exact root cause, but a clean, updated environment finally did the trick.

---

<a name="chinese"></a>
## 中文
### 一、废话
当你看到这个项目时，我已经卸载了 QQ 和 discord，并把电脑上所有与学习无关的资源，剪贴到了一个硬盘上，交给父亲保管。今天是 Aug 9, 2026，距离高考还有不到 10 个月的时间 (Jun. 5, 2027)。时间过得真快。我的高中生活很快就要结束了，这让我很伤心 QwQ。

（For those unfamiliar with the Gaokao, you can think of it as an SAT taken by over 600,000 students, the New York Times wasn't exaggerating when it described it as 'thousands of troops crossing a single-log bridge. XD The Gaokao is immensely important to me—it not only determines whether I can get into my favorite university, but also defines the future of both myself and my family. Plus, for me, I only get one shot at this.)

想必没有必要多说它是多么的残酷和重要，我必须在接下来的10个月里暂停所有其他事情，放下一切准备高考。
我把我所有的项目都暂时存在 github 上这个项目（这是其中之一），以便高考后，也就是 Jun. 10, 2027 以后重新启动。

# 目标：把iphone5s(A7,OS is ios12.5.7),降级为ios10.3.3

Jailbreak圈的朋友推荐我用Legacy-iOS-Kit(下面简称kits),kits是Linux软件，恰好之前自学网络安全时在vmware上安装了kali linud(2025.02)虚拟机，kali是debian内核，理论上可以运行kits

由于宿主机(Windows)在CN网络环境下，需要给虚拟机配置代理防止程序被墙，这里我用的是V2RayN

1.打开宿主机的cmd，用ipconfig查看ipv4地址：192.168....

2.在V2RayN种允许来自互联网的连接，它给我分配了10809端口

```bash
export http_proxy="http://192.168....:10809"	#在虚拟机的终端连接代理
export https_proxy="http://192.168....:10809"
curl -I https://www.google.com	#ping一下google看看有没有成功
```

显示了一大串东西，看来是代理配置成功了，那现在就开始刷机

```bash
sudo apt update	#更新一下kali的apt源
sudo apt install git -y	#下载安装git
git clone https://github.com/LukeZGD/Legacy-iOS-Kit.git	#下载安装Legacy-iOS-Kit
```

启动kits

```bash
cd Legacy-iOS-Kit
./restore.sh
```

结果Legacy-iOS-Kit 卡在 Stage: SETUP。分析一下日志，当我发现kits尝试调用gaster注入时，我明白为什么了：gaster对usb通信的要求是毫秒级的，虚拟机和宿主机存在通信延迟，导致gaster根本没机会注入数据包

如果我继续用gaster，就必须放弃虚拟机，在linux环境下进行刷机，但我手头没有多余的移动硬盘去安装操作系统

gemini给了我一种解决方案：将iphone越狱，然后安装dimentio插件，用它固定generator，这样kits就可以实现不用gaster也能绕过apple的检查

越狱软件：Sileo

用爱思助手越狱后在 Sileo 中安装 SSH通道插件和dimentio插件，用爱思助手连接SSH通道:

```bash
su	#赋予root权限
dimentio 	0x1111111111111111	#固定generator
```

Made it!现在kits试一下，结果还是卡在Stage: SETUP，日志显示kits依旧调用了gaster，看来kits只能用gaster实现绕过apple的检查（这只是我的猜测，真实原因要问作者）

我把我的情况说给朋友，他说我可以尝试用FutureRestore,这个是一个windows软件，所以我不需要再用虚拟机，这样就没有了虚拟机与宿主机的通信延迟

用 Legacy-iOS-Kit 下载 iOS 10.3.3 的 .shsh2 文件和适配iphone 5s的ios10.3.3的 .ipsw 固件，将其注入 FutureRestore。

结果出现 error -8 错误。虽然失败了但我发现了一个很有意思的现象：FutureRestore 在终端提示“无法进入 recovery-mode”之后，iPhone 才进入恢复模式，这说明windows与iphone间本身就存在通信延迟，这样看来，就只能彻底放弃windiws环境

我破费买了一个kingstone 64g的u盘，刷入Kali USB Boot 2025.02，在github上下载v2rayn for linux

进入BIOS将兼容模式关闭（主板比较老，bios还有legact启动方式），进入kali

```bash
sudo apt install ffmpeg	#安装obs的前置
sudo apt install obs-studio	#安装obs记录下这一切
```

之后的步骤和向前一样

结果运行 Legacy-iOS-Kit 依然卡在 Stage: SETUP。到这我就不知道到底是什么原因了，我怀疑是kali本身的问题，抱着碰运气的心态去kali.org下载了 2026-W33 (latest) 的kali

再运行kits发现成功了，kits成功跳过setup阶段进入下一个阶段，具体原因目前未知

特别致谢：@PermissionDog @

---

