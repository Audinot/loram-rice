---
title: Networking
layout: default
---

## Chapter 2 ~ Network connection
---

The "W" in "Raspberry Pi Zero 2 W" stands for Wi-fi internet. Although 
it's easy to start using `wifi-menu`, a better solution is to let 
the `wpa_supplicant` handle it at startup. At this point we're still 
working as root.

First, let's check for the name of the wireless card:

```
ip link

# Example output:
1: lo: <LOOPBACK, UP, LOWER_UP> ... 

2: wlan0 <BROADCAST,MULTICAST,UP,LOWER_UP> ...
```

The wireless card on a Raspberry Pi should have a name like `wlan0`, 
or a name that start with 'w'.

Next, we'll give systemd information for the wi-fi card.

```
# nano /etc/systemd/network/wlan0.network

[Match]
Name=wlan0

[Network]
Description=On-board wireless NIC
DHCP=yes
```

Restart the network, which won't start wi-fi but will engage the wi-fi
card.

`systemctl restart systemd-networkd`

Now to connect to your internet! You will need your SSID (the name 
of your wi-fi) and of course your wi-fi password. Again, delete any <> 
brackets and substitute your own SSID and password.

```
wpa_passphrase <YOUR SSID> <YOUR PASSWORD> >> /etc/wpa_supplicant/wpa_supplicant-wlan0.conf
```

Use nano to check on the new wpa_supplicant-wlan0.conf file, and add 
a line to the top.

```
# nano /etc/wpa_supplicant/wpa_supplicant-wlan0.conf

# Add this line:
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=wheel

# The rest should already exist below, with a random psk number.
# Don't change this part!
network={
    ssid="<YOUR SSID>"
    #psk="<YOUR PASSWORD>"
    psk=59e0d07fa4c7741797a4r394f38a5c321e3bed51d54ad5fcbd3f84bc7415d73d
    }
```

Finally, start up the wi-fi and make sure it starts every time you boot:

```
systemctl start wpa_supplicant@wlan0.service
systemctl enable wpa_supplicant@wlan0.service
```

You can do a quick check to see if it's going well:

```
# Check the logs

journalctl -f

# Check the IP address

ip addr show wlan0
```

At this point, you should have working internet. You can reboot and 
check to make sure it starts automatically.

```
reboot

# After everything restarts and you log in again...

ping www.archlinuxarm.org -c 3

# Sample output if your internet is connected:

PING www.archlinuxarm.org(archlinuxarm.org (2600:3c02::f03c:91ff:feae:a51f)) 56 data bytes
64 bytes from archlinuxarm.org (2600:3c02::f03c:91ff:feae:a51f) : icmp_seq=1 ttl=49 time=186 ms
64 bytes from archlinuxarm.org (2600:3c02::f03c:91ff:feae:a51f) : icmp_seq=2 ttl=49 time=82.5 ms
64 bytes from archlinuxarm.org (2600:3c02::f03c:91ff:feae:a51f) : icmp_seq=3 ttl=49 time=82.6 ms

--- www.archlinuxarm.org ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2003ms
rtt min/avg/max/mdev = 82.509/117.152/186.313/48.904 ms
```

With Arch installed and the internet connected, it's time to start 
downloading and installing all the important packages and tools. Let's
go!

---

[Chapter 3 ~ Installing Sway and basic tools](./sway.md)







