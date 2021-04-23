# Installing Void
In this guide you will learn how to get a working installation of Void containing:
- btrfs with Zstandard compression
- LUKS-encrypted root and swapfile
- GRUB with UEFI

Things you will _not_ find in this handbook:
- Instructions for file systems other than btrfs
- Full disk encryption (there's an official guide
  [here](https://docs.voidlinux.org/installation/guides/fde.html))
- Reasoning for all choices I've made

> **Note:** Sometimes I don't know the true reason for all my choices. 😬
