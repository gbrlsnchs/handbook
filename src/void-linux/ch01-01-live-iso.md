# Live ISO
For this tutorial, you'll need to download the _base_ image from Void's official download page,
which can be found [here](https://voidlinux.org/download/). You'll have to choose between the
_glibc_ one and the _musl_ one.

One question people make often is why the live ISO image is "not up-to-date". Void is a _rolling
release_ distro. It's different from Ubuntu, which has release cycles. Software in Void is
constantly getting updated, so regardless of how old the image is, you'll end up downloading and
installing the most recent packages there exists in the Void repos.

> **Note:** A possible issue in old live ISO images is lack of support for most recent hardware.
> This is uncommon to happen, but if it does, resort to official channels for help.

## Logging in
There are two available users, `root` (superuser) and `anon`. The password of both is `voidlinux`
(it might vary from image to image, but it's always explict in the starting screen). I prefer to log
in as superuser so I don't have to type `sudo` all the time. Also, I highly recommend that you run
`exec bash` since it is a little more comfortable to work with than `dash`, the default shell for
the live ISO image.

## Configuring keyboard layout
If you need a different layout other than `en_US` during the install process, you can change it
using `loadkeys`, for example:
```console
# loadkeys $(ls /usr/share/kbd/keymaps/i386/**/*.map.gz | grep br-abnt2)
```

## Connecting to the internet
If you're connect via ethernet, you're good to go, since the live ISO image already runs the DHCP
service by default. However, if you're on Wi-Fi, you'll need additional setup. For example, to
configure it for interface `wlan0`:
```console
# cp /etc/wpa_supplicant/wpa_supplicant.conf /etc/wpa_supplicant/wpa_supplicant-wlan0.conf
# wpa_passphrase $SSID $PASS >> /etc/wpa_supplicant/wpa_supplicant-wlan0.conf
# ip link set up wlan0
# sv restart dhcpcd
```
