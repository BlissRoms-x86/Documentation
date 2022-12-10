# Run from Docker

We have gained support for running our images from Docker thanks to @sickcodes and his Dock-Droid project.   
  
Our stable Android 9 based images are default, but you can load up a specific image as well \(helpful for testing in a development environment\)

#### Exerpt below taken from: [https://github.com/sickcodes/dock-droid](https://github.com/sickcodes/dock-droid)

## Initial setup

Before you do anything else, you will need to turn on hardware virtualization in your BIOS. Precisely how will depend on your particular machine \(and BIOS\), but it should be straightforward.

Then, you'll need QEMU and some other dependencies on your host:

```text
# ARCH
sudo pacman -S qemu libvirt dnsmasq virt-manager bridge-utils flex bison iptables-nft edk2-ovmf

# UBUNTU DEBIAN
sudo apt install qemu qemu-kvm libvirt-clients libvirt-daemon-system bridge-utils virt-manager

# CENTOS RHEL FEDORA
sudo yum install libvirt qemu-kvm
```

Then, enable libvirt and load the KVM kernel module:

```text
sudo systemctl enable --now libvirtd
sudo systemctl enable --now virtlogd

echo 1 | sudo tee /sys/module/kvm/parameters/ignore_msrs

sudo modprobe kvm
```

## Quick Start

### Run Bliss OS 11.x Image [![https://img.shields.io/docker/image-size/sickcodes/dock-droid/latest?label=sickcodes%2Fdock-droid%3Alatest](https://camo.githubusercontent.com/efcc83e99f50dcdb78a8e0dd46e66ebb79aeb3d7a2539c9367e7be7c6fdd96b8/68747470733a2f2f696d672e736869656c64732e696f2f646f636b65722f696d6167652d73697a652f7369636b636f6465732f646f636b2d64726f69642f6c61746573743f6c6162656c3d7369636b636f646573253246646f636b2d64726f69642533416c6174657374)](https://hub.docker.com/r/sickcodes/dock-droid/tags?page=1&ordering=last_updated)

```text
docker run -it \
    --device /dev/kvm \
    -v /tmp/.X11-unix:/tmp/.X11-unix \
    -e "DISPLAY=${DISPLAY:-:0.0}" \
    -p 5555:5555 \
    sickcodes/dock-droid:latest
```

Increase RAM by adding this line: `-e RAM=4 \`

Want to use your WebCam and Audio too?

`v4l2-ctl --list-devices`

`lsusb` to get the `hostbus` and `hostaddr`

```text
Bus 003 Device 003: ID 13d3:56a2 IMC Networks USB2.0 HD UVC WebCam
```

Would be `-device usb-host,hostbus=3,hostaddr=3`

```text
docker run -it \
    --privileged \
    --device /dev/kvm \
    -v /tmp/.X11-unix:/tmp/.X11-unix \
    -e "DISPLAY=${DISPLAY:-:0.0}" \
    -p 5555:5555 \
    -p 50922:10022 \
    --device /dev/video0 \
    -e EXTRA='-device usb-host,hostbus=3,hostaddr=3' \
    --device /dev/snd \
    sickcodes/dock-droid:latest
```

Want to use SwiftShader acceleration?

```text
docker run -it \
    --privileged \
    --device /dev/kvm \
    -v /tmp/.X11-unix:/tmp/.X11-unix \
    -e "DISPLAY=${DISPLAY:-:0.0}" \
    -p 5555:5555 \
    -p 50922:10022 \
    --device=/dev/dri \
    --group-add video \
    -e EXTRA='-display sdl,gl=on' \
    sickcodes/dock-droid:latest
```

In development by BlissOS team: mesa graphics card + OpenGL3.2.

#### Use your own image/naked version

```text
# get container name from 
docker ps -a

# copy out the image
docker cp container_name:/home/arch/dock-droid/android.qcow2 .
```

Use any generic ISO or use your own Android AOSP raw image or qcow2

Where, `"${PWD}/disk.qcow2"` is your image in the host system.

```text
docker run -it \
    -v "${PWD}/android.qcow2:/home/arch/dock-droid/android.qcow2" \
    --privileged \
    --device /dev/kvm \
    --device /dev/video0 \
    -v /tmp/.X11-unix:/tmp/.X11-unix \
    -e "DISPLAY=${DISPLAY:-:0.0}" \
    -p 5555:5555 \
    -p 50922:10022 \
    -e EXTRA='-device usb-host,hostbus=3,hostaddr=3' \
    sickcodes/dock-droid:latest
```

#### Custom Build

```text
CDROM_IMAGE_URL='https://sourceforge.net/projects/blissos-x86/files/Official/bleeding_edge/Generic%20builds%20-%20Pie/11.13/Bliss-v11.13--OFFICIAL-20201113-1525_x86_64_k-k4.19.122-ax86-ga-rmi_m-20.1.0-llvm90_dgc-t3_gms_intelhd.iso'

docker build \
    -t dock-droid-custom \
    -e CDROM_IMAGE_URL="${CDROM_IMAGE_URL}" .
```

#### How to connect using ADB

In the Android terminal emulator:

Edit `/default.prop`

Change `ro.adb.secure=1` to `ro.adb.secure=0`

E.g.

```text
su
sed -i -e 's/ro\.adb\.secure\=1/ro\.adb\.secure\=0/' /default.prop
```

In the Android terminal emulator, run `adbd`

Then from the host, you can can connect using either: `adb connect localhost:5555`

`adb connect 172.17.0.2:5555`

For further information, and even information. on a dockerized build environment for Bliss OS, please visit: [https://github.com/sickcodes/dock-droid](https://github.com/sickcodes/dock-droid)

