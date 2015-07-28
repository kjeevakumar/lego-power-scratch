# lego-power-scratch
A script that allows controlling Lego power functions from the Scratch programming environment.  Here is how to use it.


## Hardware
* Use [Alex Bain's schematic and instructions](http://alexba.in/blog/2013/03/09/raspberrypi-ir-schematic-for-lirc/)

## LIRC installation and configuration
* Install [LIRC](http://www.lirc.org/)
```
sudo apt-get install lirc
```

* Configure LIRC by editing `/etc/lirc/hardware.conf` to be
```
#Try to load appropriate kernel modules
LOAD_MODULES=true

# Run "lircd --driver=help" for a list of supported drivers.
DRIVER="default"
# usually /dev/lirc0 is the correct setting for systems using udev
DEVICE="/dev/lirc0"
MODULES="lirc_rpi"
```
* Add in `/etc/modules` the following
```
lirc_dev
lirc_rpi gpio_in_pin=23 gpio_out_pin=22
```
* Restart LIRC
```
sudo service lirc restart
```

* Copy PWM command generated by [this modified PWM generation program](https://github.com/dspinellis/lego-lirc) file to the Raspberry Pi's LIRC configuration directory.
```
curl https://raw.githubusercontent.com/dspinellis/lego-lirc/master/Single_Output">PWM command file |
sudo dd of=/etc/lirc/lircd.conf/Single_Output
```
* Test  the sending of Lego commands from the command line.
```
irsend SEND_ONCE LEGO_Single_Output 1B_5
sleep 1
irsend SEND_ONCE LEGO_Single_Output 1B_M1
sleep 1
irsend SEND_ONCE LEGO_Single_Output 1B_BRAKE
```

## Interfacing with Scratch</h2>
* [Enable](http://wiki.scratch.mit.edu/wiki/Remote_Sensor_Connections) remote sensor connections in Scratch.
* Installed Phillip Quiza's [scratchpy library](https://github.com/pilliq/scratchpy)
```
sudo apt-get install python-setuptools
sudo easy_install scratchpy
```
* Use this repo's  Python script that receives Scratch broadcast messages specifying Lego remote commands, and runs the LIRC command-line client to send them.
* Run  the script 
```
python control.py
```
* To use the script from another computer, specify the other computer's address
```
# With IP address
python control.py 192.168.1.12
# With host name
python control.py my-scratch-running-desktop
```

## Programming Scratch</h2>
To send Lego remote control commands from the Scratch environment, use the "broadcast" control message.
Compose a new message of the following form:
```
Lego channel# Blue/Red power
```
where
 * the channel number is an integer from 1 to 4</li>
 * "Blue" or "Red" denotes the corresponding colored side of the receiver block
 * Power is an integer from -7 to 7 or the string "brake"
