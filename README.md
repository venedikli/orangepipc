# orangepipc
orangepi pc 3.5 tft screen dts file 

If you decide to use orangepi with armbian on 3.5" tft screen (driver ili9486) you have to compile and use this file.

How to use ? 

sudo nano /boot/armbianEnv.txt. //Add the following lines to the end of the file 

overlays=spi-spidev spi-add-cs1
param_spidev_spi_bus=0
param_spidev_spi_cs=1

save and quit

sudo nano /etc/modules-load.d/98-fbtft.conf. // insert the following line  
fbtft
fbtft_device

save and quit

sudo nano /etc/modprobe.d/fbtft.conf // Add the following
options fbtft_device rotate=90 name=piscreen speed=16000000 gpios=reset:2,dc:71 txbuflen=32768 fps=25

save and quit

sudo apt-get install xserver-xorg-video-fbdev
sudo nano /usr/share/X11/xorg.conf.d/99-fbdev.conf. // Insert this in the file:

Section "Device"  
  Identifier "piscreen"
  Driver "fbdev"
  Option "fbdev" "/dev/fb0"
EndSection

save and quit

sudo armbian-add-overlay foo.dts

sudo reboot

Thats it.After all you should calibrate the touch controller. How to calibrate touch controller ? 


sudo apt-get install xinput
nano /etc/modprobe.d/ads7846_device.conf
options ads7846_device model=7846 cs=0 spibus=0 gpio_pendown=1 keep_vref_on=1 swap_xy=0 pressure_max=255 x_plate_ohms=60 x_min=200 x_max=3900 y_min=200 y_max=3900

save and quit

export XAUTHORITY=$(eval echo ~`who | grep tty7 | sed 's/\([a-z]*\).*/\1/'`)/.Xauthority
sudo nano /home/<your username>/.xsessionrc
DISPLAY=:0.0 xinput --set-prop 'ADS7846 Touchscreen' 'Coordinate Transformation Matrix' 0 -1 1 1 0 0 0 0 1

After that your screen must be calibrated.

!!!!!!!!!! DONT FORGET !!!!!!!!!
This setting for kernel v5.5.0>   
