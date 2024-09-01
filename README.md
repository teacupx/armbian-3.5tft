# sunxi-tft
Armbian sunxi pc 3.5 tft screen dts file 

If you decide to use orangepi with armbian on 3.5" tft screen (driver ili9486) you have to compile and use this file.

## How to use

### Preliminary steps
> Command:

`git clone https://github.com/teacupx/armbian-3.5tft`

`cd armbian-3.5tft`
### 2. Configure overlays
> Command:

`sudo nano /boot/armbianEnv.txt`
> Add the following lines to the end of the file:
```
overlays=spi-spidev spi-add-cs1
param_spidev_spi_bus=0 # Change to 1 for h5
param_spidev_spi_cs=1
```

> Command:

`sudo armbian-add-overlay overlays/<file>.dts`

Where `<file>` is the most appropriate overlay for your device

### 3. Configure modules
> Command:

`sudo nano /etc/modules-load.d/98-fbtft.conf` 
> Insert the following lines: 
```
fbtft
fbtft_device

```

> Command:

`sudo nano /etc/modprobe.d/fbtft.conf` 
> Add the following:
```
options fbtft_device rotate=90 name=piscreen speed=16000000 gpios=reset:2,dc:71 txbuflen=32768 fps=25

```
> Reboot:

 `sudo reboot` 

### 4. Configure X server
> Command:

`sudo apt-get install xserver-xorg-video-fbdev`

`sudo nano /usr/share/X11/xorg.conf.d/99-fbdev.conf`
> Put this in the file:
```
Section "Device"  
  Identifier "piscreen"
  Driver "fbdev"
  Option "fbdev" "/dev/fb0"
EndSection

```


### 5. Calibrate touch controller

sudo apt-get install xinput<br>
nano /etc/modprobe.d/ads7846_device.conf<br>
options ads7846_device model=7846 cs=0 spibus=0 gpio_pendown=1 keep_vref_on=1 swap_xy=0 pressure_max=255 x_plate_ohms=60 x_min=200 x_max=3900 y_min=200 y_max=3900<br>

save and quit

export XAUTHORITY=$(eval echo ~`who | grep tty7 | sed 's/\([a-z]*\).*/\1/'`)/.Xauthority<br>
sudo nano /home/<your username>/.xsessionrc<br>
DISPLAY=:0.0 xinput --set-prop 'ADS7846 Touchscreen' 'Coordinate Transformation Matrix' 0 -1 1 1 0 0 0 0 1<br>

After that your screen must be calibrated.
