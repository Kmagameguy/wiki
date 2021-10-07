# Controlling AMDGPU
AMDGPU is the generic name for the AMD drivers for linux.  If you are using Pop! OS then the driver is already included in the distro's kernel. Nice.

## Reading GPU Properties
Given the linux philosophy of "everything is a file", accessing GPU properties is trivial if you know where to look.  Some notable information is outlined below:

### Device Properties Directory

Some of the enumerable values are system dependent, but they should (conceptually) be the same.  At time of writing here is where my device properties can be found:

```
$ ls /sys/class/drm/card0/device/hwmon/hwmon2/

[..]
temp1_crit             temp1_label           temp2_input       temp3_emergency
temp1_crit_hyst        temp2_crit            temp2_label       temp3_input
temp_1_emergency       temp2_crit_hyst       temp3_crit        temp3_label
temp1_input            temp2_emergency       temp3_crit_hyst
[...]
```

You can `cat`  the temp*_label files to see what each sensor is responsible for.  E.g.:
- temp1 = edge temperature
- temp2 = junction temperature
- temp3 = mem temperature

### Reading Temperature Values
No one wants their system to overheat, so you may occasionally want to spot check the GPU's temperature sensors, specifically the edge value.  You can do that like so:

```
$ cat /sys/class/drm/card0/device/hwmon/hwmon2/temp1_input

52000 
```

Where `52000` = 52 centigrade with three zeroes attached.

### Reading & Writing Fan Speed Values
**Reading Values**  
By default the fan speeds are operated in "automatic" mode by the system.  You can check the current fan speed like so:

```
$ cat /sys/class/drm/card0/device/hwmon/hwmon2/pwm1

73
```

Where 73 = a fan speed of 28% (values are reported in PWM cycles, of which 255 is the maximum value.  A factor of 2.55 is used to convert back and forth.  E.g. multiply desired fan speed % by 2.55 to get the PWM value; or divide the PWM value by 2.55 to get the fan speed %).

**Writing Fan Speed Values**  
You can take manual control of the fan speed if you know the GPU will be under stress or don't trust the built-in automatic management engine.  Do so like this:

First, assume manual control:

```
$ echo "1" > /sys/class/drm/card0/device/hwmon/hwmon2/pwm1_enable
```

Once set to manual mode you can push values to the fan speed controller.  First make sure you convert your desired fan speed % into a PWM value.

For example, 50% * 2.55 = ﻿128 (rounded to nearest whole number).  Set it like so:

```
$ echo "128" > /sys/class/drm/card0/device/hwmon/hwmon2/pwm1
```

**Resetting Fan Speed Management**  
You may also resume automatic fan speed management. Do this like so:

```
$ echo "2" > /sys/class/drm/card0/device/hwmon/hwmon2/pwm1_enable
```

???+ note
     You may need to restart the AMDGPU service to see the automatic management kick back up.  A reboot will work if you're not sure how to restart the service.

## References
The great Arch Wiki entry for the Fan Speed Control contains a section dedicated to the AMDGPU driver:

<https://wiki.archlinux.org/title/fanspeedcontrol_#AMDGPU_sysfs_fan_control>

The `Issues` page for the AMDGPU driver can be useful if something seems broken.  Kernel updates are notorious for breaking the AMDGPU driver features; upgrade with caution:

<https://gitlab.freedesktop.org/drm/amd/-/issues>
