---
layout: migration
title: Migration to Artix
permalink: /migrate/
sub_page: true
---


Existing OpenRC systems (whether arch-openrc or manjaro-openrc) can be converted to the new scheme with minimal effort. Older eudev-openrc ones might need some more tweaking, especially with concern to the desktop: consolekit2 is replaced by elogind. In any case, the procedure outlined below is meant for OpenRC systems only. Those with vanilla Arch or Manjaro must first <a href="http://systemd-free.org/migrate.php">migrate as described on systemd-free.org</a>.

## Summary
{: .has-text-danger }

In brief, the new repos must be placed before the official Arch or Manjaro ones. Then, sysvinit (provided now by openrc itself) and systemd-sysusers must be removed, as well as consolekit which is replaced by elogind. Next, the base and base devel group must be installed from the new repos and a full-system upgrade must be run. All -nosystemd and consolekit packages have to be replaced by their equivalent too. Finally udev, dbus and elogind services must be enabled and mkinitcpio has to be run for your kernel.

The procedure is roughly the following, it widely depends on individual setups and it's still in beta. <font color="#F99">PROCEED WITH CAUTION!</font> You must be fairly confortable with Linux and keep an extra kernel in /boot and a bootable medium nearby. <a href="https://sourceforge.net/projects/artix-linux/files/iso/lxqt/">Fresh install ISOs</a> <strike>will be available soon</strike> are already available!
<br><br>

The guide is work-in-progress, updated with each report we receive. If something needs correction for your setup, report it!

Let's begin

# Setup Repositories
{: #repositories}

Put these repos in /etc/pacman.conf *before* the official Arch/Manjaro ones and disable [core] of the latter:

```
# Artix repos
[system]
Include = /etc/pacman.d/mirrorlist
[world]
Include = /etc/pacman.d/mirrorlist
[galaxy]
Include = /etc/pacman.d/mirrorlist

# Arch repos, overriden and [core] disabled
# [core]
# Include = /etc/pacman.d/mirrorlist-arch
[extra]
Include = /etc/pacman.d/mirrorlist-arch
[community]
Include = /etc/pacman.d/mirrorlist-arch
```


>_The [arch-openrc] and [arch-nosystemd] repos (or [openrc-eudev] if you're still on it) must be disabled._
{: .is-danger}

> The [multilib] repo will eventually be available too.
{: .is-info}

# Setup mirrors
{: #mirrors}

Rename /etc/pacman.d/mirrorlist to /etc/pacman.d/mirrorlist-arch

```bash
mv /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist-arch
```

Create a new /etc/pacman.d/mirrorlist, refresh the database and install the new keyring. The new keyring can be installed either by lowering the security levels in pacman.conf or by ignoring pacman, see below.
```
# cat > /etc/pacman.d/mirrorlist <<\EOF
# Worldwide mirrors
Server = https://netcologne.dl.sourceforge.net/project/artix-linux/repos/$repo/os/$arch
Server = https://artix.mief.nl/repos/$repo/os/$arch
Server = https://freefr.dl.sourceforge.net/project/artix-linux/repos/$repo/os/$arch
Server = https://10gbps-io.dl.sourceforge.net/project/artix-linux/repos/$repo/os/$arch
Server = https://netix.dl.sourceforge.net/project/artix-linux/repos/$repo/os/$arch
Server = https://kent.dl.sourceforge.net/project/artix-linux/repos/$repo/os/$arch
Server = https://superb-dca2.dl.sourceforge.net/project/artix-linux/repos/$repo/os/$arch
Server = https://pilotfiber.dl.sourceforge.net/project/artix-linux/repos/$repo/os/$arch
Server = https://iweb.dl.sourceforge.net/project/artix-linux/repos/$repo/os/$arch
Server = https://managedway.dl.sourceforge.net/project/artix-linux/repos/$repo/os/$arch
Server = https://cfhcable.dl.sourceforge.net/project/artix-linux/repos/$repo/os/$arch
Server = https://svwh.dl.sourceforge.net/project/artix-linux/repos/$repo/os/$arch
Server = https://versaweb.dl.sourceforge.net/project/artix-linux/repos/$repo/os/$arch
Server = https://cytranet.dl.sourceforge.net/project/artix-linux/repos/$repo/os/$arch
Server = https://gigenet.dl.sourceforge.net/project/artix-linux/repos/$repo/os/$arch
Server = https://excellmedia.dl.sourceforge.net/project/artix-linux/repos/$repo/os/$arch
Server = https://liquidtelecom.dl.sourceforge.net/project/artix-linux/repos/$repo/os/$arch
Server = https://phoenixnap.dl.sourceforge.net/project/artix-linux/repos/$repo/os/$arch
Server = https://superb-sea2.dl.sourceforge.net/project/artix-linux/repos/$repo/os/$arch
Server = https://ayera.dl.sourceforge.net/project/artix-linux/repos/$repo/os/$arch
Server = https://ufpr.dl.sourceforge.net/project/artix-linux/repos/$repo/os/$arch
Server = https://razaoinfo.dl.sourceforge.net/project/artix-linux/repos/$repo/os/$arch
Server = https://jaist.dl.sourceforge.net/project/artix-linux/repos/$repo/os/$arch
Server = https://ncu.dl.sourceforge.net/project/artix-linux/repos/$repo/os/$arch
Server = https://nchc.dl.sourceforge.net/project/artix-linux/repos/$repo/os/$arch
Server = https://vorboss.dl.sourceforge.net/project/artix-linux/repos/$repo/os/$arch
EOF
```

Clean all cache and force sync:
```
pacman -Scc && pacman -Syy
```

# Install keyring
{: #keyring}
To allow installation of our keyring, change pacman.conf directives:
```
SigLevel          = Never #Required DatabaseOptional
LocalFileSigLevel = Never #Optional
```

Now run:

```bash
pacman -S artix-keyring
pacman-key --populate artix
```
And restore pacman.conf security. Alternatively, don't touch pacman.conf, but ignore pacman's warnings and leave artix-keyring in the package cache:
```
pacman -Sw artix-keyring
```
Say "y" to signature imports and "n" to deleting corrupted packages.
```
pacman -U /var/cache/pacman/pkg/artix-keyring-[PRESS TAB]
pacman-key --populate artix
```

# Migrating from Manjaro
{: #migrating-from-manjaro}

People coming from Manjaro OpenRC, will additionally need to run:

```bash
pacman -Rdd manjaro-system
pacman -Rsc manjaro-tools-base \
            manjaro-system \
            mhwd \
            mhwd-db \
            manjaro-firmware \
            manjaro-settings-manager \
            intel-ucode \
            lsb-release \
            rpcbind-openrc
mv /etc/conf.d/rpcbind /etc/conf.d/rpcbindmanj
```

# Setup base
{: #setup-base}
First off, some packages must be removed manually (sysvinit is provided by openrc and consolekit is replaced by elogind).
```bash
pacman -Rdd sysvinit udev-openrc consolekit consolekit-openrc
```
Install the dummy systemd packages
```
pacman -S --asdeps systemd-dummy libsystemd-dummy
```

All your packages from base and base-devel groups must be replaced from the ones in [system]. Artix uses linux-lts as its default kernel, so it must be installed too, along with openrc-system.
```
pacman -Su base base-devel openrc-system grub linux-lts linux-lts-headers
```
Otherwise you must find only the installed ones and replace them.
```
pacman -Qg base base-devel | awk '{print $2}' | sort | uniq > installed
```
Assemble the entire list
```
pacman -Sg base base-devel | awk '{print $2}' | sort | uniq >| groups
```
And compare them against it
```
pacman -S `comm -2 installed groups`
```

All -nosystemd packages must be replaced with their equivalent from the new repos. If pacman refuses to uninstall one due to dependencies remove it with -Rdd and then install its equivalent. Do the same for -elogind, -consolekit and -upower packages.
```
# for p in `pacman -Qq|grep nosystemd`; do pacman -S `sed s/-nosystemd// <<<$p`; done
# for p in `pacman -Qq|grep elogind`; do pacman -S `sed s/-elogind// <<<$p`; done
...
```

Remove more systemd cruft and then run a full system upgrade:

```bash
pacman -Rsdd systemd-sysusers
pacman -Su
pacman -S --needed opensysusers
```

Make sure udev, dbus and elogind services are enabled
<br>
(they should already be, but it won't hurt to re-add them)

```bash
rc-update add udev boot
rc-update del elogind default
rc-update add elogind boot
rc-update add dbus default
```

# Reinstall GRUB
{: #reinstall-grub}

Check if your mkinitcpio.conf has been renamed to .pacsave and recreate your kernel's initramfs with mkinitcpio. I also had to re-install grub in one laptop because it couldn't boot and dropped into a shell. Also check if your grub.cfg is still in place or pacsaved.

```
mkinitcpio -p linux (or whatever kernel you're using)
update-grub
# grub-install /dev/sda (your boot disk or partition, for BIOS systems)
# grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=grub (for UEFI systems)
# grub-install --target=x86_64-efi --efi-directory=esp_mount --bootloader-id=grub (ditto, a user reported success with this one)
```

# Check configuration files
{: #check-config-files}

Go to /etc/conf.d and merge those .pacnew, .pacorig or whatever config files. I use extra/meld for very quick, visual representation and merging of the differences. Check whether your /etc/{passwd,shadow,groups} have been renamed to .pacsaves!
<br>

# Verify /sbin/init
{: #verifiy-sbin-init}

Check if /sbin/init exists! If not, you missed something or hit a bug. Simply re-install openrc:
```
pacman -S openrc
```

# AUR Packages
{: #aur-packages}

Find packages not in repos (i.e. from the AUR). Some might need updating/rebuilding or removal, if they're show-stoppers (unlikely):
```
pacman -Qm
```

# Graphics drivers
{: #graphics-drivers}
This mostly concerns closed source binary drivers like NVidia's. You can either use nvidia-lts with our linux-lts or the nvidia-dkms package which builds the module for all installed kernels.
<br>

# Reboot
{: #reboot}

Your normal menu or command line reboot action won't be present or work, just sync and umount your partitions manually, remount / read-only, hit the power button and profit!
<br><br>

> Use some common sense when executing these instructions. I repeat, the project is still beta, do expect minor problems. Then, report glitches, errors, success stories to the <a href="http://systemd-free.org/comments.php">systemd-free.org's comments section</a>.
{: .is-danger}
