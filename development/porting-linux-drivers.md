# Porting Linux Drivers

When it seems that there might be a missing driver or hardware support for your specific device, the best way to add support for that is o search online for a Linux kernel module for your target component. 

Once you've tracked down or created Linux drivers for your device, you have to turn it into a Linux kernel module, and then add it to external/kernel-drivers for it to be included in the current kernel build. Examples of others we add are here [https://github.com/BlissRoms-x86/external\_kernel-drivers/tree/r11-r36](https://github.com/BlissRoms-x86/external_kernel-drivers/tree/k5.12-b2) 

The examples only add one Android.mk file, and the one I use is generic for just about any kernel module that has a standard MAKEFILE, not a MAKEFILE.am file.

```text
#
# Copyright (C) 2018 The Android-x86 Open Source Project
#
# Licensed under the GNU General Public License Version 2 or later.
# You may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      http://www.gnu.org/licenses/gpl.html
#

LOCAL_PATH := $(my-dir)
LOCAL_MODULE := $(notdir $(LOCAL_PATH))
EXTRA_KERNEL_MODULE_PATH_$(LOCAL_MODULE) := $(LOCAL_PATH)
```

So you should really only need to add your kernel module folder to external/kernel-drivers/ and copy the Android.mk into it, then build source as normal. It'll get picked up as one of the last steps in building and packaging the kernel

Once you have it packaged up, it's best practice to compile the full source and test that it is functioning properly, and once confirmed, submit the change to [https://github.com/BlissRoms-x86/external\_kernel-drivers](https://github.com/BlissRoms-x86/external_kernel-drivers) as a pull-request. 
