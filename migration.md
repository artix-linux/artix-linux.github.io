---
layout: migration
title: Migrate
permalink: /migrate/
sub_page: true
---


# A word of caution
{: .has-text-danger } 

Existing OpenRC systems (whether arch-openrc or manjaro-openrc) can be converted to the new scheme with minimal effort. Older eudev-openrc ones might need some more tweaking, especially with concern to the desktop: consolekit2 is replaced by elogind. In any case, the procedure outlined below is meant for OpenRC systems only. Those with vanilla Arch or Manjaro must first <a href="http://systemd-free.org/migrate.php">migrate as described on systemd-free.org</a>.

# Summary
{: .has-text-danger }

In brief, the new repos must be placed before the official Arch or Manjaro ones. Then, sysvinit (provided now by openrc itself) and systemd-sysusers must be removed, as well as consolekit which is replaced by elogind. Next, the base and base devel group must be installed from the new repos and a full-system upgrade must be run. All -nosystemd and consolekit packages have to be replaced by their equivalent too. Finally udev, dbus and elogind services must be enabled and mkinitcpio has to be run for your kernel.

The procedure is roughly the following, it widely depends on individual setups and it's still in beta. <font color="#F99">PROCEED WITH CAUTION!</font> You must be fairly confortable with Linux and keep an extra kernel in /boot and a bootable medium nearby. <a href="https://sourceforge.net/projects/artix-linux/files/iso/lxqt/">Fresh install ISOs</a> <strike>will be available soon</strike> are already available!
<br><br>
<center><font color="#6F6">The guide is work-in-progress, updated with each report we receive. If something needs correction for your setup, report it!</font></center>

# 1. Setup Repositories
Put these repos in /etc/pacman.conf *before* the official Arch/Manjaro ones and disable [core] of the latter:

<code># Artix repos
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
</code>

The [multilib] repo will eventually be available too.
<u><i>The [arch-openrc] and [arch-nosystemd] repos (or [openrc-eudev] if you're still on it) must be disabled.</i></u>

# Troubleshooting

## Revert to systemd
{: #revert}
If, perchance, you're not satisfied with the result you can always roll-back to systemd by executing the above two pacman commands inverted. 

```bash
pacman -Rs sysvinit  \ 
    openrc  \ 
    eudev  \ 
    udev-openrc  \ 
    eudev-systemd  \ 
    dbus-openrc  \ 
    procps-ng-nosystemd  \ 
    syslog-ng-nosystemd  \ 
    udisks2-nosystemd  \ 
    consolekit  \ 
    polkit-consolekit  \ 
    upower-pm-utils  \ 
    udisks2-nosystemd  \ 
    desktop-privileges  \ 
    xorg-xwrapper  \ 
    acpid-openrc  \ 
    alsa-utils-openrc  \ 
    autofs-openrc  \ 
    consolekit  \ 
    consolekit-openrc  \ 
    cronie-openrc  \ 
    dbus-openrc  \ 
    cups-openrc  \ 
    displaymanager-openrc  \ 
    fuse-openrc  \ 
    haveged-openrc  \ 
    hdparm-openrc  \ 
    openssh-openrc  \ 
    samba-openrc  \ 
    syslog-ng-openrc  \ 
    avahi-openrc
pacman -S systemd libsystemd systemd-sysvcompat
```
