# LORAM Rice
---
*Custom Arch+Sway, designed for the RPi Zero 2W with under 512mb RAM*

The challenge I set for myself was to build a useable desktop on a 
Raspberry Pi Zero 2w, as well as a satisfying rice. The keyword is 
that it must be *useable.* Unsatisfying performance was unacceptable. 
With only 512mb of RAM, only 418 of which show up as useable on htop, 
and no way to upgrade it, this means the installation and rice must be
lean. Here is a guide to getting your own $15 riced and ready for work!

Some assumptions: this guide is about building a desktop for work. Therefore
it's assumed you aren't running a headless RPi with SSH. I also chose to 
manually install Arch. Although you can install Manjaro easily using the 
Raspberry Pi Imager and end up with something similar, the spirit of the 
LORAM rice guide is to use as little space and resources as possible, while 
Manjaro does not have the same focus.

### You will need:

- [x] Any Raspberry Pi, but this guide assumes you are using RPi02w.
- [x] A monitor. Although you can run a headless RPi, this guide is about desktops!
- [x] A keyboard.
- [x] Cables: HDMI, keyboard, power, and miniUSB-to-USB if required.

### You will not need:

- [ ] A mouse. It's optional and this guide assumes you don't use one.

## Part I - Install Arch Linux ARM

To start, [follow the Wiki guide for AArch64 installation.](https://archlinuxarm.org/platforms/armv8/broadcom/raspberry-pi-zero-2)

I won't copy it out here. Follow the guide exactly. When you get to the `wget` 
command, use the Aarch64 link and instructions (at the bottom of the page). At 
the end of the install, **DO NOT immediately insert the SD card into the RPi.** 
Instead, hang on to it. Don't worry, we'll get to the RPi. For now, don't start 
your Raspberry Pi yet.

### Chrooting

As of today's date (February 28, 2022) there is a problem with the RPi Zero 2 
boot and Arch. You can fix this by replacing the generic vanilla Arch kernel 
with the Raspberry Pi kernel.

If you do not have another Arch ARM device or Raspberry Pi, you will need a 
spare USB key and an iso imager, like Rufus on Windows.

[Get a liveUSB copy of Linux Mint here.](https://linuxmint.com) You can use any 
live environment, but I have found Mint is an excellent "rescue" environment 
for command line installs or fixing broken installs.

Flash your spare USB with your chosen live environment and boot your current 
host computer into the USB. Assuming you're using Mint, you now need to connect 
to the internet and install a few tools:

```
apt install arch-install-scripts qemu-user-binfmt qemu-user-static
```

Make sure ARM executable support is active:

```
ls /proc/sys/fs/binfmt_misc

# The list you get from this ls should include all of the following:

qemu-aarch64		qemu-alpha
qemu-arm		qemu-armeb
qemu-cris		qemu-m68k
qemu-microblaze		qemu-mips
qemu-mipsel		qemu-ppc
qemu-ppc64		qemu-ppc64abi32
qemu-riscv64		qemu-s390x
qemu-sh4		qemu-sh4eb
qemu-sparc		qemu-sparc32plus
qemu-sparc64		register
status
```

Plug your SD card in and mount it to a new mount point. Take a deep breath and 
chroot into your new system.

```
mkdir -p /mnt/sdcard

# Assuming your SD card is at /dev/sdX...

mount /dev/sdX2 /mnt/sdcard
mount /dev/sdX1 /mnt/sdcard/boot

arch-chroot /mnt/sdcard /bin/bash
```

The above steps assumed you're on an x86_64 computer. QEMU is used as an ARM 
emulator to allow the chroot. If you had any trouble, look up QEMU on the 
[official Arch wiki.](https://wiki.archlinux.org/title/QEMU)

If your chroot was successful, you can now complete your Arch install.

```
pacman-key --init
pacman-key --populate archlinuxarm
pacman -Syu
```

At this point, you need two more things! The RPi kernel, and the associated 
firmware.

```
pacman -S linux-rpi
# This will ask to replace AArch64, which is in conflict.
# Say 'yes' or 'y' to everything.

pacman -S raspberrypi-firmware

# Great! Leave the chroot and eject the SD card.

exit
umount /mnt/sdcard/boot
umount /mnt/sdcard
```

Deep breath. Remove the SD card, place it in the Raspberry Pi, plug it in, and 
cross fingers. If all goes well, you should be greeted with a login prompt. The 
default username is `alarm` and the default password is `alarm` as well. The 
root password is `root` by default.

### Cleaning up

Let's start by changing our default user and our default root password.

```
# Log in as root to start.
# The <> brackets don't exist in the actual commands.
# So if you wanted to set your username to "Bob" for example, you would use
# $ usermod -l Bob alarm

usermod -l <username> alarm
passwd <new username>
usermod -d /home/<username> -m <username>

# Also change the default root password to something stronger.

passwd

# Don't forget to give your user sudo!

pacman -S sudo

visudo
```

Under the visudo config file, add your user under the user privilege area.

```
##
## User privilege specification
##
root ALL=(ALL) ALL
<username> ALL=(ALL) ALL
```

OR, add yourself to the Wheel group - however you like to do it.

Now set up your default hostname (the name of your computer).

```
hostnamectl set-hostname <hostname>
```

Now, following the [official Arch wiki install guide,](https://wiki.archlinux.org/title/installation_guide) we have to set a time zone and a locale.

```
# You can ls /usr/share/zoneinfo to find your region.
# ls /usr/share/zoneinfo/REGION will help you find your city.
# For example, /usr/share/zoneinfo/Canada/Vancouver

ln -sf /usr/share/zoneinfo/<Region>/<City> /etc/localtime

nano /etc/locale.gen

# Uncomment any required locales. For US English, uncomment
# en_US ISO-8859-1
# en_US.UTF-8 UTF-8

locale-gen
nano /etc/locale.conf

# Add the following line to locale.conf, assuming US English:

LANG=en_US.UTF-8
```

Done! Reboot your Pi into your new Arch install. At this point, you should have 
a working login, and a shell prompt with your username and hostname. You will 
also see a lot of audit messages telling you things like "cam dummy 
disconnected." This is completely normal and means your Arch install is working 
as intended, but it can be distracting.

To hide most of the distracting audit messages, login as your user and:

```
sudo systemctl mask systemd-journald-audit.socket
```

Reboot and log in again, and it will be a lot better!

## Next steps

This guide is incomplete. More to come:

- [ ] Setting up wi-fi
- [ ] Installing Sway
- [ ] Basic setup: using Sway with Alacritty and removing any need for the mouse
- [ ] Using ZRAM for a boost
- [ ] Customizing the looks


