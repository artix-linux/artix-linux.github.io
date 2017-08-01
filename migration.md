---
layout: migration
title: Migrate
permalink: /migrate/
sub_page: true
---


# A word of caution
{: .has-text-danger } 

Do *not* just copy/paste these lines; read carefully and understand what you are about to do. While you won't get an unbootable system if you just copy/paste these, you might end up with reduced or weird functionality, especially in your desktop environment. 

Some decisions are required from your part, according to your setup. Do read the configuration section afterwards. 
If you would rather prefer the previous tabled, side-by-side version of this page, you can still access it here (might be seriously out-of-date). 


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