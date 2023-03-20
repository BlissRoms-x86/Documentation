# Install from Windows - UEFI/ESP \(64-bit\)

### Before you begin

**You must turn off Secure Boot from your BIOS if you have it enabled. Otherwise it won't boot.**
**There is no need to format or partition the disk unless you want to. Installation along with windows won't wipe the drive and works fine**

For 32bit or MBR/Legacy install, Axon from Supreme-Gamers put together a great installer that works with MBR or EFI and installs on NTFS or EXT4. Check it out here: [https://supreme-gamers.com/t/advanced-android-x86-installer-for-windows.300/](https://supreme-gamers.com/t/advanced-android-x86-installer-for-windows.300/)

## Windows-based installer - UEFI/ESP \(64-bit\)

**Proceed at your own risk.** This method might be the easiest currently if you understand what you are doing.

### Part 1 - Downloading and installing the Grub2Win Bootloader.

Download the file from here - https://sourceforge.net/projects/grub2win/ and proceed to install it. 
Grub2Win allows use to make changes to the bootloader within Windows its and makes the overall process a lot smoother. It is recommended that bootloader be installed in the default directory (C:). 

### Part 2 - Installing BlissOS

Open the C: drive and a new folder and rename it to "AndroidOS". 
Next mount the iso downloaded and copy the following files to paste it inside the AndroidOS folder we created.

Files: 

*initrd.img
*install.img
*Kernel
*System.sfs

After that done, we need one more file and that is the data.img which is storage of the OS. To make the data.img, there are a few options. But i recommend RMXtools, 
Open RMXtools [RMXtools_v1.8_UserUpload.Net.zip](https://github.com/BlissRoms-x86/Documentation/files/10987199/RMXtools_v1.8_UserUpload.Net.zip) and drag the silder to change the size of data.img file or set the size you want in MB in the "Enter data.img size". Once the size is set, click "Create-img" and set the destination to the AndroidOS folder. Now all thats left is add the boot entry.


### Part 3 - Adding UEFI Boot Entry in Grub2Win

Open the Grun2Win application and Click the "Manage Boot Menu" button. Then click the "Add A New Entry" button. In the new window, Select "Android" as the Type.
Next Change "The Current Android Kernel Path" to "/AndroidOS/kernel". Click "Apply" in both the orange background window and blue background window. Finally click "Ok" and we are done. 

The "Linux Boot Parms" should have the following command. Copy and paste it, then click apply if it doesnt show up by default.

"root=/dev/ram0 verbose androidboot.selinux=permissive vmalloc=512M buildvariant=userdebug quiet "

quiet - Hides the boot messages.
vmalloc - Above 512M is probably more than enough. Remember this the memory allocation for the kernel and not the the system.

This is an optional flag to add which can help with issue regarding video play in Youtube. If you are getting some green lines when playing back HD video trying adding the following flags at the end. Note I tested it on intel UHD graphics.

"GRALLOC=minigbm HWC=drm_minigbm_celadon"

Note : Using Grub2Win allows you to change boot flags from windows and Grub bootloader allow you to editing from bootloader before booting. In both, same flags are used. Edits made in Grub2Win are persistent while edits from Grub bootloade are temporary. I recommend that editing flags from the bootloader should be your first option because if anything goes wrong, a quick reboot can reset it. 

<img width="572" alt="1" src="https://user-images.githubusercontent.com/19780503/225529366-8cebb14f-bc1b-4e40-8221-b2ed18f3f5ee.png">

<img width="593" alt="2" src="https://user-images.githubusercontent.com/19780503/225529384-39102d34-c6c8-4687-9a3b-70071eaffb88.png">

<img width="575" alt="3" src="https://user-images.githubusercontent.com/19780503/225529385-20862b50-8fc4-4205-907e-343ee03d0874.png">

Reboot and select android and enjoy your BlissOS install.

By Default, the AndroidOS wouold be the last option and if it nothing is selected it will boot Windows.

* This is for the installation in the C: drive.

### Troubleshooting:

For questions, please join our Community Support chats on Telegram(https://t.me/blissx86) or Discord(https://discord.com/invite/F9n5gbdNy2) and search for answers there.

For most of the issues, changing the boot flag options is answer. You can temperoraly edit the boot flags by press "E" when AndroidOS is highlighted. Usual Grub boot flags are to be used. 

HD DRM protected streaming won't work because of the Widevine L3 certification and there is nothing we can to fix it. 

**Here are some boot flags that you can for troubleshooting.**

* (Default) w/ FFMPEG": Use the default settings with FFMPEG enabled.

"FFMPEG_CODEC=1": Enable FFMPEG codec support.

"FFMPEG_PREFER_C2=1": Use FFMPEG C2 as the preferred codec.

"(Intel) w/ FFMPEG": Use Intel-specific settings with FFMPEG enabled.

"HWC=drm_minigbm_celadon": Use the drm_minigbm_celadon hardware composer.

"GRALLOC=minigbm": Use the minigbm graphics allocator.

"PC-Mode (Default)": Use default settings in PC mode.

"PC_MODE=1": Enable PC mode.

"PC-Mode (Default) w/ FFMPEG": Use default settings with FFMPEG enabled in PC mode.

"PC-Mode (Intel)": Use Intel-specific settings in PC mode.

"PC-Mode (Intel) w/ FFMPEG": Use Intel-specific settings with FFMPEG enabled in PC mode.

* "Debugging": Choose a debugging option.

"Debug": Enable general debugging.

"Debug gralloc.gbm": Enable debugging for the gralloc.gbm graphics allocator.

"Debug drmfb-composer": Enable debugging for the drmfb-composer hardware composer.

"Debug hwcomposer.drm": Enable debugging for the hwcomposer.drm hardware composer.

"Debug gralloc.minigbm": Enable debugging for the gralloc.minigbm graphics allocator.

"Debug gralloc.minigbm_gbm_mesa": Enable debugging for the gralloc.minigbm_gbm_mesa graphics allocator.

"Debug hwcomposer.drm_minigbm": Enable debugging for the hwcomposer.drm_minigbm hardware composer.

"Debug hwcomposer.drm_minigbm_celadon": Enable debugging for the hwcomposer.drm_minigbm_celadon hardware composer.

"Debug hwcomposer.intel": Enable debugging for the hwcomposer.intel hardware composer.

*These options below are only available when used with the old method. That is using .img directly with a VM or when using a bootable flash drive.

* "VM Options ->": Choose a virtual machine option.

"QEMU/KVM - Virgl - SW-FFMPEG": Use QEMU/KVM with Virgl and software FFMPEG.

"nomodeset HWACCEL=0": Disable hardware acceleration for Vbox/VMWare.

"Debug QEMU/KVM - Virgl - SW-FFMPEG": Use debug settings with QEMU/KVM, Virgl, and software FFMPEG.

"DEBUG=2": Enable debugging with level 2.

"VMware - No HW Acceleration": Disable hardware acceleration for VMware.

"Debug Vbox/VMWare - No HW Acceleration": Use debug settings with Vbox/VMWare and no hardware acceleration.

"Advanced options ->": Choose an advanced option.

"Vulkan support (experimental)": Enable experimental Vulkan support.

"SETUPWIZARD=0": Disable the setup wizard.

"No Hardware Acceleration": Disable hardware acceleration.

Updated by Bilawal(https://github.com/FrozenBrick)
