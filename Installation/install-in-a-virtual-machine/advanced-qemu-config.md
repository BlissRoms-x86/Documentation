---
layout: post
title: 'Advanced configuration for qemu'
date: ''
---
# Advanced configuration for qemu cli

Qemu is a very powerful tool that may be confusing for a lot people. However when using it we can get some very detailed and custom setups, this page will document some more advanced features that folk may wish to utilize with qemu. The majority of this page will assume running on linux. Many of these will not be compatible with windows. While this guide is oriented towards android. Many of the same concepts apply to general VM usage.

Many of the concepts on this page will not apply to all users, If you have been linked This document is a document and this warning is still present. Any questions or corrextions can be asked and posted on the Bliss OS telegram, Matrix, and Discord groups. Ping `@quackdoc` `@Quack Doc`.

[comment]: <> (TODO: update when virt-manager docs are availible)
This guide is for for qemu's cli. while many concepts still apply, virt-manager/libvirt documentation is being worked on.

## Evdev

One of the more useful and easy to implement features of qemu is evdev passthrough. Doing this can enable low latency and a better by using hotkeys to globally toggle device passthrough. 

There are two methods of doing this, we can use `virtio-input-host` and we can attach evdev to `virtio-mouse` and `virtio-keyboard`.Using `virtio-input-host` will give the guest exclusive accsess to the device, and will implicitly setup the proper device. This may cause bugs, and is likely not what all users would like, however this might be helpful for unique devices that don't fall under traditional inputs, and won't work with usb passthrough.

`virtio-input-host` is fairly easy to setup simply add. It does However comes at the detriment that you cannot use a hotkey to bind it back to the host without closing the VM. It accepts both `eventx` device files, as well as works with `by-id` paths. However the strength of it comes from being able to use any evdev device. This includes gamepads, some multitouch screens and more. 

The below to add a multi-touch device through to the VM.

```
-device virtio-input-host,id=touch0,evdev=/dev/input/eventX
```

A minimal example of this would be 

```bash
 #!/bin/bash
 qemu-system-x86_64 \
 -enable-kvm \
 -M q35 -m 4096 -smp 4 -cpu host \
 -bios /usr/share/ovmf/x64/OVMF.fd \
 -drive file=disks/bliss.qcow2,if=virtio \
 -device virtio-vga-gl -display sdl,gl=on \
 -device virtio-input-host,id=touch0,evdev=/dev/input/eventx
```

The more common option would be attaching evdev devices to `virtio-keyboard` and `virtio-mouse`. This will allow us to toggle back and forth between the the guest VM and the host OS. We need to attach the below output to qemu corresponding device ids.

```
 -object input-linux,id=mouse1,evdev=/dev/input/by-id/MOUSE_NAME
 -object input-linux,id=kbd1,evdev=/dev/input/by-id/KEYBOARD_NAME,grab_all=on,repeat=on
```
A functional example of this would be 

```bash
 #!/bin/bash
 qemu-system-x86_64 \
 -enable-kvm \
 -M q35 -m 4096 -smp 4 -cpu host \
 -bios /usr/share/ovmf/x64/OVMF.fd \
 -drive file=disks/bliss.qcow2,if=virtio \
 -device virtio-tablet,id-mouse1 \
 -device virtio-keyboard,id=kbd1 \
 -device virtio-vga-gl -display sdl,gl=on \
 -object input-linux,id=mouse1,evdev=/dev/input/by-id/MOUSE_NAME \
 -object input-linux,id=kbd1,evdev=/dev/input/by-id/KEYBOARD_NAME,grab_all=on,repeat=on
```

By default you can use left and right `ctrl` together to change whether the device is used by the host or the VM.

## Flexible bash scripting

Some people who add and remove options from qemu often may find that the current script setups are more of a hassle to use. However we can set up scripts using arrays instead this can allow users to comment out arguments from the script without breaking script. This is very useful for prototyping as this allows us to more efficently test and comment out various arguments. This does however pose some issues which will be explained under the `booting a physical install` section.


```bash
#!/bin/bash

## cd image "bliss.iso"
array=(
-enable-kvm
-M q35
-m 6000
-smp 3
-cpu host
-bios /usr/share/ovmf/x64/OVMF.fd
-drive file=android.qcow2,if=virtio
#-cdrom ~/Downloads/bliss.iso
-device virtio-tablet
-device virtio-keyboard
-device qemu-xhci,id=xhci
-machine vmport=off
-device virtio-vga-gl
-display sdl,gl=on
-net nic,model=virtio-net-pci -net user,hostfwd=tcp::4444-:5555
)

qemu-system-x86_64 ${array[@]}
```

## VFIO

You may wish to pass a physical PCIe through to the VM. This can be helpful for people looking to make a gaming setup using bliss, or perhaps more unique setups. Qemu and Crosvm both allow doing this. With libvirt this is unbinding, and binding handled mostly automatically. You still need to load the vfio module yourself (see below).

This article will assume you have prepared IOMMU for passthrough, there are many guides on how to find your IOMMU grouping. If you have a bad grouping it is typically possible to override them using ACS override. Be warned, this is a potential security risk and could be dangerous for host security. ACS will not be elaborated any further in this and it is totally an `At your own risk` solution.

First we need to prepare the device(s) we are going to passthrough, the command to achieve this is below. It requires root being done by root, not simply with root permissions. The easiest way to do this is to use `su -c`. but first we need the pcie function address and the pcie driver. To get this we can use `lspci | grep -i <vendor>` to find the function address. We can then use `lspci -vvnn -s xx:xx.x` to get the pci device driver too. It will be listed under `kernel modules: <pci_device_driver>`. After that we need to append the domain to it, for the majority of users it will be `0000:`. Below is an example command line.


```bash
su -c 'echo 0000:<function_address> > /sys/bus/pci/drivers/<pci_device_driver>/unbind' 
```

After we have unmounted the drive we need to bind to vfio. This is done in a similar manor with the command below, however we need to load the vfio module and use the vid:pid of the device. The VID and PID will be printed in `lspci -nn xx:xx.x` an example output would, in the case below where the vid and pid would be `8086 56a5` be;

```
0d:00.0 VGA compatible controller [0300]: Intel Corporation DG2 [Arc A380] [8086:56a5] (rev 05)
```

Using the above output we would do the below command to bind the DG2 graphics card to VFIO.

If you have multiple GPUs with the same vendor and product IDs, you will need to use same gpu passthrough techniques. Arch wiki's PCI_passthrough page provides a number of possible scripts one can use to make this possible. 

This needs to be done for all the devices in the IOMMU group until all of them are bound to VFIO.

```bash
sudo modprobe vfio
su -c 'echo 8086 56a5 > /sys/bus/pci/drivers/vfio-pci/new_id"' 
```


You can instead add this to module autoload using your init system, this can be done various ways such as mkinitcpio and systemd, an example of a systemd setup would be;

```
/etc/modules-load.d/vfio.conf
~~~~~~~~~~~~~~~~~~~~~~~~~
vfio-pci
```

After the card is loaded into VFIO we can then load it into qemu using the below argument. The second device is because we need to passthrough any additional function devices seperately, this include audio and on some gpus USB controllers.

All devices in the iommu group should to be bound to the VM.

```
-device vfio-pci,host=<function_address>,multifunction=on,x-vga=on \
-device vfio-pci,host=<function_address>
```

It may also be necssary to specify the `pcie-root-port` device.  Below is a small example of this

```
    -device pcie-root-port,id=pcie.1,bus=pcie.0,addr=1c.0,slot=1,chassis=1,multifunction=on \
    -device vfio-pci,host=<function_address>,bus=pcie.1,addr=00.0,x-vga=on,multifunction=on \
    -device vfio-pci,host=<function_address>,bus=pcie.1,addr=00.1
```

It is a good idea to explicitly disable attaching any other displays to qemu by passing the arguments. This may or may not be needed.

```
-nographic -vga none
```

## Booting physical media

Below is an example of booting a physical install where a second drive is mounted to `nvme` and bliss is installed in the folder called `android`. However there is a small issue, due to how arays work under bash, this can complicate how variables and strings work, meaning that sometimes it is better to simply avoid using them in the array, thankfully that does not pose too much issues in the majority of cases.


```bash
#!/bin/bash

install_location=/nvme/android

#common kernel arguments;
#root=/dev/ram0 console=ttyS0 HWC=drm_minigbm GRALLOC=minigbm_arcvm video=800x480 DATA=/dev/vdb quiet DEBUG=2
#'-drive index=0,if=virtio,id=system,file=${install_location}system.efs,format=raw,readonly=on'


args=(
    ##CPU
    '-smp 4'
    '-M q35'
    '-m 4096'
    '-cpu host'
    '-accel kvm'
    ##GPU
    '-device virtio-vga-gl,edid=on'
    '-display sdl,gl=on,show-cursor=true'
    ##devices
    '-usb'
    '-audiodev pa,id=snd0'
    '-device AC97,audiodev=snd0'
    '-device virtio-tablet'
    '-device virtio-keyboard'
    ##net
    '-net nic,model=virtio-net-pci -net user,hostfwd=tcp::4444-:5555'
    ##drives
    '-drive index=0,if=virtio,id=system,file=/nvme/android/system.efs,format=raw,readonly=on'
    '-drive index=0,if=virtio,id=system,file=/nvme/android/data.img,format=raw,readonly=on'
    '-initrd /nvme/android/initrd.img'
    '-kernel /nvme/android/kernel'
    ##misc
    '-monitor stdio'
    #`-serial stdio`
    #'-serial mon:stdio'
)

qemu-system-x86_64 ${args[@]} \
      -append "root=/dev/ram0 console=ttyS0 HWC=drm_minigbm GRALLOC=minigbm_arcvm DATA=/dev/vdb" 
```

## USB passthrough

In qemu, there are three main ways to achieve USB passthrough. `Spice`, `VFIO` and `usb-host`. When using `usb-host` we can passthrough either a `hub` device or a specifc usb device. I have typically had poor experience using hub passthrough in the past so I would reccomend prefering single device passthrough. 

The most simple way to do this is typically the best, simply run `lsusb` and grab the `VID:PID` of the device you wish to passthrough, Then use `-device usb-host` to add it to the VM. 

Instead of simply adding `-usb` we can add specific controllers instead, an example would be.

```
    -device qemu-xhci \
    -device usb-host,vendorid=0xVID,productid=0xPID,id=$SOMETHINGHERE
```

In some cases it might be more suitable to attach the device using it's address;
```
    -usb \
    -device usb-host,hostbus=bus,hostaddr=addr
```
In other cases you can use the below command to passthrough via hostbus and host port, which is useful for passthing through usb hubs. Finding the appropriate hub or device can be done using `lsusb -t`
```
    -usb \
    -device usb-host,hostbus=bus,hostport=port
```

It is always a good idea to append an appropriate ID to the device, in case of a keyboard, simply do `id=keyboard` in the case of a thumbdrive, you can do `id=usb-drive`. This is largely preference.

For using spice, 

It will be explained in more depth below, however in the spice client you will need to enable usb-passthrough and select the appropriate device there.

For VFIO,

simply pass the appropriate USB controller through to the VM using vfio above. Using `lsusb -t` to locate the controller and `lspci` can be useful for determing what devices to passthrough.

USB devices can be hotplugged using the Qemu Human Monitor explained below.

## Low latency Pipewire/Jack audio

Using qemu, we can instead of using pulse audio backend, use jack. this is very useful since we can use jack for higher quality and lower latency audio, as well as a highly configurable two way audio. this is great because Bliss and other android x86 operating systems have a quite high base latency. 

using jack you can manage to cut a round trip from 240ms to 200ms. it's important to remeber that this is a <strong>Round Trip</strong> numbers, meaning the time it takes from audio to go from the host to the virtualized microphone, into android, to the speakers, and back to the host. this is not the android -> host latency, currently this is not something tested. however it could be anywhere from 1/3 of this to 2/3 of this. This will assume using pipewire since it is the easiest and most convient option.

using `jack_lsp` to list ports we can connect to the ports I am interested are 

```
speakers 

Family 17h (Models 00h-0fh) HD Audio Controller Analog Stereo:playback_FL
Family 17h (Models 00h-0fh) HD Audio Controller Analog Stereo:playback_FR

microphone 
Realtek Audio USB Analog Stereo:capture_FL
Realtek Audio USB Analog Stereo:capture_FR
```
qemu uses regex to match, so here we want to choose a name that will match the devices we want, but not the extras
```
  -audiodev jack,id=jack0,out.connect-ports=Family,in.connect-ports=USB \
  -device ich9-intel-hda -device hda-duplex,audiodev=jack0
```
If you remove the `out.connect-ports=Family,in.connect-ports=USB` part of the command line, it will create an output not connected to anything. in this case you could either manually connect ports using helvum or pipewire direcly, or you could setup a wireplumber profile to handle automatic connections. however this is far out of scope


we can also do a bit more tunning by setting `PIPEWIRE_LATENCY` when we do this, we can actually ignore the output qemu tells us when it starts as if we check pw-top we can see that it is indeed running when we check pw-top

```
export PIPEWIRE_LATENCY=128/48000
```

However jack is not the only options we have, we also have Alsa output. this is about as direct as you can get automatically outputs to headphones and microphone properly, and is still controlled via pipewire. however audio quality of it can be greatly dependant on the host PC. so while not reccomended you can try it by using `-audiodev alsa,id=alsa0` instead of `-audiodev jack,id=jack0...`

Testing `SDL` audio output is fine, but is higher latency then `Jack` so it would be typically not reccomended.

As for the emulated audio devices, AC97 is the lowest latency, but may suffer in audio quality. There are multiple other audio devices however at this time I do not have a method for testing latency in them. The reccomendation for low latency audio is `AC97` but for general use it is `-device ich9-intel-hda -device hda-duplex`.

This open the doors to advanced audio manipluation and high quality audio from android. this can be useful for gaming as well as game recording since you could connect to a virtual device which could then be connected to both headphones and OBS. 

This also opens the way for high fidelity audio though it is currently not reccomended to go above 48000 hz on bliss at this time due to potential audio distorting. 

## EGL-headless for remote VMs
Egl headless is useful for remote VMs. A remote VM being a virtual machine hosted on a seperate machine that the user is connecting from. Much like `-display sdl,gl=on` or `-display spice,gl=on` this provides 3d acceleration. However unlike the two previous, this does not open a window on the server. This means you can use spice-app to connect to the VM over a network. It may also have lower preformance then the alternative.

You can use `rendernode=` to specify the gpu, this can be helpful to select a secondary gpu, or perhapps if you have many VMs running at the same time to spread the load across multiple GPUs.

```
-display egl-headless,rendernode=/dev/dri/renderD128
```
Note: you can not at the current time use multiplke `-display` arguments, so you will need either spice server, or some other remote graphics solution. (IE. scrcpy)

## Spice

Spice is a method of interacting with VMs it can work both locally and over a network. It supports realtively advanced features such as usb redirection, video compression, and more. There are two ways to add spice to the VM, one for local machine, and one for remote machine. below is an example of a local machine.

```
    -device virtio-vga-gl -display spice-app,gl=on -device ac97 \
    -device virtio-serial -chardev spicevmc,id=vdagent,debug=0,name=vdagent \
    -device virtserialport,chardev=vdagent,name=com.redhat.spice.0 \
```

And below this is an example of a remote machine. It's important to remeber to setup firewall and/or portforwarding to allow remote connections.

```bash
    -spice port=3001,disable-ticketing -device ac97 -device virtio-vga-gl -display egl-headless \
    -device virtio-serial -chardev spicevmc,id=vdagent,debug=0,name=vdagent \
    -device virtserialport,chardev=vdagent,name=com.redhat.spice.0 \
```

To setup usb redirection you can copy the below block into your config. For each device you want to be able to redirect, you need to add a `chardev` device and a `usb-redir` slot. These will let you dynamically change what devices you want to passthrough.

```bash
#redirect up to 3 devices
    -chardev spicevmc,name=usbredir,id=usbredirchardev1 \
    -device usb-redir,chardev=usbredirchardev1,id=usbredirdev1 \
    -chardev spicevmc,name=usbredir,id=usbredirchardev2 \
    -device usb-redir,chardev=usbredirchardev2,id=usbredirdev2 \
    -chardev spicevmc,name=usbredir,id=usbredirchardev3 \
    -device usb-redir,chardev=usbredirchardev3,id=usbredirdev3
```
https://www.spice-space.org/spice-user-manual.html

## Human Monitor

Instead of connecting tty to the terminal, we can also connect Qemu Human Monitor to the terminal. This is an interface that allows us to send commands to a running VM in order to interact with it. We can send shutdown and reset signals, add and remove usb and storage devices. As well as more advanced features like resize disks, dump the framebuffer (a sort of over glorified screenshot) and create VM snapshots. It is a very powerful tool and only some of the more common uses will be outlined here. 

First thing is to add it to the VM. While with bliss it might be common to use `-serial stdio`, that will conflict with using qemu human monitor, what we instead want to use is `-monitor stdio`. If you need both, we can use `-serial mon:stdio` which will multiplex the serial connection and the qemu human monitor. You can swap between the two using `CTRL + a` then press `c`. That will swap between the serial and the human monitor. 

When using spice, the human monitor will already be attached to it, so do not add commands to attach human monitor to qemu itself.

The other option you have is to connect either the serial connection or the monitor connection to a socket, then interfacing with it over said socket. However most people will likely not use this route. It is therefore reccomended to simply use;

```
-serial mon:stdio
```
However a short list of examples alternatives are below, there are more options.

```bash
-monitor tcp:127.0.0.1:55555,server,nowait #tcp session
-monitor telnet:127.0.0.1:55555,server,nowait #telnet session
-monitor unix:qemu-monitor-socket,server,nowait #unix socket
```

Now that we have the human monitor attached to the VM, we can get to some of the command we can do with it. Below will be some commands with a brief writeup, to learn more about how to use the command read the link below;

```bash
system_reset #reboot the system
system_powerdown #powerdown the system
sendkey # sendkey ctrl-alt-f1 #send specific keys to the VM

quit # quit the VM
stop # pause the VM # may cause bugs on resume
cont # continue the VM
system_wakeup # wake VM

device_add #usb-host,hostbus=2,hostport=1.2.2,id=idofyourdevice # Add device
device_del #<idofyourdevice> #delete device
drive_add # add pci drive 
netdev_add # add nic
change #ide1-cd0 /path/to/some.iso #change device config

info #subcommand (snapshot to view snapshots) #print info about device 
savevm #creates a snapshot live, while the VM is still running
loadvm #loads a snapshot while the VM is still running

mouse_move #move the mouse
screendump #screenshot.ppm # dump screen into ppm image
```

https://qemu-project.gitlab.io/qemu/system/monitor.html


## Disk Optimization

There is a LOT we can do for disk optimization! This section will be as condensed as possible while still giving you a good idea at what these features are doing. Because of this do not expect this section to be as clear as the other sections.Because there is a lot we need to cover here.

NOTE: If you want to run many VMs off of a single drive, or an array, your considerations may be very different and some of these will not apply for you! this section is written based on the assumption that one, or maybe a couple VMs are being run on the computer.

### Qcow2 vs raw image

When it comes to whether or not you should use qcow2 vs raw image, this really comes down to what you as a user want out of the images. First talking about preformance. Raw vs Qcow2, under normal circumstances, should typically have similar preformance. however, Qcow2 can certainly be slow under the right (or perhaps better put wrong), circumstances.

If you want a simple answer to the question "Which format will give me the best preformance?" the answer is raw image. If you don't want any advanced features or configuration like compression or snapshots, you can skip the rest of the disk section.

### Preallocation 

Preallocation is put simply, how the VM decides to assign storage blocks when no data is actually inside of them. with `preallocation=none` no data is pre assigned. this means that the disk image is very small, and will grow as more data gets written to it. when you choose `metadata` it pre allocates space for metadata, this means an empty VM disk will take up more space then one with `none`.

In most cases, the user will probably just want metadata. as it gives the best preformance to space and features. falloc and full preallocation will cause the VM to use up more space. however in accordance with the chart below, preformance goes up when you go down the chain. But you do start to loose some features, as `full` will cause snapshots to no longer work.

```
none 
metadata
falloc
full
```

### Compression
Using compression to save space on your image can be a very valuble tool. while there are multiple ways to do this, but the focus of this will be on using qemu-img's qcow2 compression, you can simply run `qemu-img create -f qcow2 -o compression_type=zstd disk.qcow2 10G` and this will create a zstd compressed disk image for you to use. 

### Cache Modes

Qemu supports a variety of disk write `cache` modes. these cache modes can be changed to tweak I/O speed below are the ones qemu support, see the below link for an explanation on these. Using none will essentially be the best preformance option.

```
writethrough
writeback
none
directsync
unsafe
```
https://documentation.suse.com/sles/12-SP4/html/SLES-all/cha-cachemodes.html

### Custom cache size
With qemu we can set both `cache` size and `l2 cache` size

`...disk.qcow2,cache-size=16M`

`...disk.qcow2,l2-cache-size=4M` where `1M` is good for `8Gb` of random R/W. `Areasize / (clustersize / 8)` where default cluster size is `64kb`. and area size is the total ammount of space you want cache to cover. `1/8th` of `64kb` is a nice `8kb`.

In this example we can say we want `16gb` of r/w headroom, we do `16gb / 8kb`. An easier way to calculate this by hand would be to scientifc notation. this would be `32x10^9 / 8x10^3 = 4x10^6 = 4mb`

```
1KB = 10^3
1MB = 10^6
1GB = 10^9
1PB = 10^12
etc
```

Try increasing cluster size instead `qemu-img create -f qcow2 -o cluster_size=2M`.

*Try mixing `2m cluster` and `metadata` for a good blend of preformance!*


### Backups and snapshtos
utilizing snapshots is a key feature in doing development, as well as making sure that any downtime is minimal. This is particularly useful for preventing a corruption inside of the machine from causing a re-install. 

There are two main ways of doing this, you can create a full backup of the drive. this is as simple as doing rclone. This does however waste a lot of space. What we can do instead is use qemu-snapshot. 

Qemu-snapshot redirectes the writes to a new image, so that you can save space. say you have `disk.qcow2` you can run the command `qemu-img create -f qcow2 -b disk.qcow2 disk-snap1.qcow2` and that will create a new image. Now you modify qemu to use `disk-snap1.qcow2` it will read from `disk.qcow2` but will never write to it. All modifications made are made in `disk-snap1.qcow2`. and to revert the snapshot, you can simply delete the `disk-snap1.qcow2`.

Another use of this is to do `qemu-img create -f qcow2 -b base.qcow2 vm1.qcow2` && `qemu-img create -f qcow2 -b base.qcow2 vm2.qcow2`. by making two seperate snapshots, we can now run mutliple VMs each with their own unique writable disk, that is based on the `base.qcow2`. this is great for running multiple VMs without needing to waste space and setup time. **REMEBER** writing to the base disk will corrupt the snapshots. It's a wise idea to change the permissions of the base disk to read only, this prevents user error. Even prefoessionals with many hours can slip up and accidentally make an error. Remeber to practice safe VM handling.

The above paragraph is about creating external snapshots which is great for when the VM is off, and should be done when you wish to create a new VM. However qemu also supports internal snapshots and live snapshots. The snapshots work internalling in the single qcow2 (or qed) image. It may not be as flexible, however it is bother faster, and for many people more convient and nice to use internal snapshots.

Using the human monitor above it is shown the commands `savevm` `loadvm` `info snapshots` these can be used to save, load and view the current snapshots availible. live snapshots save the entire state of the `cpu`, `devices`, `Ram`, as well as the entire disk contents, so the live snapshots can be a good amount larger. It is very akin to using an emulator "save state". 

Removable devices can sometimes cause issues, so it's prefered to disconnect any before taking a snapshot and before loading one. However as long as all the devices are there when the snapshot is made and loaded it will be fine.

### full device passthrough

For passing storage devices to qemu there are multiple ways of doing so. To pass a sata controller, the most typicall way of doing this will be via pci passthrough. you can accomplish this with the `vfio` instructions listed previously.

As for `Nvme` devices, you can actually do something similar, you can use vfio, however a better way of doing it would be to actually use qemu's built in functionality for this listed below

```
qemu-system-x86_64 -drive file=nvme://HOST:BUS:SLOT.FUNC/NAMESPACE
```

You can also passthrough `raw block devices` or better known as partition passthrough. you can pass it through a lot like you could with a standard file, an example is given below

```
    -drive file=/dev/sdb2,if=none,id=drive-virtio-disk0,format=raw \
    -device virtio-blk-pci,scsi=off,drive=drive-virtio-disk0,id=virtio-disk0
```

### IoThread

IO threads can help both raw and qcow2 when using virtio drives

```
-object iothread,id=iothread0
-device virtio-blk-pci,iothread=iothread0,id=...
-device virtio-scsi-pci,iothread=iothread0,id=...
```

### EroFS to SFS and the other way around.

Bliss Is currently shipping with `SquashFS` or `EroFS`. `EroFS` is has the best raw read speed of the supported read only filesystems. however it has a significant compromise in being the compression. `EroFS` only supports `LZMA` and `LZ4` compression. `LZMA` is quite slow, and LZ4 doesn't offer great compression ratios.

So there are two reasons why you would want to recompress `system.img` using `SquashFS` or `EroFS`. The first one being wanting to simply shrink the the size of system.img.

if you have an `eroFS` image and want to compress it further, you may want to convert it to an `SquashFS` image. Like wise, if you have an `SFS` image, you might be better suited to convert it to `EroFS` if you want better performance if you are willing to sacrafice a bit of storage space. 

```bash
sudo modprobe erofs # Many distros will ship EroFS but it won't be enabled by default
sudo mount -o loop system.efs /mnt 
mksquashfs /mnt system.sfs -comp zstd # you can choose from a couple different formats but ZSTD is the main benefit here
sudo umount /mnt
rm system.efs
```

If you wanted to keep using `EroFS` but use the slower but best in class compression LZMA, you could use the below sample.

```bash
sudo modprobe erofs
mv system.efs system-old.efs
sudo mount -o loop system-old.efs
mkfs.erofs -c xz system.efs /mnt
sudo umount /mnt
rm system-old.efs
```

### Q&A about qemu's drives
```
Q: `virtio-blk` vs `virtio-scsi`
A: `Virtio-blk` is usually faster across the board, however is limited in the ammount of drives, probably not an issue for most
```
```
Q: what is the best `Cache` mode to use?
A: you typically want `cache=none`
```
```
Q: My VM has terrible write speeds, and it is even effecting my host
A: check if you are using a COW fs. if sodisable COW on btrfs and other COW systems for the VM
```
```
Q: `lazy_refcounts` Can cause corruption! but is it worth it?
A: Typically yes. preformance can increase by a good amount at the trade off of accidental power off. but disable it if you change the `cache` mode to `none`.
```
https://events19.lfasiallc.com/wp-content/uploads/2017/11/Storage-Performance-Tuning-for-FAST-Virtual-Machines_Fam-Zheng.pdf


## Finishing Notes

Handling multiple VMs efficently can be a complicated topic, and is largely out of scope for this guide. This bit is just a few tips to make running more then a couple VMs a better experience. this would be a stopgap between a more professional solution such as `proxmox` or a `libvirt`. This is just a rough guideline, and may not apply across the board or when you scale up.

When installing `Bliss`, It might be a good idea to break up the Disks, you only need one `system` disks for the VMs if it's read only. This can save many gigabytes of data storage, and increase storage performance across the storage of any potentially effected drives.

Another good idea is to consider what VMs need persistant memory and which ones don't. If you are running many VMs at once, whether it be for a cloud solution or a more tailored setup, you may find that you don't want many, or even any of the VMs to retain any modifications to them.

It is **highly** reccomend reading the free `redhat` docs on setting up and tuning VMs if you need more then what this guide offers. It will be a lot of reading. It will however be for more valuble then most resources you can freely find on the internet. It should be the first stop for anyone wanting to actually learn more and implement these kinds of setups for yourself.

The next freely accessible resource is the `oVirt` forums, documentation, and most importantly Mailing list. `oVirt` is an enterprise grade solution built upon libivrt so many of the solutions and concepts will directly apply when using libvirt, and can be used to gain a futher understanding of VM infranstructure in general.

https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/virtualization_tuning_and_optimization_guide/index

