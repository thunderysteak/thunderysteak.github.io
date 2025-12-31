# Getting serial based Wacom Penabled tablet digitizers working on modern Linux

I have this Fujitsu Lifebook tablet that I installed Debian on and I couldn't get the digitizer to work. Turns out this is due to some weird udev rules with the wacom driver and the wacom driver maybe just not supporting serial based devices anymore(?) (Don't quote me on this one.).  

On the Fujitsu tablet, when looking at the ACPI table the tablet is listed under ID FUJ02E5. I have found this out by running `udevadm info -e | grep WAC`.  

## Identifying the digitizer and it's baud rate

Since the digitizer is serial based, we need to first identify which serial interface it's running on and guess the baud rate. You will need the wacom drivers installed beforehand.  

You want to first list the devices as a sanity check to make sure that this is not some completely unrelated issue and that no wacom devices are listed. Run this command and it should come up empty:  

```
xsetwacom list devices
```

Once you see that no wacom device is listed, list all serial devices:  

```
ls /dev | grep ttyS
```   

In my case, I have ttyS0, ttyS1 and ttyS2. I started with ttyS0. We will be utilizing the `isdv4-serial-debugger` tool to identify the serial output at specific baud rates to see if it's actually a wacom digitizer or just a regular serial interface on your machine.  

Run this following command, you might need to try around different ttySx devices or different baud rates, but this following one worked for me:  

```
isdv4-serial-debugger --baudrate 19200 /dev/ttyS0
```

Now grab your pen and hover around the screen, you should get following output blasting thru the terminal that will confirm that you located the wacom digitizer:  

```
TABLET: version: 145
TABLET: x max: 24576 y max 18432
TABLET: tilt_x max: 0 tilt_y max 0
TABLET: pressure max: 255
timeout.
ignoring touch query timeout
PEN	1767199252:10379/ 8326 | pressure:   0 |    0/  0 |p       |
PEN	1767199252:10339/ 8273 | pressure:   0 |    0/  0 |p       |
PEN	1767199252:10305/ 8223 | pressure:   0 |    0/  0 |p       |
PEN	1767199252:10277/ 8176 | pressure:   0 |    0/  0 |p       |
PEN	1767199252:10255/ 8135 | pressure:   0 |    0/  0 |p       |
PEN	1767199252:10237/ 8103 | pressure:   0 |    0/  0 |p       |
PEN	1767199252:10224/ 8078 | pressure:   0 |    0/  0 |p       |
PEN	1767199252:10213/ 8058 | pressure:   0 |    0/  0 |p       |
PEN	1767199252:10207/ 8046 | pressure:   0 |    0/  0 |p       |
PEN	1767199252:10204/ 8037 | pressure:   0 |    0/  0 |p       |
PEN	1767199252:10204/ 8037 | pressure:   0 |    0/  0 |p       |
PEN	1767199252:10204/ 8023 | pressure:   0 |    0/  0 |p       |
```

## Mounting the Wacom digitizer to the system

You will need to install the `inputattach` package. Once that's done, grab your testing results of the baud rate and the ttySx device and punch it into the command as follows:  

```
sudo inputattach --baud 19200 --w8001 /dev/ttyS0
```

Now open a second terminal window and confirm that `xsetwacom` sees it:

```
thunderysteak@mx-lifebook7:~
$ xsetwacom list devices
Wacom Serial Penabled Pen stylus	id: 16	type: STYLUS    
Wacom Serial Penabled Pen eraser	id: 17	type: ERASER 
```

This should mean that the driver is loaded and that the pen should work now with pressure sensitivity. Try launching Krita or GIMP and confirm you got pressure sensitivity. Once done kill the inputattach command with ctrl + c.   

## Mounting the digitizer on boot

Ok so we want to make this persistent across reboots. We will be creating a systemd unit file to daemonize inputattach.  

Create a new file `/etc/systemd/system/wacom-inputattach.service` as root and fill it with this. Make sure to change your baudrate and ttySx device!  

```
[Unit]
Description=Inputattach for Wacom touchscreen
After=ttyS0.device
After=serial-getty@ttyS0.service

[Service]
Type=oneshot
ExecStart=/usr/bin/inputattach --daemon --baud 19200 --w8001 /dev/ttyS0
User=root
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

Save it and then hit it with `sudo systemctl daemon-reload` to reload all unit files. You want to enable the unit file to run on startup and then start the service:

```
sudo systemctl enable wacom-inputattach
sudo systemctl start wacom-inputattach
```

Check the status and hover your pen around, it should be working now:
```
thunderysteak@mx-lifebook7:~
$ systemctl status wacom-inputattach
● wacom-inputattach.service - Inputattach for Wacom touchscreen
     Loaded: loaded (/etc/systemd/system/wacom-inputattach.service; disabled; preset: enabled)
     Active: active (exited) since Wed 2025-12-31 17:26:41 GMT; 4s ago
 Invocation: 35a5fb17795945e59fea019840e82bb4
    Process: 22652 ExecStart=/usr/bin/inputattach --daemon --baud 19200 --w8001 /dev/ttyS0 (code=ex>
   Main PID: 22652 (code=exited, status=0/SUCCESS)
      Tasks: 1 (limit: 4526)
     Memory: 1M (peak: 2.5M)
        CPU: 13ms
     CGroup: /system.slice/wacom-inputattach.service
             └─22653 /usr/bin/inputattach --daemon --baud 19200 --w8001 /dev/ttyS0

Dec 31 17:26:41 mx-lifebook7 systemd[1]: Starting wacom-inputattach.service - Inputattach for Wacom>
Dec 31 17:26:41 mx-lifebook7 systemd[1]: Finished wacom-inputattach.service - Inputattach for Wacom>
thunderysteak@mx-lifebook7:~
$ xsetwacom list devices
Wacom Serial Penabled Pen stylus	id: 16	type: STYLUS    
Wacom Serial Penabled Pen eraser	id: 17	type: ERASER 
```

Sources:  
https://github.com/linuxwacom/xf86-input-wacom/issues/157  
https://www.linuxquestions.org/questions/linux-hardware-18/serial-wacom-device-broken-by-debian-12-a-4175731876-print/




