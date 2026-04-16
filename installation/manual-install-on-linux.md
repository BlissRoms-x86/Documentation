# Manual Install on Linux

## Installation

Create a directory at / as /blissos

1. Extract `initrd.img`, `ramdisk.img`, `kernel` and system.\* from your desired blissOS ISO into the /blissos directory. `ramdisk.img` can be ignored for Android 10 and newer as it is already merged into the system (system-as-root).
2. Make a directory called `/blissos/data`. This will only work for ext4 filesystems, for NTFS and other filesystems or if you are having bootloop you need data.img, can be created with make\_ext4fs. 
3. Create a new grub entry with this the following code:&#x20;

```
menuentry "BlissOS (Default) w/ FFMPEG" { 
    set SOURCE_NAME="blissos"
    search --set=root --file /$SOURCE_NAME/kernel 
    linux /$SOURCE_NAME/kernel FFMPEG_CODEC=1 FFMPEG_PREFER_C2=1 quiet root=/dev/ram0 SRC=/$SOURCE_NAME  
    initrd /$SOURCE_NAME/initrd.img
}

menuentry "BlissOS (Intel) w/ FFMPEG" { 
    set SOURCE_NAME="blissos"
    search --set=root --file /$SOURCE_NAME/kernel 
    linux /$SOURCE_NAME/kernel HWC=drm_minigbm_celadon GRALLOC=minigbm FFMPEG_CODEC=1 FFMPEG_PREFER_C2=1 quiet root=/dev/ram0 SRC=/$SOURCE_NAME  
    initrd /$SOURCE_NAME/initrd.img
}

menuentry "BlissOS PC-Mode (Default) w/ FFMPEG" { 
    set SOURCE_NAME="blissos"
    search --set=root --file /$SOURCE_NAME/kernel 
    linux /$SOURCE_NAME/kernel  quiet root=/dev/ram0 SRC=/$SOURCE_NAME  
    initrd /$SOURCE_NAME/initrd.img
}

menuentry "BlissOS PC-Mode (Intel) w/ FFMPEG" { 
    set SOURCE_NAME="blissos"
    search --set=root --file /$SOURCE_NAME/kernel 
    linux /$SOURCE_NAME/kernel PC_MODE=1 HWC=drm_minigbm_celadon GRALLOC=minigbm FFMPEG_CODEC=1 FFMPEG_PREFER_C2=1 quiet root=/dev/ram0 SRC=/$SOURCE_NAME  
    initrd /$SOURCE_NAME/initrd.img
}
```

### **Example for making a 8gb image:**&#x20;

```
dd if=/dev/zero of=data.img bs=1 count=0 seek=8G
sudo mkfs.ext4 -F data.img
```
Alternatively, one can use `truncate`
```
truncate -s 8G data.img
mkfs.ext4 -F -b 4096 -L "/data" data.img
```

Here are some additional tips for installing BlissOS on Linux:
- Do not try to install Bliss OS on exotic linux filesystems such ZFS, XFS, BtrFS, currently not every filesystem has built-in support in the Bliss OS kernel, ext4 is supported. If you install it on an unsupported filesystem, it will be stuck at `Detecting Android-x86...`. You will probably have to compile your own kernel and use a modified initrd.img to boot from other filesystems.
- If you want read-write /system or being able to make changes to the system, simply extract system.img from system.img using a tool that support Zstandard compressed squashFS images. It can also be mounted.
- For `data.img`, it would be good to repair/check filesystem regularly using command `e2fsck -f data.img`. 

**!!ATTENTION!!** Bliss OS 14.3 and below versions also support Jaxparrow's Android-x86 Installer for Linux. Source can be found here: [https://github.com/jaxparrow07/Androidx86-Installer-Linux](https://github.com/jaxparrow07/Androidx86-Installer-Linux)&#x20;
