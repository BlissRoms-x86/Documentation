# Wifi Issues

For many users, wifi is not detected right on boot. If you are using a build that is pre Kernel-5.x, you can try and add the grub option "AUTO\_LOAD=old". If you are using a newer kernel \(5.0+\), make sure your device has native support included in the kernel. Some devices may also require the grub option "VULKAN=1" or "GRALLOC=gbm" too.

If it's not working, you can provide your wifi card info to us for help, or you can submit a pull request to our kernel-drivers repo with the proper Linux driver modules for your device \( [https://github.com/BlissRoms-x86/external\_kernel-drivers](https://github.com/BlissRoms-x86/external_kernel-drivers) \). If you don't know the card name you can try to provide lspci or lsusb \(for usb cards\) output for us by typing the command into the Terminal \(with root\)

`su  
lsusb > sdcard/lsusb.txt`

