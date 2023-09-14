---
description: Install Bliss OS in a Virtual Machine of your choice
---

# Install in a Virtual Machine

This method is **incomplete**, a **work-in-progress**. Android in general do not run great in VMs, because they do not have the proper drivers. As such performance is greatly reduced if you use this method. Please only install Bliss OS in VMs for evaluation, and keep in mind that the performance exhibited by Bliss OS in such an environment is not a true representation of actual performance on bare metal. With that in mind, let's get started!

First, make sure your CPU is capable of running VMs. For Intel, it is usually Intel VT-x and VT-d. For AMD, it is usually AMD-V. You may also need IOMMU support, although it is probably not necessary since you won't be passing through GPUs.

Download the latest Bliss OS `.iso` from our website. Then, download the latest version of VirtualBox, and the latest VirtualBox **extension pack**. Install both executables.

Once inside VirtualBox, click on "New." For the name, type "Bliss OS." For the type, select `Linux`, and then `Linux 2.6 / 3.x / 4.x (64-bit)` for the version. Set memory size to 2048 MB \(2 GB\) or more. For the disk, accept the default options for the disk type. For the size, set it to 20 GB. VirtualBox should initialize a new virtual machine.

We need to tweak a couple more settings. Click on "Settings" on the top bar. Allocate more logical processors in System &gt; Processor. Drag the slider to the right, keeping it **inside** the green bar, as you want to leave a couple of logical processors for the host operating system. For the display, try allocating all of the VRAM. \(128 MB most likely\) Enable 3D Acceleration. Click OK to save settings.

Now click on Start. VirtualBox will ask you for the CD image. Click on the folder with the green up arrow, and then select the ISO you downloaded earlier.

Select "Installation - Install Bliss-OS to harddisk" Press D to detect devices. Press C to create and modify partitions. If asked, select No for GPT. Select New, Primary, full size, and then make it bootable. Write to disk. Quit. Select the partition and then reformat to ext4. Select Yes to install GRUB. (**On Bliss OS 9.x - 11.x**) Select Yes to make the /system directory writable.

[**PLEASE NOTE**]: Recent versions of Bliss OS require manually extracting the system.img from within the system.sfs, removing the system.sfs afterwards from the installed directory in order to allow the system to be mounted as R/W. Please follow further instructions here for that process: [Remount System as Read-Write](https://docs.blissos.org/knowledgebase/troubleshooting/remount-system-as-read-write/)

At this point the installation should be complete. But when you reboot, you will notice that the screen is completely black with a cursor at the top-left. This is because there is no display drivers for the virtual machine. Reset the instance, edit the boot parameters, and add `nomodeset` to the end. Bliss OS should then boot fully.

Congratulations! You should have a fully working Bliss OS install in a VM, or at least something that works... even if it may be slow. Again, Android on VM is generally not a good idea and you will get a lot more performance if you install Bliss OS to the actual hardware.

