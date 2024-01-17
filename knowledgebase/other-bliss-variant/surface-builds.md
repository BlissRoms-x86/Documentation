# BlissOS for Surface devices

This version is designed specifically for the Microsoft Surface devices. The kernel use almost the same LTS kernel but with extra patches from the [linux-surface](https://github.com/linux-surface) project to ensure the most compatibility.

While this build indicate that this is for Surface devices, not all devices need to use it. To know if your device need to install this build or not, go to this section of linux-surface wiki:

https://github.com/linux-surface/linux-surface/wiki/Supported-Devices-and-Features

If you see your device has the number that said `Requires linux-surface kernel` or `Requires linux-surface kernel >=x.x.x` then this is the build you are looking for.

The Surface build of BlissOS also include `iptsd` an userspace touch processing daemon for Microsoft Surface devices using Intel Precise Touch technology made by linux-surface team. If you have any issue with the touchscreen, try to Calibrate or tweak the daemon. Go to [Calibrate and configure iptsd](/configuration/Calibrate-and-configure-iptsd.md) to know how.
