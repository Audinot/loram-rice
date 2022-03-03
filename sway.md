---
title: Sway and Tools 
layout: default
---

## Sway and basic tools 

---

With internet connected, you are ready to start the rice journey. 
The first step is to set up the most basic part: installing the 
tools required for the bare minimum work of displaying anything at all! 

The theme of Loram is minimalism. It's designed for a Pi Zero Two, so 
we will use a window manager instead of a full desktop environment. 
It is also designed not to need a mouse, so let's use a keyboard-focused
manager. I have chosen Sway for its light RAM footprint and 
straightforward default keyboard mapping.

At this point you can log in as your user. We will be installing:

- Sway: the base of the rice.
- Swaylock and Swayidle: optional but nice to have.
- Dmenu: the default application launcher for Sway.
- Foot: the default terminal for Sway.
- Alacritty: OPTIONAL, a faster terminal for the RPi02.

There is nothing wrong with Foot, but Alacritty *feels nicer* on a Pi. 
Ricing is all about the little things, right?

So to get started...

`sudo pacman -S sway swaylock swayidle dmenu foot alacritty`

Now for the big moment!

`sway`

Welcome to your new desktop.

While you could start Sway from a display manager, this is not yet 
officially supported. To be safe, we will tell Sway to start after 
TTY login.

Open a terminal with `$MOD+Return`, which is the Windows key + Return.

Add the following to the bottom of the .bashrc file.

```bash
# Start Sway after logging in

if [ -z $DISPLAY ] && [ "$(tty)" = "/dev/tty1" ]; then
    exec sway
fi
```

Our next goal is to download more RAM.

...Just kidding. However, it would be good to use the RAM we have more 
efficiently. We can do this with ZRAM, a necessity to give a small 
board like the Pi Zero more headroom!

To install ZRAM, we will use the Novaspirit script. This is optional 
if you don't have a Raspberry Pi, or if you have the 8GB Pi 4 model.

```bash
sudo pacman -S git

# Let's add a bin folder to your $HOME if you don't have one already.

mkdir bin
cd bin

# Get the script!

git clone https://github.com/novascript/rpi_zram.git
cd rpi_zram

# Copy the zram script to /usr/bin and make it executable.

sudo cp zram.sh /usr/bin
sudo chmod +x /usr/bin/zram.sh

# Now we add it as a service to Systemd.

sudo tee /etc/systemd/zram.service <<-'EOF'
[Unit]
Description=zram Service
;After=network-online.target
;Wants=network-online.target systemd-networkd-wait-online.service

[Service]
Type=simple
ExecStart=/usr/bin/zram.sh

[Install]
WantedBy=multi-user.target
EOF

# Now we start it!

sudo systemctl daemon-reload
sudo systemctl enable zram.service
```

After zram enables and runs, it dynamically figures out the best use 
of swap. Using `free -m` will show you swap space even though we never 
installed a swap partition. Neat!

Finally, we want to be able to close Sway alerts (called "nags") 
without using a mouse to click them. For this we will use an AUR 
package, so let's install an AUR helper as well.

```bash
# Start with the helper. Yay is easy to use.

sudo pacman -S yay

# ...Yay! Now we can get the package. Yay does not use sudo, but 
# will ask for your password.

yay -S swaynagmode
```

Now to edit the Sway config to use our basic tools so far. You can 
edit it with `cd` to your home directory, and then `nano 
.config/sway/config`.

```bash
# At the very top of the config, add

exec zram

# Not too far down, you can comment out and replace the terminal 
# with Alacritty. For Raspberry Pi you need to let it know to use 
# OpenGL rendering.

# set $term foot
set $term LIBGL_ALWAYS_SOFTWARE=1 alacritty

# A little further down, we will add key bindings for swaynagmode 
# just under the "preferred application launcher" lines for dmenu.

# ...
# on the original workspace that the command was run on.
set $menu dmenu_path | dmenu | xargs swaymsg exec --

# NEW nags with keyboard bindings

set {
    $nag		exec swaynagmode
    $nag_exit		$nag --exit
    $nag_confirm	$nag --confirm
    $nag_select		$nag --select
}
```

If you installed Swaylock and Swayidle, there is a nice example config 
you can uncomment here, with the title `### Idle configuration`.

```bash
# About halfway down, we use the nag variables we just set to replace 
# the current bindsym $mod+Shift+e line.

# ...
# Exit sway (logs you out of your Wayland session)

# Exit nag with keyboard bindings instead of mouse

    mode "nag" {
        bindsym {
            Ctrl+d	mode "default"

            Ctrl+c	$nag_exit
            q		$nag_exit
            Escape	$nag_exit

            Return	$nag_confirm

            Tab		$nag_select prev
            Shift+Tab	$nag_select next

            Left	$nag_select next
            Right	$nag_select prev

            Up		$nag_select next
            Down	$nag_select prev
        }
    }
    bindsym {
        $mod+Shift+e $nag -t "warning" -m "Exit Sway?" -b "Exit" "swaymsg exit" -b "Reload" "swaymsg reload"
    }
    # -R is recommended for swaynag to emergency reload, in case of typo
    swaynag_command $nag -R
```

Whew! Finally, for a start on some nicer looks, turn off the mouse 
after 3 seconds of inactivity and add some gaps around windows.

```bash
# Append to the bottom of Sway config.

# Hide mouse after 3 seconds of inactivity
seat seat0 hide_cursor 3000

# Gaps around windows, set however many pixels you like 
gaps inner 15
```

Done! Save and quit config, and reload your Sway session 
`($mod+Shift+c)`.

For the full test, restart your pi/computer and make sure everything 
is enabled once you log in: Sway, ZRAM, your preferred terminal, gaps, 
Swaylock/idle, nags (you can test this with `$mod+Shift+e`), network, 
and dmenu (`$mod+d). If it's all what you expect, congratulations! You 
have installed a complete Arch Linux OS and Sway on a Raspberry Pi Zero 
2 W... Or similar lo-spec computer. Our next goal is to rice it.


