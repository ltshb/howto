# Boot Partition Clean Up

# Safely removing old kernels from /boot partition

1. `sudo rm -rv ${TMPDIR:-/var/tmp}/mkinitramfs-*`
1. Determine current kernel

    ```bash
    $ uname -r
    5.6.0-1036-oem
    ```
    
1. List all Kernels

    ```bash
    $ dpkg -l | tail -n +6 | grep -E 'linux-image-[0-9]+'
    ii  linux-image-5.4.0-58-generic               5.4.0-58.64                               amd64        Signed kernel image generic
    ii  linux-image-5.4.0-59-generic               5.4.0-59.65                               amd64        Signed kernel image generic
    ii  linux-image-5.6.0-1036-oem                 5.6.0-1036.39                             amd64        Signed kernel image oem
    iF  linux-image-5.6.0-1039-oem                 5.6.0-1039.43                             amd64        Signed kernel image oem
    ii  linux-image-5.8.0-34-generic               5.8.0-34.37~20.04.2                       amd64        Signed kernel image generic
    ```

1. Manual removal
    1. Remove initrd.img of old kernels

        ```bash
        $ sudo update-initramfs -d -k 5.4.0-58-generic
        update-initramfs: Deleting /boot/initrd.img-5.4.0-58-generic
        
        $ sudo update-initramfs -d -k 5.4.0-59-generic
        update-initramfs: Deleting /boot/initrd.img-5.4.0-59-generic
        ```

    1. Now we'll use dpkg in order to TRY to purge the kernel package for the same old kernel: 

        ```bash
        sudo dpkg --purge linux-image-5.4.0-58-generic
        (Reading database ... 331840 files and directories currently installed.)
        Removing linux-image-5.4.0-58-generic (5.4.0-58.64) ...
        debconf: DbDriver "config": /var/cache/debconf/config.dat is locked by another process: Resource temporarily unavailable
        dpkg: error processing package linux-image-5.4.0-58-generic (--purge):
        installed linux-image-5.4.0-58-generic package pre-removal script subprocess returned error exit status 1
        Errors were encountered while processing:
        linux-image-5.4.0-58-generic
        ```
    
1. Auto removal

    1. Instead of the setps above do the following command

        ```bash
        kernel=$(uname -r); for kernel in $(dpkg -l | tail -n +6 | grep -E 'linux-image-[0-9]+' | awk '{print $2}' | awk  '!/'${kernel}'/ {print $1}' | sed 's/linux-image-//'); do sudo update-initramfs -d -k ${kernel}; sudo dpkg --purge linux-image-${kernel}; echo "${kernel} removed"; done
        ```
        
1. If `/var/cache/debconf/config.dat` is locked, then unlock it and retry the step above

    ```bash
    sudo fuser -k /var/cache/debconf/config.dat
    ```
    
1. Check `/boot` partition size again

    ```bash
    sudo baobab
    ```
    
## References

- [Safely Removing Old Kernels](https://help.ubuntu.com/community/RemoveOldKernels#Safely_Removing_Old_Kernels)
