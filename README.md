# Gameboy Zero

![Raspberry Pinout](https://raw.githubusercontent.com/mrdink/gameboy-zero-setup/master/img/gameboy-zero.png "Gameboy Zero")

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

First, connect to the pi using ssh and run the following:

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

Thanks to [prerunnerseth](http://www.sudomod.com/forum/viewtopic.php?t=57) for explaining how to set this up.

![Raspberry Pinout](https://raw.githubusercontent.com/mrdink/gameboy-zero-setup/master/img/gpio-pinout-vet.png "Raspberry Pinout")

First, create the following directory in pi user home directory.

```bash
cd ~
mkdir Adafruit-Retrogame
cd Adafruit-Retrogame
```

Next, download `retrogame.c`

```bash
wget https://raw.githubusercontent.com/mrdink/gameboy-zero-setup/master/retrogame.c
```

Next, run the following to make the executable retrogame utility.

```bash
make retrogame
```

Next, create a custom udev rule which will allow retrogame input events to be visible to applications built using the latest SDL2 library.

```bash
sudo nano /etc/udev/rules.d/10-retrogame.rules
```

Copy the following into the console:

```bash
SUBSYSTEM=="input", ATTRS{name}=="retrogame", ENV{ID_INPUT_KEYBOARD}="1"
```

Save with Control+O, Enter, Control+X.

Reboot to make sure the configuration has saved. `sudo reboot now`

Now, let's run the utility.

```bash
cd Adafruit-Retrogame
sudo chmod +x retrogame
sudo ./retrogame
```

If this seems to be working, cancel the process with Control+C.

Next, setup the system to automatically load this process at startup.

```bash
sudo nano /etc/rc.local
```

Add the following before `exit 0` at the bottom of the file.

```
/home/pi/Adafruit-Retrogame/retrogame &
```

Finally, reboot and test to make sure buttons are working as expected.

## Add custom splash screen

[AJRedfern](http://www.sudomod.com/forum/viewtopic.php?f=42&t=1440) did an awesome job creating a splashscreen. I've included his latest (43v2-1) in this repo but be sure to check his thread for any updates.

First, connect to the pi and download the mp4 to the splashscreens directory/

```bash
cd RetroPie/splashscreens
wget https://github.com/mrdink/gameboy-zero-setup/raw/master/splashscreens/GBZ-Splash-Screen.mp4
```

Next, load the Retropie Setup script.

```bash
sudo bash ~/RetroPie-Setup/retropie_setup.sh
```

Next, go to:

* Configurations / tools
* splashscreen
* Choose splashscreen
* Own/Extra splashscreens (from /home/pi/RetroPie/splashscreens)

Finally, select GBZ-Splash-Screen.mp4 and perform a reboot.

## Add Gameboy optimized theme for RetroPie

[RxBrad](http://sudomod.com/forum/viewtopic.php?t=1549) has the best theme for a smaller screen in my opinion.

Installation is simple. Run the RetroPie Setup script again.

```bash
sudo ~/RetroPie-Setup/retropie_setup.sh
```

Next, go to:

* Configurations / tools
* esthemes

Next, scroll down to find "rxbrad/gbz35" and "rxbrad/gbz35-dark" hit Enter to install. Now the theme should show up within RetroPie UI Settings.

## [Steven Selph's Scraper](https://github.com/retropie/retropie-setup/wiki/scraper#steven-selphs-scraper)

Steven Selph's scraper is the simplest and best way of scraping roms (provided that the systems are supported.) It can be installed and used from the setup menu using the following steps:

1. (Optional)If you are remotely running this script you must be logged in with pi otherwise it will confuse the scraper
1. Quit EmulationStation (from the start menu or press F4) and type `sudo ~/RetroPie-Setup/retropie_setup.sh`
1. In 3.x Choose 'Setup'. In 4.x Choose 'Manage Packages' then 'Optional Packages'.
1. Select Scraper
1. Wait for the installation to complete (it may take some time as it has to install various software libraries)

## PWM Audio

Add the following to `/boot/config.text`

```
dtoverlay=pwm-2chan,pin=18,func=2,pin2=13,func2=4
disable_audio_dither=1
```

## PRS Cartridge Reader

Test pins:

* Blue - 15
* Yellow - 19
* Purple - 18
* Orange - 14
* White - 16
* Red - 17
* Green - 3.3V
* Brown/Tan - Ground