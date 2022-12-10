# Manual Install on Linux

## Installation

**!!ATTENTION!!** Bliss OS also supports Jaxparrow's Android-x86 Installer for Linux. Source can be found here: [https://github.com/jaxparrow07/Androidx86-Installer-Linux](https://github.com/jaxparrow07/Androidx86-Installer-Linux)&#x20;

Create a directory at / as /blissos

1. Extract initrd.img, ramdisk.img, kernel and system.\* from your desired blissOS ISO into the /blissos directory.
2. Make a dir called /blissos/data (Only valid for android-pie, for higher versions you need data.img, can be created with make\_ext4fs)
3. Create a new grub entry with this the following code:&#x20;

`menuentry "BlissOS" { set SOURCE_NAME="blissos" search --set=root --file /$SOURCE_NAME/kernel `\
`      linux /$SOURCE_NAME/kernel quiet root=/dev/ram0 androidboot.hardware=android_x86_64 androidboot.selinux=permissive acpi_sleep=s3_bios,s3_mode SRC=/$SOURCE_NAME  `\
`     initrd /$SOURCE_NAME/initrd.img`\
`}`

### **Example for making a 8gb image:**&#x20;

```
dd if=/dev/zero of=data.img bs=1024 count=0 seek=$[102410248]
sudo mkfs.ext4 -F data.img
```
