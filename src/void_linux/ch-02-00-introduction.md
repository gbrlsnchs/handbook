# Post-installation
If everything went well in the previous chapter, your Void system has successfully booted and you
have unlocked your disk in order to be prompted with the login screen in a terminal. Since you only
have root available, the first thing to be done is _not_ to be root anymore, so let's create a user
account.

## Creating the main user
Log in as root, set your username to a variable, let's say, `MY_USERNAME`, and run:
```console
# xbps-install -S zsh
# useradd -m -G wheel,input,video -s /bin/zsh $MY_USERNAME
# passwd $MY_USERNAME
# visudo
```

After running `visudo`, uncomment the line that contains `%wheel`. Log out and then log in with the
newly created user.

> **Note:** If you want to lock down the root account, you can run `sudo passwd -dl root`. Be
> careful though, as you won't be able to log in using the root account anymore, so make sure your
> account has root privileges with `sudo`!
