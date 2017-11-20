# Gameboy Zero

Checklist for building a custom Gameboy Zero.

## Disk setup

* Install [Retropie](https://retropie.org.uk/download/) using [Etcher](https://etcher.io/)
* Add blank ssh file to root of disk
* Add wpa_supplicant.conf with wifi credentials to root of disk

### Example wpa_supplicant.conf

```
country=US
update_config=1
ctrl_interface=/var/run/wpa_supplicant

network={
	ssid="YOUR SSID"
	psk="YOUR PASSWORD"
	scan_ssid=1
}
```

## Configure

First, connect to the pi using ssh and run:

```bash
sudo raspi-config
```

* 1 > Change password
* 2 > Change hostname
* 4 > Setup localization
* 7 > Advanced Options > Expand Filesystem
* Reboot

Next, change the font for the console to be legible for a smaller screen.

```bash
sudo dpkg-reconfigure console-setup
```

Select UTF-8 > Guess optimal character set > Terminus > 10x20 (framebuffer only)

## Setup GPIO for buttons

Thanks to []prerunnerseth](http://www.sudomod.com/forum/viewtopic.php?t=57) for explaining how to set this up.

![Raspberry Pinout][ltd7B84.jpg]

* Up - up
* Down - down
* left - left
* right - right
* start - enter
* select - s
* A - a
* B - b
* X - x
* Y - y
* L - l
* R - r

