# Dual Boot with Ubuntu 20.04 LUKS Encrypted and Windows 10 Bitlocker encrypted

This HOW TO describe how I installed Ubuntu 20.04 with LUKS full Hard Disk Encryption together with Windows 10 setup with Bitlocker on a DELL XPS 15 Laptop.

# 1. Deactivate Intel RST in UEFI BIOS

Ubuntu cannot be installed when Intel RST is enabled. Therefore I deactivated in BIOS.

# 2. Reinstall Windows 10

When you deactivate the Intel RST feature you have either to completely reinstall Windows 10 (NOTE: using the DELL Recovery Image doesn't work as it doesn't install Windows but put back on an Image that has been setup with RST) or have to mess around with the Windows registery. As my laptop was brand new and did not have any data on it, I choosed to reinstall it in order to avoid messing up with the registry. If you decide to keep windows there are plenty of help online to disable RST on existing Windows (I haven't tested any of them).

# 3. Make room for Linux with the Windows Disk Managment Tool

I shrink my C: partition using Windows Disk Management Tool (see https://www.dell.com/support/article/de-ch/sln301754/how-to-install-ubuntu-and-windows-8-or-10-as-a-dual-boot-on-your-dell-pc?lang=en) and left the space unalocated. 

For my linux I left 128GB.


⚠️ *Turns out that when using a lot docker, 128GB is not enough and I have to regularly purge my docker containers/images*

# 4. Make a Ubuntu 20.04 USB Bootable

Although this should a very easy step with plenty of tutorial on how to do it online, it gave troubles. I created a Ubuntu 20.04 bootable USB using the Ubuntu `Startup Disk Creator`. Unfortunately the first two USB that I used did not work, during the installation process of Ubuntu (see below), ubuntu installer freezed and the USB key was then not usable anymore! The third key that I did then work perfectly.

# 5. Install Ubuntu 20.04 with LUKS Encryption

Follow the step defined in https://medium.com/@chrishantha/encrypting-disks-on-ubuntu-19-04-b50bfc65182a

As mentioned in above I had to repeat this procedure 3 times as the first two did not worked and broke my USB Key. No idea why, both USB Key were from the same Vendor ! The third USB that I used was from a different Vendor.

Based on the link above I create 3 partitions:_

1. /boot => 512MB
2. / (root) => 48.8GB
3. /home => 79GB

⚠️ **512MB for my /boot partition is not enough for ubuntu 20.04 !!! I have to often do some manual kernel cleanup due to /boot being full, see https://github.com/ltshb/howto/blob/main/BOOT_PARTITION_CLEAN_UP.md. I would recommend at 1GB for /boot.**

# 6. Configure auto removal of old kernel to avoid filling /boot partition

Configure auto removal of old kernel as follow

1. Open the file `/etc/apt/apt.conf.d/50unattended-upgrades`

    ```bash
    sudo vi /etc/apt/apt.conf.d/50unattended-upgrades
    ```

1. Uncomment the following lines

    ```bash
    Unattended-Upgrade::Remove-Unused-Kernel-Packages "true";
    Unattended-Upgrade::Remove-New-Unused-Dependencies "true";
    ```

1. Uncomment the following line and set it to true

    ```bash
    Unattended-Upgrade::Remove-Unused-Dependencies "true";
    ```

For more infos or manual removal of old image in /boot see https://help.ubuntu.com/community/RemoveOldKernels#Configure_Unattended_Upgrades_to_Remove_Unneeded_Kernels_Automatically

## Time conflict

See http://ubuntuhandbook.org/index.php/2016/05/time-differences-ubuntu-1604-windows-10/

To fix time conflict between windows an ubuntu when switching enter the following command in Windows cmd as admin. This command configure windows to use UTC in RTC instead of local time

```
Reg add HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_QWORD /d 1
```

## Sound Driver

To fix some bad sound quality with my DELL XPS 15 I reinstall the sound card ALSA driver and also installed pulseaudio.

1. Remove the ALSA packages.

    ```bash
    sudo apt --purge remove linux-sound-base alsa-base alsa-utils
    ```
 
 1. Reinstall the same packages.
 
    ```bash
    sudo apt-get install linux-sound-base alsa-base alsa-utils
    sudo apt install gdm3 ubuntu-desktop
    ```
    
1. Install pulseaudio

    ```bash
    sudo add-apt-repository ppa:nilarimogard/webupd8
    sudo apt-get update
    sudo apt-get install pulseaudio pulseaudio-utils pulseaudio-equalizer
    ```

1. Reboot

    ```bash
    sudo reboot
    ```

See https://askubuntu.com/questions/722685/realtek-audio-drivers-for-ubuntu

## WIFI

```bash
sudo sed -i 's/wifi.powersave = 3/wifi.powersave = 2/' /etc/NetworkManager/conf.d/default-wifi-powersave-on.conf
```

```bash
sudo apt-get install --reinstall linux-firmware
```

```bash
sudo vi /etc/default/crda
```

then

```bash
REGDOMAIN=CH
```

## Crash

Install the following driver to avoid a ubuntu crash

```bash
sudo apt install ubuntu-drivers-common
```

## Battery life

To increase battery life on Battery install tlp

https://doc.ubuntu-fr.org/tlp

```bash
sudo apt install tlp
```

The use this config 

```bash
sudo cp asset/dual-boot/tlp.conf /etc/
```

Install tlp graphic tool

```bash
sudo add-apt-repository ppa:slimbook/slimbook

sudo apt update

sudo apt install slimbookbattery
```

Install prime-select

```bash
sudo apt install nvidia-prime
sudo prime-select intel
```

## References

- https://medium.com/@chrishantha/encrypting-disks-on-ubuntu-19-04-b50bfc65182a
- https://www.dell.com/support/article/de-ch/sln301754/how-to-install-ubuntu-and-windows-8-or-10-as-a-dual-boot-on-your-dell-pc?lang=en
- https://itectec.com/ubuntu/ubuntu-ubuntu-dual-boot-with-lvm-and-luks-install-fails/
- https://askubuntu.com/questions/293028/how-can-i-install-ubuntu-encrypted-with-luks-with-dual-boot
