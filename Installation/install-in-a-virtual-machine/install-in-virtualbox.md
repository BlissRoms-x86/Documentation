---
layout: post
title: '[deprecated]Bliss OS: How to install Bliss OS on VirtualBox'
date: '2020-08-27 23:17 +0800'
---

# Install in VirtualBox

This article is a draft content to describe how to install `Bliss OS` on `VirtualBox`. It combines [`android-x86` `VirtualBox` howto](https://www.android-x86.org/documentation/virtualbox.html) and our old article:"Install Bliss OS on a VM \(`virtualbox`\)".

## Create a new VM

If you have not already created a `VirtualBox` virtual machine for `Bliss OS` yet, do so as follows:

1. Click the "New" button, and name your new virtual machine however you like. Set Type to Linux, and Version to Linux 2.6 / 3.x / 4.x. Note that you should choose the appropriate bit type for the version of `Bliss OS` that you downloaded.
2. Specify how much RAM will be allocated to your virtual machine when you run it. `Android` doesn't specify a bare-minimum requirement for memory, just keep in mind what apps you plan on running. 2GB \(2048MB\) is a good place to start, and you can change this later if you need to.
3. Create a new Hard disk image which will act as your machine's storage. The recommended starting size of 8GB is enough. Click through the rest of the options for creating your Hard disk.

Your virtual machine has now been created. It still needs to be initially installed at this point.

## Settings

Tested on `VirtualBox` 64-bit for `Ubuntu` 20.04 with `Intel` and `AMD` \(`Ryzen R7 4800H`\) processors, version 6.1. `Bliss OS` version `v11.11`, 64-bit.

Select your machine, then click the Settings button and refer to the below recommended configuration to make sure your settings match.

1. \[_System_\] Recommended: Processor\(s\) should be set above 1 if you have more than one virtual processor in your host system. Failure to do so means every single app \(like Google Chrome\) might crush if you try to use it.
2. \[_Display_\]:

   2.1. _Optional_: Video Memory may be increased beyond the minimum selected automatically. The affects of this are unknown.

   2.2 _Mandatory_: Unless guest additions are installed, change the default VMSVGA to VBoxVGA.

   2.3 _Optional_: Enable 3D Acceleration may be checked. The Linux Guest Additions must \(`VirtualBox` v6.1+\) / may \(`VirtualBox` v6.0 and below\) need to be installed to get any benefit from this.

   Failure to do so means you won't even be able to launch `Bliss OS` in the first place.

3. \[_Storage_\] Find the first "Empty" item \(this should have an icon of a CD\). In the Attributes, click on the CD icon with a small down arrow, and pick "Choose Optical Virtual Disk File...". Specify the `Bliss OS` ISO that you downloaded.
4. \[_Audio_\] Intel HD Audio seems to be natively supported in `Bliss OS`.
5. \[_Network_\] By default, your installation of `Bliss OS` will be able to automatically connect to the internet. If not, you can try to enable WiFi in "Settings/Network & Internet", and connect to showing `VirtWifi`. If you do not want to connect to the internet in `VirtualBox`, uncheck "Enable Network Adapter" under the Adapter 1 tab.

## Install

Now we can click the start on `VirtualBox` to start the VM.

When entering installation page, 1. select "Installation - Install Bliss-OS to harddisk". 2. Press `D` to detect devices. 3. Press `C` to create and modify partitions. If asked, select "No" for `GPT`. 4. Select "New", "Primary", "full size", and then make it bootable. 5. "Write to disk". 6. "Quit". 7. Select the partition and then reformat to `ext4`. 8. Select "Yes" to install `GRUB`. 9. Select "Yes" to make the `/system` directory writable.

After than, just closing the installing window, and restart this VM instance, and select "Advanced Options" and "Boot from local device" to select `Bliss OS` with different options to start.

To here, you should have a fully working Bliss OS install in a VM, or at least something that works... even if it may be slow. Again, `Android` on VM is generally not a good idea and you will get a lot more performance if you install `Bliss OS` to the actual hardware.

