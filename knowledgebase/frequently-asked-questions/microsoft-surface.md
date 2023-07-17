# Microsoft Surface IPTS

Many Microsoft surface devices will run well without any special consideration.  Use the standard Bliss distributions for these devices. See here [https://github.com/linux-surface/linux-surface/wiki/Supported-Devices-and-Features#feature-matrix](https://github.com/linux-surface/linux-surface/wiki/Supported-Devices-and-Features#feature-matrix) for specifics on device support.

If you have a Microsoft Surface device and touchscreen support is not enabled by default, this likely means that your device requires a special kernel for support. Builds for Surface can currently be found on our discord channel #releases. Builds will be built using BlissLabs Jenkins soon, just like the generic builds.

The surface builds include the linux-surface kernel and iptsd (Daemon for the Intel Precise Touch & Stylus driver). See [https://github.com/linux-surface/intel-precise-touch](https://github.com/linux-surface/intel-precise-touch) and [https://github.com/linux-surface/iptsd](https://github.com/linux-surface/iptsd) for details.
  
**If you have trouble with iptsd registering touches (single touch, touch turn into mouse, etc.):**

You can make a configuration file and put it into /data/vendor/ipts/<your_file>.conf. The developer behind iptsd already documented all the options in the sample iptsd.conf so if you have any trouble, try to tweak and make your own file.
[https://github.com/linux-surface/iptsd/blob/master/etc/iptsd.conf](https://github.com/linux-surface/iptsd/blob/master/etc/iptsd.conf)

**If you need to reset your IPTS touchscreen while the system is running:**

Open the alt-f1/f7 console, or built in terminal, and type:

`su  
rmmod ipts_surface && rmmod intel_ipts && modprobe intel_ipts && modprobe ipts_surface`

