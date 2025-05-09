# UGREEN-Fan-Control

After multiple searches I found a bunch of posts about loud fans but not how to control the fans.
This applies to those who do not use UGOS PRO but __unRAID, Debian, Ubuntu, Fedora__ etc.

Now it is possible to control the fans of a UGREEN NAS ! :)

Here is a step by step guide on how to do this:

## Package Requirements

- gcc
- make
- dkms
- dwarves
- kernel-headers
- lm_sensors

## System requirements to set up fan control

- SSH Client
- Basic knowledge with Linux and terminal commands

## Install Guide

1) SSH into your UGREEN NAS

2) Install the packages mentioned above like

```bash
sudo dnf install gcc make dkms dwarves kernel-headers sensors
```

3) Building the dkms module and installing it

```bash
cd /it87
make -j4
sudo make install
```

> [!NOTE]
> If you see this:
> __Skipping BTF generation [module name] due to unavailabilty of vmlinux.__
>
> You can simply run:
>
> ```bash
> cp /sys/kernel/btf/vmlinux /usr/lib/modules/`uname -r`/build/
> ```
>
> And clean up the previous, interrupted build and do a clean build from scratch
>
> ```bash
> make clean && make -j4 && sudo make install
> ```

1) Testing and configure the fans by configure lm_sensors

```bash
sudo sensors-detect
```

You can answer all questions with Y.

> [!NOTE]
> If you have previously executed lm_sensor in and the dkms module has not yet been installed, you may see the following message:
>
> ```txt
> Found `ITE IT8613E Super IO Sensors'                        Success!
> (address 0xa30, driver `to-be-written')
> ```
>
> Thats normal behavior and still will appear, even when the driver has been installed. But the interface to the ventilation is now available

Do determine now which fan uses which channel, we can run pwmconfig which makes it easy for us, to detect:

```bash
sudo pwmconfig
```

This small application will take over for creating the fancontrol config file in /etc

Finally, to be on the safe side, we can activate the fancontrol service at boot time

```bash
systemctl enable --now fancontrol
```

### Why did i that?

The idea for this project has been brought by this [Reddit post](https://www.reddit.com/r/unRAID/comments/1dzep0s/how_to_configure_fan_control_ugreen_nas/)

### Who wrote the dkms module?

That was written by 
 *  Copyright (C) 2001 Chris Gauthron
 *  Copyright (C) 2005-2010 Jean Delvare <jdelvare@suse.de>
and archived by [a1wong](https://github.com/a1wong/it87)

## Results

Tested with

```txt
# sensors-detect version 3.6.0
# System: UGREEN DXP2800 [EM_DXP2800_V1.0.25]
# Board: Default string Default string
# OS: Fedora 42 Server Edition
# Kernel: 6.14.5-300.fc42.x86_64 x86_64
# Processor: Intel(R) N100 (6/190/0)
```

```bash
root@lainpool:/# fancontrol
Loading configuration from /etc/fancontrol ...

Common settings:
  INTERVAL=10

Settings for hwmon2/pwm3:
  Depends on hwmon1/temp3_input
  Controls hwmon2/fan3_input
  MINTEMP=22
  MAXTEMP=60
  MINSTART=105
  MINSTOP=26
  MINPWM=24
  MAXPWM=255
  AVERAGE=1
```

```bash
sensors
it8613-isa-0a30
Adapter: ISA adapter
in0:         660.00 mV (min =  +0.00 V, max =  +2.81 V)
in1:           1.12 V  (min =  +0.00 V, max =  +2.81 V)
in2:           2.07 V  (min =  +0.00 V, max =  +2.81 V)
in4:           2.06 V  (min =  +0.00 V, max =  +2.81 V)
in5:           2.08 V  (min =  +0.00 V, max =  +2.81 V)
3VSB:          3.30 V  (min =  +0.00 V, max =  +5.61 V)
Vbat:          3.15 V  
+3.3V:         3.37 V  
fan2:           0 RPM  (min =    0 RPM)
fan3:        1726 RPM  (min =    0 RPM)
temp1:        +40.0°C  (low  = -128.0°C, high = +127.0°C)  sensor = thermistor
temp2:        +23.0°C  (low  = -128.0°C, high = +127.0°C)  sensor = thermistor
temp3:        +42.0°C  (low  = -128.0°C, high = +127.0°C)
intrusion0:  ALARM

acpitz-acpi-0
Adapter: ACPI interface
temp1:        +27.8°C  

coretemp-isa-0000
Adapter: ISA adapter
Package id 0:  +49.0°C  (high = +105.0°C, crit = +105.0°C)
Core 0:        +49.0°C  (high = +105.0°C, crit = +105.0°C)
Core 1:        +49.0°C  (high = +105.0°C, crit = +105.0°C)
Core 2:        +49.0°C  (high = +105.0°C, crit = +105.0°C)
Core 3:        +49.0°C  (high = +105.0°C, crit = +105.0°C)
```

# Bugs

Please report them here.
Anything related to dkms, please open a ticket at [a1wong](https://github.com/a1wong/it87) repository.
I do not maintain this part of the project.

## Donations

It took me a few hours to prepare, testing and deliver this for you. :)
I'll appreciate any contribution to the coffee fund :3

BTC: ```3EdkooEbQJurjCHScwUjPHGCCszoFh1pmM```

ETH: ```0x0dB50ef6C03c354795e306133B71A69d8F2e9cc6```
