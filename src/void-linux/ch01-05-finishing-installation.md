# Finishing installation
## Intel microcode
```console
chroot# xbps-install -Su void-repo-nonfree intel-ucode
```

> **Note:** If you're using an AMD CPU, install `linux-firmware-amd` instead.

## GRUB
```console
chroot# xbps-install grub-x86_64-efi
chroot# grub-install --target=x86_64-efi --efi-directory=/efi --bootloader-id="Void Linux"
```

## Swapfile
In order to have an encrypted swap, let's use a more clean approach by using a _swapfile_ for,
_duh_, swap. For this example, I'll create a swapfile of 16 GiB, but you can choose the best size
for your installation:
```console
chroot# truncate -s 0 /var/swap/swapfile
chroot# chattr +C /var/swap/swapfile
chroot# btrfs property set /var/swap/swapfile compression none
chroot# chmod 600 /var/swap/swapfile
chroot# dd if=/dev/zero of=/var/swap/swapfile bs=1G count=16 status=progress
chroot# mkswap /var/swap/swapfile
chroot# swapon /var/swap/swapfile
```

Now, you'll need a tool to calculate the `resume_offset` kernel parameter for btrfs. You can either
follow [this guide on Arch Linux's
wiki](https://wiki.archlinux.org/index.php/Power_management/Suspend_and_hibernate#Hibernation_into_swap_file_on_Btrfs)
or you can use XBPS to build [a template I maintain
myself](https://github.com/gbrlsnchs/void-packages/tree/btrfs_map_physical) of the same code cited
in Arch's wiki.

After calculating the value, set it to a variable, let's say, `RESUME_OFFSET`, and append the
following to GRUB's config:
```console
chroot# echo GRUB_CMDLINE_LINUX="resume=UUID=$ROOT_UUID resume_offset=$RESUME_OFFSET" >> /etc/default/grub
```

> **Note:** This won't be an issue with Void, but be aware that you need Linux kernel version 5.0+
> in order to use a swapfile with btrfs.

## Regenerating configurations
Finally, regenerate configurations and reboot the machine:
```console
chroot# xbps-reconfigure -fa
chroot# exit
# umount -R /mnt
# shutdown -r now
```
