# Microsoft Surface IPTS Gearlock Package

If you have a Microsoft Surface device and touchscreen support is not enabled by default, this likely means that your device requires a special kernel for support.

**Getting the Kernel:**

You should use the latest Generic i3/5/7/9 ISO \(11.13/14.3\) which comes with Gearlock, then download the surface kernel from SupremeGamers resource site \([https://supreme-gamers.com/t/android-x86-kernel-for-microsoft-surface-based-on-linux-surface-patches.389/](https://supreme-gamers.com/t/android-x86-kernel-for-microsoft-surface-based-on-linux-surface-patches.389/)\). and install it after initial boot from Gearlock Recovery mode.  
  
**If you need to reset your IPTS touchscreen while the system is running:**

Open the alt-f1/f7 console, or built in terminal, and type:

`su  
rmmod ipts_surface && rmmod intel_ipts && modprobe intel_ipts && modprobe ipts_surface`

