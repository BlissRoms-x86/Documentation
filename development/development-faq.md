# Development FAQ

Much of what we are doing with Android source is lacking proper documentation as to what it does, and how. This document will try to answer a few of those questions. 

1. What is bootable/newinstaller repo ? What is the purpose of it ? what does it contain ?

   **Bootable/newinstaller was initally created to handle the ramdisk.img, initrd.img and install.img creation, as well as packaging the final .iso/.img/,rpm file.  This means it contains a set of scripts for the initrd image, a set of scripts for ths te installer image, and scripts to setup the filesystem and root environment.**  

2. How grub is handled in BlissOS ? How does it show first when normal AndroidOS boots straight to bootanimation ?

   **This is done by using the typical Linux method for booting with initrd.img, kernel, and system.img/ramdisk.img, where a grub menuentry is created that references the location of each img, and loads itlike normal linux would. The initrd.img and kernel load first, then after zygote loads, the init.sh file takes over and set's up the remaining hardware dynamically, and then initiallizes the graphics composer.**  

3. How does it recognize PC Bios on reboot ? as normally there is Bootloader in Android devices!

   **See above answers**  

4. What are the different Partitions that BlissOS Requires for running? This is in terms of Developer point of view nd not user! Does it have same partition table as that of any Android mobile ?

   **See above answers, with the addition of the install process creating a data.img or data folder on the install partiton, and that is detected by the initrd startup scripts and set as Internal Storage.**  

5. What type of kernels BlissOS supports or in general an Android-x86 support ? r there any specific one's ?

   **Linux/Google Android 11/12 LTS kernels, with about 20-30 patches added on top to support our filesystem and modularize all the drivers or options.**
