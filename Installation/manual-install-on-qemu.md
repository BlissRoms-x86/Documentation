# Manual Install on QEMU

> The following tutorial has been tested for `Bliss-Zenith-v16.9.6-x86_64-OFFICIAL-gapps-20240715.iso` on linux host.

## Preparation

[Bliss OS live CD ISO](https://blissos.org/index.html#download) and [QEMU](https://www.qemu.org/download).

Create a new empty virtual disk:

```
dd if=/dev/zero of=<working-directory>/android.img bs=1 count=0 seek=8G
mkfs.ext4 -F <working-directory>/android.img
```

## Installation

extract the content of Bliss OS live CD ISO by just mount it somewhere:

```
sudo mount <path-to-the-livecd-iso> /mnt
```

mount `android.img`:

```
mkdir <android-directory>
sudo mount <working-directory>/android.img <android-directory>
```

and copy the following required image:

```
cd /mnt
sudo cp system.efs <android-directory>
sudo cp kernel initrd.img <working-directory>
```

and make an empty `data` directory inside `android.img`:

```
mkdir <android-directory>/data
```

leave it empty and the system will automatically generate necessary files on there on first boot.

unmount previous mounted filesystem:
```
sudo umount <android-directory>
sudo umount /mnt
```

the unmount step is optional, but it's good practice to unmount it if you not used it anymore.

create a wrapper script to run qemu:

```
#!/bin/bash

qemu-system-x86_64 -M q35 -enable-kvm -cpu host -smp 4 -m 4096 \
-drive file=<working-directory>/android.img,format=raw,cache=none,if=virtio \
-display gtk,gl=es,show-cursor=on \
-device virtio-vga-gl \
-net nic,model=virtio-net-pci -net user,hostfwd=tcp::5555-:5555 \
-machine vmport=off -machine q35 \
-device virtio-tablet-pci -device virtio-keyboard-pci \
-serial mon:stdio \
-audiodev pipewire,id=snd0 -device AC97,audiodev=snd0 \
-kernel <working-directory>/kernel -append "root=/dev/ram0 quiet SRC=/ VIRT_WIFI=1 console=ttyS0 androidboot.enable_console=1" \
-initrd <working-directory>/initrd.img
```

save it as `run_emulator.sh` on `<working-directory>`, chmod it, run it, and enjoy!

please note that some option on the script may need an adjustment based on your linux environment.

## Tips

if you already have bliss os installed on qemu's qcow virtual disk, you might want to consider use this way to gain the following advantages:
- boost the boot time a little bit by skip bootloader phase
- make modify kernel parameter a lot easier and quicker

if you decided to switch your workflow to this way, consider migrate your android's data by mount qcow2 disk through network block device, please refer to [this post](https://unix.stackexchange.com/questions/268460/how-to-mount-qcow2-image) for HOWTO, but keep in mind sometimes migrating process will not work as expected and can cause boot loop, so please proceed with caution!

## Note
I don't know how to setup A/B partition scheme manually to add seamless system update, so if you want to use A/B partition scheme, I recommend you to use the installer instead of this manual installation.
