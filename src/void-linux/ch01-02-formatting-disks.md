# Formatting disks
## Partitioning
The minimum number of partitions is three:
- The EFI partition, where the GRUB bootloader resides (`/efi`)
- The boot partition, where kernels are stored (`/boot`)
- The LUKS-encrypted btrfs root partition

First, we need to generate the partition tables. Let's set the disk up:
```console
# fdisk /dev/sda
```
> **Note:** Check which device is the one you want to install Void into. For this guide I'll simply
> use `/dev/sda`, but it can change depending on your setup, so _watch out!_

After running `fdisk`, it will prompt you with a menu, so follow these steps:
1. Press <kbd>g</kbd> and then <kbd>Enter</kbd> to generate a GPT table
2. Press <kbd>n</kbd> and then <kbd>Enter</kbd> to create the EFI partition with size of 200 MiB
   (`+200M`)
3. After creating the partition, change its type by pressing <kbd>t</kbd> and then <kbd>Enter</kbd>
   and select the option that represents the EFI partition (usually 1)
4. Press <kbd>n</kbd> and then <kbd>Enter</kbd> to create the boot partition with size of at least
   500 MiB (`+500M`)
5. If the default partition type selected for the root partition is not already `Linux Filesystem`,
   change it by pressing <kbd>t</kbd> and then selecting it (usually 20)

## Creating the file systems
### EFI partition
```console
# mkfs.vfat -nBOOT -F32 /dev/sda1
```

### Boot partition
```console
# mkfs.ext2 -L grub /dev/sda2
```

### LUKS-encrypted root partition
```console
# cryptsetup luksFormat --type=luks -s=512 /dev/sda3
# cryptsetup open /dev/sda3 cryptroot
# mkfs.btrfs -L void /dev/mapper/cryptroot
```

## Mounting 
Now that the file systems have been created, it's time to mount them.
### Root partition
For the root partition, it is necessary to first create _top-level subvolumes_:
```console
# BTRFS_OPTS="rw,noatime,ssd,compress=zstd,space_cache,commit=120"
# mount -o $BTRFS_OPTS /dev/mapper/cryptroot /mnt
# btrfs subvolume create /mnt/@
# btrfs subvolume create /mnt/@home
# btrfs subvolume create /mnt/@snapshots
# umount /mnt
```
Then, proceed to mount them:
```console
# mount -o $BTRFS_OPTS,subvol=@ /dev/mapper/cryptroot /mnt
# mkdir /mnt/home && mount -o $BTRFS_OPTS,subvol=@home /dev/mapper/cryptroot /mnt
# mkdir /mnt/.snapshots && mount -o $BTRFS_OPTS,subvol=@snapshots /dev/mapper/cryptroot /mnt
```

> **Note:** Those are the options and names I like to use for btrfs. Feel free to change them
> according to your needs!

### Extra subvolumes
Since btrfs doesn't take snapshots of nested subvolumes, it is a good practice to create some extra,
nested subvolumes for directories that are not meant to be in the snapshots:
```console
# mkdir -p /mnt/var/cache
# btrfs subvolume create /mnt/var/cache/xbps
# btrfs subvolume create /mnt/var/tmp
# btrfs subvolume create /mnt/srv
```
Also, if you're going for the swapfile, don't forget to create a nested subvolume for it:
```console
# btrfs subvolume create /mnt/var/swap
```

> **Note:** Nested subvolumes are mounted automatically when their parent subvolume is mounted.

### EFI and boot partitions
Once the root partition is ready, it is time to mount the remaining ones:
```console
# mkdir /mnt/efi && mount -o rw,noatime /dev/sda1 /mnt/efi
# mkdir /mnt/boot && mount -o rw,noatime /dev/sda2 /mnt/boot
```
