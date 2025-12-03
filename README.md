<div align="center"> 
  
<img src="https://raw.githubusercontent.com/AlejandroSQR/Rocky-Linux-10.x-Post-Installation-Guide/main/images/rockypostguide10xlogo.svg" width="700" alt="RockyL10XPI">

# Rocky Linux 10.x - Post-Installation Guide (v0.5)

  **Detailed Guide for Rocky Linux 10.x and Future Versions (WIP)**

Report any issues or Bugs you find in our repository's Issues tab, or in Discord Server

[![Discord](https://img.shields.io/badge/Discord-Join_Server-5865F2?style=for-the-badge&logo=discord&logoColor=white)](https://discord.gg/ADHq9Mp7Wx)


</div>

# Introduction
Rocky Linux 10 is a distribution based on RHEL that aims for long-term stability, remaining constant over time and avoiding fast or rapid changes. This guide serves  who wish to have their desktop updated with the essentials to be able to work on the Distro.

# 0.1 Before Installation

## [IMPORTANT] Secure Boot Disabled
This guide was created based on a version of Rocky Linux without Secure Boot, although in some points it is highlighted what to do in case it is enabled.

## [IMPORTANT] Not Intended for Fedora-Based Distros 

**NOT** for Linux Distributions that are constantly updated such as Fedora Workstation/Spin, Nobara, Bazzite, Asahi, Ultramarine, etc.

> [!WARNING]
> If you want to apply this guide with the previous mentioned distros it may work, but it is very likely that you could break your OS due to the frequency of updates offered by each distro. That include tools, kernel, drivers and development kits, which updates in a short periods of time.

> [!TIP]
> If you are looking for a tutorial for Fedora-Based distros, see the Post-Installation Guide for Fedora 43 by devangshekhawat) (Can work on previous Versions)

[https://github.com/devangshekhawat/Fedora-43-Post-Install-Guide](https://github.com/devangshekhawat/Fedora-43-Post-Install-Guide)

## [EXPERIMENTAL] RHEL-Based Distros
Although the guide is written to work with Rocky Linux 10.x it may also work on Distros like:

- AlmaLinux
- CentOS 
- Oracle 
  
> [!WARNING]  
> Compatibility is not guaranteed, some sections or tools may behave differently depending on the specific distribution including repositories and  even previous versions.

> [!IMPORTANT]
> Includes Rocky Linux 8.x & 9.x

## Differences between Rocky ISOs

Link to Download Rocky Linux 10.x
[https://rockylinux.org/download](https://rockylinux.org/download)

> [!IMPORTANT]
> The complete installation **(DVD ISO)** is being used on this Guide. It provides GNOME by default and basic apps and is not necessary to select additional configurations.

--- 
- The Online version **(Boot ISO)** requires internet connection during installation, which offers you more options, repositories, and programs depending on what you want, but that will not change the steps in this guide.

 > [!TIP]
> If you prefer a configuration like the complete version (DVD ISO), just need to select and check Workstation/Desktop option during installation

- The (Minimal ISO) version will not install any graphical interface after installation; everything done after reboot will be doing from the command line.

 > [!TIP]
> If you wish to install a graphical environment, try using this command (By default, this installs the GNOME environment)
```
sudo dnf groupinstall "Server with GUI"
sudo systemctl set-default graphical
sudo reboot
```

> [!IMPORTANT]
> **Cloud, Docker, Workstation Live Images and WSL have not been tested**

## First Looking After Installation
If you installed the complete version or a version with basic components, you will have 3 essential repositories.

- BaseOS
- AppStream
- Extras.

> [!NOTE]
> If you wish to obtain more information to know what each repository does, visit [https://wiki.rockylinux.org/rocky/repo/](https://wiki.rockylinux.org/rocky/repo/)

# 0.5 Upgrade to Rocky Linux 10.x
Since November 11 2025, Rocky began updating to versions 10.x (Similar to 8.x and 9.x)

If you have version 10.0 and a higher version exists (10.0 → 10.x), update it to the latest version, to avoid errors in this guide.

## Method 1 - Terminal
```
sudo dnf update
sudo dnf upgrade
```
## Method 2 - Software
**Software > Updates > Check Updates**

# 1. Essential Repositories
Enable **"EPEL" (Extra Packages for Enterprise Linux)** repositories
```
sudo dnf install epel-release
```
Enable **"ElRepo"** repositories
```
sudo dnf install https://www.elrepo.org/elrepo-release-10.el10.elrepo.noarch.rpm
```
Enable **"RPM Fusion Free & Non Free"** repositories
```
sudo dnf install https://download1.rpmfusion.org/free/el/rpmfusion-free-release-$(rpm -E %rhel).noarch.rpm https://download1.rpmfusion.org/nonfree/el/rpmfusion-nonfree-release-$(rpm -E %rhel).noarch.rpm
```
After enabling the repositories what you need, check if they are enabled with the command
```
sudo dnf repolist
```
> [!NOTE]
> If necessary, reboot your system to see the applications in the Software Store
```
reboot
```
# 1.5 [OPTIONAL] Install Kernel 6.12+
A modern kernel in Rocky Linux is not necessary if you do not present any problem with your Hardware or external devices. In case your hardware is very recent and does not detect something specific, the stable kernel that comes with Rocky may not work and require this step.

Import the GPG key
```
rpm —import https://www.elrepo.org/RPM-GPG-KEY-v2-elrepo.org
```
> [!NOTE]
> Why? RPM verifies that it has not been modified and that it truly comes from the official repository. Install the new kernel (Q4 2025)

Install the new Kernel **(Q4 2025)**
```
dnf —enablerepo elrepo-kernel install kernel-ml
```
The repository will offer you the 6.17.9 Kernel Version  **(Q4 2025)**

## [IMPORTANT] Secure Boot configuration after installing the new Kernel
This step is very important if you installed the latest kernel and you have Secure Boot enabled; if you ignore this step, it will not let you start the OS correctly. 

Update the ElRepo repository
```
sudo dnf update elrepo-release
```
> This will help you to have the most recent key and thus avoid problems 
Get your SecureBoot Key **(ElRepo)**
```
wget https://elrepo.org/SECURE-BOOT-KEY-elrepo.org.der
```
Install **mokutil**
```
sudo dnf install mokutil
```
Install your Key with mokutil
```
mokutil --import /etc/pki/elrepo/SECURE-BOOT-KEY-elrepo.org.der
```
The moment when you enter the command, it will ask you to create a password and confirm it again

When your password is created, reboot and you will see the Shim UEFI Key Management screen **(Generally the screen is Blue)**

Press any key within 10 seconds to continue. Enter using the password established previously

> [!NOTE]
> If you see that when entering the password it says "GPG check FAILED, error when fetching installing or upgrading packages" it's because they released a new key, the best thing to do is disable Secure Boot to re-enter and generate a new key.

> [!CAUTION]
> In case of any error with the Kernel, return to the previous version with GRUB or reinstall your OS


If something's wrong , visit these pages as an alternative to this Kernel installation
[https://vinfrastructure.it/2025/08/installing-latest-kernel-on-a-rocky-linux/](https://vinfrastructure.it/2025/08/installing-latest-kernel-on-a-rocky-linux/)
[https://docs.rockylinux.org/10/guides/custom-linux-kernel/](https://docs.rockylinux.org/10/guides/custom-linux-kernel/)

# 2. Essential Utilities and Dependencies


> [!IMPORTANT]
> Many tools are for programmers, however this step is NECESSARY for those who wish to install NVIDIA & INTEL Drivers 

Enable CodeReady Builder (CRB) repository. You have two methods to do it. 

#### Method 1 - Terminal
```
sudo dnf config-manager --enable crb
```
#### Method 2 - Software
- Software > Main Menu > Software Repositories > "Rocky Linux 10 - CRB"


Install "Development Tools" group that comes by default in Rocky
```
sudo dnf groupinstall "Development Tools"
```

Install essential packages for package management and network operation.
```
sudo dnf install wget curl dnf-utils
```

Install kernel-devel packages
```
sudo dnf install kernel-devel-matched kernel-headers
```
> [!WARNING]
> Even with the new Kernel nothing will happen and it will not present problems, but it may happen that the command does not work or installs a package with an old version of the installation.

# 3. NVIDIA Drivers
> [!CAUTION]
> It's **NECESSARY** to Install all packages from the section "Essential Utilities and Dependencies" **(Step 2)**

> [!NOTE]
> The best way to install NVIDIA drivers from the official page. However, Rocky has a repository for RHEL 10, which simplify installation

## Method Recommended by Rocky Linux
Add the official NVIDIA repository (Rocky Linux 10 / RHEL 10)
```
sudo dnf config-manager —add-repo http://developer.download.nvidia.com/compute/cuda/repos/rhel10/$(uname -m)/cuda-rhel10.repo
```
Clean all your cache repositories 
```
sudo dnf clean expire-cache
```
Select the Driver you are interested in installing; you have two options to do so:
### Open Source Drivers
```
sudo dnf install nvidia-open
```
### Proprietary Drivers
```
sudo dnf install cuda-drivers
```

#### Which NVIDIA drivers should I choose?
- **Open Source Drivers (nvidia-open)**
	- They can work in casual applications and environments that need a graphics card such as games or applications with 2D and 3D views.
   
- **Proprietary Drivers (cuda-drivers):**
	- It works better in areas like 3D, Video Editing, Simulation or Artificial Intelligence that need CUDA and to make the most of the performance of your Graphics Card.
   
## Disable "Nouveau" in your system
Nouveau is an open source NVIDIA driver that offers limited functionality compared to proprietary NVIDIA drivers. It is better to disable it to avoid conflicts between drivers

> [!IMPORTANT]
> Even if you installed the proprietary version with CUDA, it is recommended to use this command; there are situations where if it is not disabled, the system will automatically switch to open source drivers
```
sudo grubby —args="nouveau.modeset=0 rd.driver.blacklist=nouveau" —update-kernel=ALL
```

## NVIDIA & Secure Boot [IMPORTANT]
If you have Secure Boot enabled, you need to activate this command:
```
sudo mokutil --import /var/lib/dkms/mok.pub
```
> [!NOTE]
> Why? With Secure Boot enabled, Linux will only load signed modules; proprietary NVIDIA drivers need to be signed to load correctly.

> [!IMPORTANT]
> After rebooting, the system will ask you if you wish to register a key, select "yes" and it will ask you to create a new password that you entered in the mokutil command (Similar to the Kernel Secure Boot process)

## Verify if NVIDIA installed correctly
At the moment you installed the NVIDIA drivers, it is most recommended to:
- Wait 5-10 minutes for the drivers to load.
- Reboot
  
At the moment of installation it is necessary that you verify if they are working correctly. 

Verify that the NVIDIA kernel module is loaded
```
lsmod | grep nvidia
```
Check that the System detects the NVIDIA Driver
```
lspci | grep -i nvidia
```
Verify the NVIDIA version
```
nvidia-smi
```

## Alternatives [COMING SOON]
RPMFusion and ElRepo" NVIDIA Repositories

-RPMFusion and ELRepo are available for those who prefer a community NVIDIA repository based on the Distro; for older or deprecated hardware, RPMFusion works better

# 4. Flatpak / Flathub

Flatpak comes installed by default starting from Rocky 8, only its Flathub repositories need to be enabled

## Method 1 (Gnome Software)
Install from the Software application:
- Download, open and install the Flathub Repository File:

[https://dl.flathub.org/repo/flathub.flatpakrepo](https://dl.flathub.org/repo/flathub.flatpakrepo)
  
## Method 2 (Terminal)
```
flatpak remote-add —if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
```
After adding the repositories you need, check if they are enabled with the command
```
sudo dnf repolist
```
# 5. AppImage + Fuse (Optional)
Rocky 10 does not need you to install dependencies to run .AppImage files. However, having "fuse" package makes you avoid compatibility problems
```
sudo dnf in fuse
```
- The package is included in the "BaseOS" repository
  
# 6. Audio and Video Codecs
Additional Codecs and Libraries to play audio and videos smoothly on Linux.

## General Codecs
```
sudo dnf4 group install multimedia
sudo dnf swap ffmpeg-free ffmpeg --allowerasing
sudo dnf update @multimedia --setopt="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin
```
## FFmpeg & Multimedia Libraries
FFmpeg allows processing, converting formats, and optimizing streaming, being compatible with almost all audio and video formats.
```
sudo dnf install ffmpeg ffmpeg-devel x264 x265
```
- The dependencies are found in the "RPMFusion" repositories

# 7. Hardware Video Acceleration (Base)

## Libva
Libva allows multimedia players and tools to use the GPU to decode, encode, or process video, reducing the load on the CPU and improving performance.
```
sudo dnf install libva
```
## Mesa
Mesa is a open source graphics drivers and libraries that allow Linux to use 3D and 2D acceleration through APIs like OpenGL and Vulkan, improving your GPU performance.
```
sudo dnf install mesa-dri-drivers
sudo dnf install mesa-libgbm
```

# 8. Hardware Acceleration + GPU (NVIDIA/AMD/INTEL)

Here, depending on your hardware, you need to install these packages to get the most out of your GPU (or CPU with integrated graphics)

## NVIDIA

### Libva + NVIDIA
```
sudo dnf install libva-nvidia-driver
```

## AMD
AMD doesn't have separate drivers like Intel or NVIDIA; all AMD video acceleration is integrated on Mesa, we also use the previous command from the Mesa section.
```
sudo dnf install mesa-dri-drivers
sudo dnf install mesa-libgbm
```

## Intel
### Libva + Intel (Integrated Graphics) (Modern) (Haswell CPU or Higher)
```
sudo dnf install libva-intel-driver
```
### Libva + Intel (Integrated Graphics) (Old) (G45, Sandy Bridge, Ivy Bridge or older)
```
sudo dnf install libva-intel-hybrid-driver
```
### Libva + Intel (Discrete GPU) (Arc / Intel Iris Xe MAX)
```
sudo dnf install intel-media-driver
```
> [!NOTE]
> You can also install all packages if you want


# 9. Optimizations
## Disable "NetworkManager-wait-online.service" service

> [!NOTE]
> By default, Linux verify that you have internet connection, in case you don't have internet or your Wifi/Bluetooth device is not detected, it can end up in an infinite loop.

```
sudo systemctl disable NetworkManager-wait-online.service
```
## Disable Gnome Software when starting the computer
```
sudo rm /etc/xdg/autostart/org.gnome.Software.desktop
```
> [!NOTE]
> Reduces between 50 and 200MB of RAM, which is a good addition if you have limited RAM

# 10. Additional Add-ons
## Gnome Tweaks
```
sudo dnf install gnome-tweaks
```

# Observations
- Terra does not work on RHEL-based distros

# To-Do / Things to Add

- Printers and Scanners
- Performance on Laptops
- Optimization on Laptops (AMD/NVIDIA/INTEL)
- Tutorial for AMD / Intel (Arc/Iris) drivers
- Better documentation when having Secure Boot Enabled (Kernel and NVIDIA Drivers)
- Add alternatives to NVIDIA drivers (ElRepo and RPM Fusion)
- System behavior with the Updated Kernel
- Check if the system can break with general updates
- Behavior between Versions of Rocky 10 (Ex: 10.0 to 10.1 and future versions)

If you would like me to format this as a Markdown file download or assist with any specific section of the guide, just let me know!
