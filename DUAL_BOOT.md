# Dual Boot with Ubuntu 20.04 LUKS Encrypted and Windows 10 Bitlocker encrypted

This HOW TO describe how I installed Ubuntu 20.04 with LUKS full Hard Disk Encryption together with Windows 10 setup with Bitlocker on a DELL XPS 15 Laptop.

# 1. Deactivate Intel RST in UEFI BIOS

Ubuntu cannot be installed when Intel RST is enabled. Therefore I deactivated in BIOS.

# 2. Reinstall Windows 10

When you deactivate the Intel RST feature you have either to completely reinstall Windows 10 (NOTE: using the DELL Recovery Image doesn't work as it doesn't install Windows but put back on an Image that has been setup with RST) or have to mess around with the Windows registery. As my laptop was brand new and did not have any data on it, I choosed to reinstall it in order to avoid messing up with the registry. If you decide to keep windows there are plenty of help online to disable RST on existing Windows (I haven't tested any of them).

# 3. Make room for Linux with the Windows Disk Managment Tool

I shrink my C: partition using Windows Disk Management Tool (see https://www.dell.com/support/article/de-ch/sln301754/how-to-install-ubuntu-and-windows-8-or-10-as-a-dual-boot-on-your-dell-pc?lang=en) and left the space unalocated. For my linux I left 128GB.

# 4. Make a Ubuntu 20.04 USB Bootable

Although this should a very easy step with plenty of tutorial on how to do it online, it gave troubles. I created a Ubuntu 20.04 bootable USB using the Ubuntu `Startup Disk Creator`. Unfortunately the first two USB that I used did not work, during the installation process of Ubuntu (see below), ubuntu installer freezed and the USB key was then not usable anymore! The third key that I did then work perfectly.

# 5. Install Ubuntu 20.04 with LUKS Encryption

Follow the step defined in https://medium.com/@chrishantha/encrypting-disks-on-ubuntu-19-04-b50bfc65182a

As mentioned in above I had to repeat this procedure 3 times as the first two did not worked and broke my USB Key. No idea why, both USB Key were from the same Vendor ! The third USB that I used was from a different Vendor.

# 6. Configure auto remove old kernel to avoid /boot partition full

See https://help.ubuntu.com/community/RemoveOldKernels#Configure_Unattended_Upgrades_to_Remove_Unneeded_Kernels_Automatically

## Sound Driver

This command seems to fix some bad sound quality with my DELL XPS 15.

```bash
sudo apt install linux-oem-20.04
```

## References

- https://medium.com/@chrishantha/encrypting-disks-on-ubuntu-19-04-b50bfc65182a
- https://www.dell.com/support/article/de-ch/sln301754/how-to-install-ubuntu-and-windows-8-or-10-as-a-dual-boot-on-your-dell-pc?lang=en
- https://itectec.com/ubuntu/ubuntu-ubuntu-dual-boot-with-lvm-and-luks-install-fails/
- https://askubuntu.com/questions/293028/how-can-i-install-ubuntu-encrypted-with-luks-with-dual-boot
