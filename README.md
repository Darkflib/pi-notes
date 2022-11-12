# Notes about the pi (primarily pi4, but probably applicable to any)

## build-in / on-board LEDs

* led0 - Green
* led1 - Red

```
echo none > /sys/class/leds/led0/trigger    # Disable SD card triggering (mmc0)
echo 255 > /sys/class/leds/led0/brightness  # Turn On (as it doesn't support variable brightness, anything other than 0 works)
echo 0 > /sys/class/leds/led0/brightness    # Turn Off
```

### Or in python
```python
#!/usr/bin/python

import RPi.GPIO as GPIO
from time import sleep

# Needs to be BCM. GPIO.BOARD lets you address GPIO ports by periperal
# connector pin number, and the LED GPIO isn't on the connector
GPIO.setmode(GPIO.BCM)

# set up GPIO output channel
GPIO.setup(16, GPIO.OUT)
# On
GPIO.output(16, GPIO.LOW)

# Wait a bit
sleep(10)

# Off
GPIO.output(16, GPIO.HIGH)
```
It's important to note that RPi.GPIO will set the GPIO port to be an input when it's finished (it sets to an input any port which the script set to an output, and you have to set it to an output before you can do anything with it, even if it already was). As GPIO16 stops being an output, control via the kernel interface will no longer work.


## Add a new user

```
adduser mike
usermod -a -G sudo mike     # Required for sudo access
usermod -a -G video mike    # Required for vcgencmd (for temps etc)
```

## Give user passwordless sudo
```
cd /etc/sudoers.d
cp 010_pi-nopasswd 010_mike-nopasswd
sed -i 's/pi/mike/' 010_mike-nopasswd 
```

## LIRCD Alts

* http://manpages.ubuntu.com/manpages/bionic/man1/ir-keytable.1.html
* http://manpages.ubuntu.com/manpages/bionic/man1/ir-ctl.1.html

## Argon One stuff

* https://github.com/Argon40Tech/Argon-ONE-i2c-Codes/blob/master/README.md - i2c fan control
* https://gitlab.com/DarkElvenAngel/argononed - alt daemon

## Power Consumption

| Product |	Recommended PSU current capacity | Maximum total USB peripheral current draw | Typical bare-board active current consumption |
| ------- | -------------------------------- | ----------------------------------------- | --------------------------------------------- |
| Raspberry Pi Model A |	700mA |	500mA |	200mA |
| Raspberry Pi Model B |	1.2A |	500mA |	500mA |
| Raspberry Pi Model A+ |	700mA |	500mA |	180mA |
| Raspberry Pi Model B+ |	1.8A |	1.2A |	330mA |
| Raspberry Pi 2 Model B |	1.8A |	1.2A |	350mA |
| Raspberry Pi 3 Model B |	2.5A |	1.2A |	400mA |
| Raspberry Pi 3 Model A+ |	2.5A |	Limited by PSU, board, and connector ratings only. |	350mA |
| Raspberry Pi 3 Model B+ |	2.5A |	1.2A |	500mA |
| Raspberry Pi 4 Model B |	3.0A |	1.2A |	600mA |
| Raspberry Pi Zero |	1.2A |	Limited by PSU, board, and connector ratings only |	100mA |
| Raspberry Pi Zero W/WH |	1.2A |	Limited by PSU, board, and connector ratings only. |	150mA |

From https://www.raspberrypi.org/documentation/hardware/raspberrypi/power/README.md

All models require a 5.1V supply

Absolute Range: 4.63V (+/- 5%) to 5.25V


## Images

* https://downloads.raspberrypi.org/raspios_lite_arm64/images/ - 64bit lite
* https://downloads.raspberrypi.org/raspios_arm64/ -- 64bit 


## Frequency scaling

The default CPU governor is `ondemand`, the governor can be manually changed with the `cpufreq-set` command (from the cpufrequtils package) to reduce idle power consumption:

```
sudo apt install cpufrequtils
sudo cpufreq-set -g powersave
```
## Governor types

| Governor | Description |
| -------- | ----------- |
| performance | |
| powersave | |
| userspace | |
| ondemand | |
| conservative | |
| schedutil | |

* https://www.kernel.org/doc/Documentation/cpu-freq/governors.txt

## Setting the default governor

Create a file `/etc/default/cpufrequtils` with these contents

```
GOVERNOR="performance"
```


## Setting the governor per core

```
> cd /sys/devices/system/cpu
> cat cpu0/cpufreq/scaling_available_governors
conservative ondemand userspace powersave performance
```

To check:

```
> cat cpu0/cpufreq/scaling_governor
ondemand
```

To change it:

`echo performance > cpu0/cpufreq/scaling_governor`

or

`sudo sh -c "echo performance > cpu0/cpufreq/scaling_governor"`




## Temp

```
vcgencmd measure_temp
```


## Clock

`measure_clock [clock]`

This returns the current frequency of the specified clock. The options are:

| clock |	Description |
| ----- | ----------- |
| arm |	ARM core(s) |
| core |	GPU core |
| H264 |	H.264 block |
| isp |	Image Sensor Pipeline |
| v3d |	3D block |
| uart |	UART |
| pwm |	PWM block (analogue audio output) |
| emmc |	SD card interface |
| pixel |	Pixel valves |
| vec |	Analogue video encoder |
| hdmi |	HDMI |
| dpi |	Display Parallel Interface |

## Bootloader

* https://www.raspberrypi.org/documentation/hardware/raspberrypi/booteeprom.md

```
vcgencmd bootloader_version
```

```
sudo rpi-eeprom-update  # check if update available
sudo rpi-eeprom-update -a
sudo reboot
```


## Swap (dphy-swapfile)

```
> dphys-swapfile swapoff
> dphys-swapfile uninstall
> systemctl disable dphys-swapfile.service 
Synchronizing state of dphys-swapfile.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install disable dphys-swapfile
Removed /etc/systemd/system/multi-user.target.wants/dphys-swapfile.service.
```

## Timezone

```
date
timedatectl set-timezone UTC
date
```

## Lock pi user

```
usermod -L pi
```


## Overclock

```
arm_boost=1
```

```
#Overclock
arm_freq=2300
gpu_freq=500
over_voltage=14
force_turbo=1
```

```
arm_freq=2275
gpu_freq=750
over_voltage=8
```

```
over_voltage=6
arm_freq=2000
```

```
over_voltage=6
arm_freq=2000
gpu_freq=750
```
