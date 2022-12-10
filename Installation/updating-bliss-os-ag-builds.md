# Updating Bliss OS/AG builds

Somethmes you might want to update your Bliss OS/AG install when a new release is pushed out. Doing this is simple, as long as you used our documented methods to install. 

**Bootable USB Method:**

This process is exactly the same as when you installed, except now it will ask if you want to update and existing install instead. 

**Windows Installer Method:**

Use 7-Zip to open the new .iso, and simply drag the kernel, initrd.img \(and ramdisk.img if on Pie\) to the install folder, overwriting the old ones, then in 7-Zip, double click on the system.sfs file and drag the system.img inside over to replace your existing system.img. If you only have a system.sfs file, replace that with the system.img, and delete the old .sfs file. Then reboot and boot into your newly updated release

