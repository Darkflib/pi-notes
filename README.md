# Notes about the pi (primarily pi4, but probably applicable to any)

## build-in / on-board LEDs

```
echo none > /sys/class/leds/led0/trigger    # Disable SD card triggering (mmc0)
echo 255 > /sys/class/leds/led0/brightness  # Turn On (as it doesn't support variable brightness, anything other than 0 works)
echo 0 > /sys/class/leds/led0/brightness    # Turn Off
```

### Or in python
```
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
cp 010_pi-nopasswd 010_mike-nopasswd
sed -i 's/pi/mike/' 010_mike-nopasswd 
```

# LIRCD Alts

* http://manpages.ubuntu.com/manpages/bionic/man1/ir-keytable.1.html
* http://manpages.ubuntu.com/manpages/bionic/man1/ir-ctl.1.html

# Argon One stuff

* https://github.com/Argon40Tech/Argon-ONE-i2c-Codes/blob/master/README.md - i2c fan control
* https://gitlab.com/DarkElvenAngel/argononed - alt daemon

