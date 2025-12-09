# Arch-Installation-Report

 Mohammad Paiman Faqirzada <br>
 CYB 3353 – Arch Linux Installation Project 1 <br>
 Codi West <br>
 
**Introduction**
This project involved manually installing and configuring Arch Linux inside a VirtualBox virtual machine on macOS (Apple Silicon). I used my friend’s MacBook since my Windows laptop couldn’t run the ARM 64 version of Arch Linux due to its lightweight hardware.
 The main objective was to build an operating system completely from scratch — learning how each layer, from partitions to the graphical interface, fits together.
Arch Linux is known for its simplicity, flexibility, and do-it-yourself philosophy. Installing it manually gave me a deeper understanding of how Linux works internally — from managing storage and setting up a bootloader to customizing the shell and enabling networking.
By the end of this project, I had a fully functional system running the GNOME desktop environment with Zsh as my default shell.

# Step 1: Setting Up the Virtual Machine

I began by downloading two essential files:

•⁠  ⁠[VirtualBox](https://www.virtualbox.org/wiki/Downloads)  
•⁠  ⁠[Archboot ISO (AARCH64)](https://release.archboot.com/aarch64/latest/iso/)

After installing VirtualBox, I created a new virtual machine and configured it with the following settings:

| Setting | Value |
|----------|--------|
| OS Type | Linux (ARM 64-bit) |
| Memory | 8 GB |
| CPUs | 10 |
| Storage | 20 GB |
| Boot Mode | UEFI |

Once the Archboot ISO was attached, I launched the VM to begin the installation.

---

# Step 2: Launching the Arch Installer

After booting into the Archboot ISO, the startup menu appeared. I selected “Launch UEFI Archboot” to enter the live installation environment.

The setup began by:
1.⁠ ⁠Selecting the preferred language and locale  
2.⁠ ⁠Choosing “No” for online mode (to use the local ISO packages)  
3.⁠ ⁠Configuring the timezone and region  
4.⁠ ⁠Launching Archboot Setup to begin disk partitioning  

This phase prepared the environment for installing the base operating system.
# Step 3: Disk Partitioning

Inside the Archboot setup, I selected “Prepare Storage Device → Quick Setup” to automatically configure my virtual drive ⁠ /dev/sda ⁠.

The partition layout created was:

| Partition | Mount Point | File System | Size | Purpose |
|------------|--------------|--------------|------|----------|
| /dev/sda1 | /efi | FAT32 | 512 MB | EFI System Partition |
| /dev/sda2 | — | SWAP | 2 GB | Swap Space |
| /dev/sda3 | / | ext4 | 10 GB | Root Directory |
| /dev/sda4 | /home | ext4 | 8 GB | Home Directory |

This setup ensured separate spaces for the system, user files, and swap memory.  
Once confirmed, I formatted all partitions and authorized data deletion on the drive to finalize the storage configuration.

---

## Step 4: Installing the Base System

After preparing the storage, I selected “Install Packages” and installed the following essential components:
<br>base
<br>linux
<br>linux-firmware
<br>networkmanager
<br>zsh

I included Zsh during the installation process so it would be available as the system’s default shell immediately after setup.  
Once all the core packages were installed, I generated the fstab file to define how partitions are mounted each time the system boots.

---

## Step 5: System Configuration

I configured the system’s identity and localization files manually:
etc/locale.conf → LANG=en_US.UTF-8
/etc/locale.gen → Un-commented “en_US.UTF-8 UTF-8”
/etc/hostname → archlinux
/etc/hosts → 127.0.1.1 archlinux.localdomain archlinux
Then I set the timezone and synced the hardware clock:

```bash
ln -sf /usr/share/zoneinfo/America/Chicago /etc/localtime  
hwclock --systohc  
locale-gen
```
Finally, I created a user account and set Zsh as the default shell:
useradd -m -G wheel -s /bin/zsh paiman  
passwd paiman  
EDITOR=nano visudo
useradd -m -G wheel -s /bin/zsh paiman  
passwd paiman  
EDITOR=nano visudo
nside the visudo file, I enabled admin privileges by un-commenting:
wheel ALL=(ALL:ALL) ALL

Step 6: Bootloader Installation

With the base system ready, I installed and configured the GRUB bootloader to enable system startup:
pacman -S grub efibootmgr  
grub-install --target=arm64-efi --efi-directory=/efi --bootloader-id=GRUB  
grub-mkconfig -o /boot/grub/grub.cfg

After completing the setup, I rebooted and successfully booted into my new Arch Linux installation.

Step 7: Installing and Enabling SSH

Once logged in, I installed OpenSSH to allow secure remote access:

sudo pacman -Sy openssh  
sudo systemctl start sshd  
sudo systemctl enable sshd

Then I verified that SSH was running:
systemctl status sshd

This allowed me to connect to my Arch VM remotely.

Step 8: Configuring Zsh and Enabling Color

Since Zsh was installed earlier, I customized it after the first boot.

I opened the configuration file:
nano ~/.zshrc
Added color-coded aliases for better readability:
alias ls='ls --color=auto'  
alias grep='grep --color=auto'  
alias diff='diff --color=auto'  
PROMPT='%F{blue}%n@%m%f:%F{red}%~%f%# '

Then reloaded the configuration:
source ~/.zshrc
To test color support, I created a sample directory and confirmed that folders appeared in blue.

Step 9: Adding More Aliases

To make the terminal more efficient, I added additional shortcuts:
alias la='ls -A --color=auto'  
alias l='ls -CF --color=auto'

Step 10: Installing the GNOME Desktop Environment

To add a graphical interface, I installed the GNOME Desktop Environment and the GDM display manager:
sudo pacman -S gnome  
sudo pacman -S gdm  
sudo systemctl enable --now gdm.service

After rebooting, the GNOME login screen appeared. Logging in as “paiman” opened a clean, functional GNOME desktop environment — completing the installation.

Step 11: Reflection

This project was both challenging and rewarding.
Unlike automated installers, Arch Linux requires every step to be done manually, which helped me truly understand each configuration and command.

From partitioning disks to installing GNOME, I learned how Linux systems are built piece by piece.
The process improved my patience, attention to detail, and ability to troubleshoot — skills essential in system administration and cybersecurity.

By the end, I successfully created a stable and efficient environment featuring:

A complete GNOME desktop

Working SSH connectivity

A Zsh shell (installed during setup) with color coding and aliases

A properly configured GRUB bootloader
