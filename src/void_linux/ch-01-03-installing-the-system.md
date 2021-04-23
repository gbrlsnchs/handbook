# Installing the system
## Base installation
First, set some helper variables:
```console
# REPO=https://alpha.us.repo.voidlinux.org/current
# ARCH=x86_64
```
If going for the _musl_ variant, just reset the variables accordingly:
```console
# REPO="$REPO/musl"
# ARCH="$ARCH-musl"
```
Then proceed to install the base system:
```console
# XBPS_ARCH=$ARCH xbps-install -S -R "$REPO" -r /mnt base-system btrfs-progs cryptsetup
```

> **Note:** The command above installs the base system (`base-system`), along with btrfs utilities
> (`btrfs-progs`) and dm-crypt utilities (`cryptsetup`).

## Running `chroot`
Mount the pseudo file systems needed for chroot:
```console
# for dir in dev proc sys run; do mount --rbind /$dir /mnt/$dir; mount --make-rslave /mnt/$dir; done
```

Copy the DNS configuration into the new root so that XBPS can still download new packages inside the
chroot:
```console
# cp /etc/resolv.conf /mnt/etc/
```

Finally, `chroot` into the new installation:
```console
# BTRFS_OPTS=$BTRFS_OPTS PS1='chroot# ' chroot /mnt/ /bin/bash
```

> **Note:** The variable `BTRFS_OPTS` is passed into the chroot so we can use it later when setting
> fstab (`/etc/fstab`).
