---
layout: migration
title: Migrate
permalink: /migrate/
sub_page: true
---


# A word of caution
{: .has-text-danger } 

Existing OpenRC systems (whether arch-openrc or manjaro-openrc) can be converted to the new scheme with minimal effort. Older eudev-openrc ones might need some more tweaking, especially with concern to the desktop: consolekit2 is replaced by elogind. In any case, the procedure outlined below is meant for OpenRC systems only. Those with vanilla Arch or Manjaro must first <a href="http://systemd-free.org/migrate.php">migrate as described on systemd-free.org</a>.



# 1. Setup Repositories

Append the [arch-openrc] and [arch-nosystemd] repositories to /etc/pacman.conf (all commands below need root privileges).

```bash
cat << EOF >> /etc/pacman.conf
[arch-openrc]
SigLevel=Never
Server=http://downloads.sourceforge.net/project/archopenrc/\$repo/\$arch
Server=ftp://ftp.heanet.ie/mirrors/sourceforge/a/ar/archopenrc/\$repo/\$arch
Server=http://archbang.org/archopenrc/\$repo/\$arch

[arch-nosystemd]
SigLevel=Never
Server=http://downloads.sourceforge.net/project/archopenrc/\$repo/\$arch
Server=ftp://ftp.heanet.ie/mirrors/sourceforge/a/ar/archopenrc/\$repo/\$arch
Server=http://archbang.org/archopenrc/\$repo/\$arch
EOF
```


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
