# rpi.pi-desktop-manager

Setup for element14 Pi Desktop box


### Setup for pi-desktop-manager (manage on/off power with rpi GPIO)

    sudo cp usr/local/bin/pi-desktop-manager /usr/local/bin/
    sudo cp etc/systemd/system/pi-desktop-manager.service /etc/systemd/system/
    sudo systemctl enable pi-desktop-manager.service
    sudo systemctl start pi-desktop-manager.service


### Setup for PCF8563 RTC (hardware clock)

Adding device tree overlay :

    cat <<EOF | sudo tee -a /boot/config.txt
    # Enable Pi Desktop RTC
    dtoverlay=i2c-rtc,pcf8563
    EOF

    # reboot
    sudo reboot

Run `sudo nano /lib/udev/hwclock-set` and comment out these three lines:

    #if [ -e /run/systemd/system ] ; then
    # exit 0
    #fi

Disable the old fake hardware clock :

    sudo systemctl disable fake-hwclock.service


Init hardware clock :

    # write time to RTC (system -> RTC)
    sudo hwclock -w
    # read time from RTC
    sudo hwclock -r


### Check PCF8563 RTC (should be in usage at address i2c 0x51)

    pi@raspberrypi:~ $ sudo i2cdetect -y 1
         0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
    00:          -- -- -- -- -- -- -- -- -- -- -- -- --
    10: -- -- -- -- -- -- -- -- 18 -- -- -- -- -- -- --
    20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
    30: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
    40: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
    50: -- UU -- -- -- -- -- -- -- -- -- -- -- -- -- --
    60: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
    70: -- -- -- -- -- -- -- --
