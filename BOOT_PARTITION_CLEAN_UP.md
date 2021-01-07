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

1. Remove initrd.img of old kernels

    ```bash
    $ sudo update-initramfs -d -k 5.4.0-58-generic
    update-initramfs: Deleting /boot/initrd.img-5.4.0-58-generic
    
    $ sudo update-initramfs -d -k 5.4.0-59-generic
    update-initramfs: Deleting /boot/initrd.img-5.4.0-59-generic
    ```

1. Now we'll use dpkg in order to TRY to purge the kernel package for the same old kernel: 

    ```bash
    sudo dpkg --purge linux-image-4.2.0-15-generic
    ```
    
## References

- [Safely Removing Old Kernels](https://help.ubuntu.com/community/RemoveOldKernels#Safely_Removing_Old_Kernels)
