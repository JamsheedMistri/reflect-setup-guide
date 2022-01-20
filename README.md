# Reflect Setup Guide
This is a guide for setting up [Reflect](https://github.com/JamsheedMistri). I personally use this guide for my own magic mirror because the Raspberry Pi SD card gets corrupted every once and a while, and I end up needing to reinstall the entire OS.

## Steps
1) Install Raspbian 10 (Buster) using [Raspberry Pi Imager](https://www.raspberrypi.com/software/). Note: you must use Buster because Mopidy only supports Buster at the moment.

2) Change the local domain for easier access.

First change the hostname; change from `raspberrypi` to `mirror` in `/etc/hosts` and `/etc/hostname`.

Then, install `avahi-daemon`, commit the changes, and reboot:

```
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install avahi-daemon
sudo /etc/init.d/hostname.sh
sudo reboot
```

3) Enable SSH & VNC. Do `sudo raspi-config`, go to `Interface Options`, and enable both.

Now, you should be able to unplug the external keyboard/mouse and SSH/VNC from your computer.

4) Rotate the screen 90° by adding these to `/boot/config.txt`:

```
display_rotate=1
display_hdmi_rotate=1
lcd_rotate=1
```

5) Install Apache and PHP:

```
sudo apt install -y apache2 php php-common php-curl
sudo phpenmod curl
sudo service apache2 restart
```

6) Configure git globally (optional — I do this in case I need to commit hotfixes from my RPI)

```
sudo git config --global credential.helper store
```

7) Install Reflect into `/var/www/`:

```
sudo rm -Rf /var/www/html/
sudo git clone <reflect repo URL> /var/www/html/
```

8) Change directory permissions, owner, and group on `/var/www/` so `www-data` can read/write/execute.

9) Add `www-data` as sudoer with no password (`sudo visudo`):

```
www-data     ALL=(ALL) NOPASSWD:ALL
```

10) Install Mopidy (and dependencies), Mopidy-Spotify, Mopidy-MPD, Mopidy-ALSA, Mopidy-Iris.

11) Install MPD:

```
sudo apt-get install -y mpc
```

12) Configure Mopidy in `/etc/mopidy/mopidy.conf`:

```
# For information about configuration values that can be set in this file see:
#
#   https://docs.mopidy.com/en/latest/config/
#
# Run `sudo mopidyctl config` to see the current effective config, based on
# both defaults and this configuration file.

[audio]
mixer = alsamixer
output = alsasink

[alsamixer]
card = 0
control = HDMI

[http]
enabled = true
hostname = 0.0.0.0
port = 6680

[spotify]
username = # username
password = # password
client_id = # client ID from https://mopidy.com/ext/spotify/
client_secret = # client secret from https://mopidy.com/ext/spotify/
```

13) Install Chromium browser command line tool and cursor hider:

```
sudo apt-get install chromium-browser unclutter
```

14) Run both of the above on startup (`/etc/xdg/lxsession/LXDE-pi/autostart`):

```
@chromium-browser --kiosk http://localhost/
@unclutter -idle 0
```