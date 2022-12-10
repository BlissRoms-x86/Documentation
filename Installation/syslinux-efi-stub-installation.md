# Syslinux EFI Stub Installation

## Use syslinux EFI to run Bliss OS 7.x/10.x/11.x/12.x/14.x

Thanks to @IcedCube for the original post! This method is **NOT recommended** as it is fairly bleeding-edge and experimental, but it should help booting on Chinese tablets that do not want to run `grub`.

Use a Linux installation for the following procedure.

### Part 1 - Grab the required tools

Install `unsquashfs` \(part of `squashfs-tools`\).

### Part 2 - Get Bliss OS

Grab the latest build of Bliss OS 7.x/10.x/11.x/12.x/14.x

### Part 3 - Get the syslinux EFI bootstrap

[Grab the `.zip` file from @IcedCube's original post](https://forum.xda-developers.com/showpost.php?p=74977694&postcount=1237), and extract it to the root of the USB drive. This will bootstrap syslinux EFI onto it.

Then, make a folder called `android`.

Now, open up the `.iso` in an archive program. Extract the following files form the root directory of the `.iso` image to the USB drive's `android` folder \( ramdisk.img is not used in Android 10+ \):

* `initrd.img`
* `ramdisk.img`
* `kernel`

Extract `system.sfs` to a folder somewhere, such as `/tmp`.

Open a terminal and change directory \(using `cd`\) to `/tmp`. Run `ls` and confirm that `system.sfs` is shown in the file list. If there is no output, start over as the file is misplaced.

Run the following:

`unsquashfs ./system.sfs`

This will make a new directory called `squashfs_root`.

### Part 4 - Version specific

#### If you are using Bliss 7.x

Change directory to `squashfs_root` and run `ls`. There should only be one file - a `system.img` inside the directory. Copy the file to the USB's `android` folder.

### If you are using Bliss 10.x/11.x/12.x/14.x

Change directory to `squashfs_root`. The structure is a complete Android root filesystem. To install Bliss OS, the files will need to be in a system image. The following steps will guide you through creating a 2 GB `system.img` file, formatting it, mounting it, and copying the contents of `squashfs_root` into the new disk image.

Execute:

```text
mkdir /mnt/tempMount
truncate /tmp/system.img --size=2G
mkfs.ext4 -m0 /tmp/system.img
sudo mount -o loop /tmp/system.img /mnt/tempMount
sudo cp -prv /tmp/squashfs_root/* /mnt/tempMount/
sync
sudo umount /mnt/tempMount
```

The `sync` command might take some time.

Now copy the `/tmp/system.img` file to your USB's Android folder.

### Part 5 - Creating the data image

First, find where your USB drive is mounted. It is usually in `/mnt` or `/media` \(ex. `/media/USB`\).

`cd` into the `android` folder.

We will create a 3 GB data image file. You can attempt to create a 4 GB image but FAT32 maxes out at 4 GB per file. If your system supports exFAT or NTFS, you may try and use it.

```text
truncate data.img --size=3G
mkfs.ext4 -m0 data.img
sync
```

This will be an completely empty `ext4` disk image, but will be enough to run Bliss.

Finally, check to ensure everything is in structured like so:

```text
<ROOT>
- syslinux.cfg
- android/
-- kernel
-- system.img
-- data.img
-- ramdisk.img
-- initrd.img
- EFI/
-- BOOT/
--- bootia32.efi
--- bootx64.efi
--- ldlinux.e32
--- ldlinux.e64
```

Need to add some kernel parameters? Open `syslinux.cfg` and add them before the `initrd=/android/initrd.img` statement.

Unmount the USB from your computer. Plug it into your device and use the BIOS to boot from your UEFI USB Drive, partition 1. If all goes well, you will get a black screen with small white text saying "Booting Android..." followed by loading files. You should get the Linux kernel text, then see the Bliss boot animation play after a couple minutes depending on your USB drive read/write speed.

