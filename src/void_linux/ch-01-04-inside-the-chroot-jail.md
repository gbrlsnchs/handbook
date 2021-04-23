# Inside the chroot jail
## Basic configuration
### Hostname
Write the desired hostname to `/etc/hostname`.

### System configuration information
Refer to [this documentation](https://docs.voidlinux.org/config/rc-files.html#rcconf) in order to
configure your `rc.conf` file.

### Configuring locales
For _glibc_ installations, edit `/etc/default/libc-locales`, then run:
```console
chroot# xbps-reconfigure -f glibc-locales
```

### Root password
```console
chroot# passwd
```

### Configuring fstab
If you store the appropriate data before creating the fstab file, you don't even need an editor:
```console
chroot# UEFI_UUID=$(blkid -s UUID -o value /dev/sda1)
chroot# GRUB_UUID=$(blkid -s UUID -o value /dev/sda2)
chroot# ROOT_UUID=$(blkid -s UUID -o value /dev/mapper/cryptoroot)
chroot# cat <<EOF > /etc/fstab
UUID=$ROOT_UUID / btrfs $BTRFS_OPTS,subvol=@ 0 1
UUID=$UEFI_UUID /efi vfat defaults,noatime 0 2
UUID=$GRUB_UUID /boot ext2 defaults,noatime 0 2
UUID=$ROOT_UUID /home btrfs $BTRFS_OPTS,subvol=@home 0 2
UUID=$ROOT_UUID /.snapshots btrfs $BTRFS_OPTS,subvol=@snapshots 0 2
tmpfs /tmp tmpfs defaults,nosuid,nodev 0 0
EOF
```

### Setting up Dracut
I suggest doing a _hostonly_ install, that is, Dracut  will generate a lean initramfs with
everything you might need:
```console
chroot# echo hostonly=yes >> /etc/dracut.conf
```
> **Note:** For example, with _hostonly_ activated, Dracut will include i915 DRM drivers if you have
> an Intel CPU with integrated graphics.
