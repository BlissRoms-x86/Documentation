# Install on Mac OS

## **Triple boot macOS, Windows and Bliss OS**

\(Thanks to @lconstantin for this guide\)

_**Alternative:**_ _It should be possible to skip the Windows installation. Read at the end._

1. BACKUP ALL DATA with TimeMachine

2. Disable FileVault \(disk encryption in macOS\). This can take a long time \(hours\) if you already have it turned on and sometimes it can result in a locked state where the filesystem is not fully decrypted and the option to turn FileVault on or off is greyed out. Recovering from this state might require wiping the entire SSD from macOS recovery. This happened to me and I couldn't find a solution. Apparently it's a fairly common problem as I found many reports online.

Note: I have not tried this multiboot process with FileVault enabled because I read FileVault does not play nicely with multiboot so I just decided to disable FileVault. I don't know if it might actually work with FileVault on, but probably not.

3. Download the Windows 10 installation ISO from Microsoft's website. It’s freely available.

4. Launch Boot Camp Assistant and follow the instructions to install Windows using the downloaded ISO. The process will allow you to resize the macOS partition and will create the Windows partition called BOOTCAMP for you. When you choose the size of the BOOTCAMP partition please account for the extra space you will need for Bliss OS including its data. I wanted 32GB for the Bliss OS data image so I accounted for 50GB extra for BlissOS when choosing the size of the BOOTCAMP partition.

Note: Once Boot Camp is done installing Windows you will be able to boot into it and select it as boot option when the Mac starts up. At this point the system will still use Apple's bootloader, which by design is capable of booting Windows too.

5. Boot into Windows and download a\) the latest version of Bliss OS, b\) [the latest version of the rEFInd boot manager](https://www.rodsbooks.com/refind/getting.html) and c\) [Rufus](https://rufus.ie/en/), a program for creating bootable USB sticks.

6. Install Rufus and create a bootable stick for Bliss OS using the ISO you downloaded earlier.

7. Unpack the rEFInd zip and copy its directory to the Bliss OS bootable stick. DON’T SKIP.  


8. OPTIONAL: You have the option to either install Bliss OS on the same partition as Windows or you can resize the Windows partition using the Disk Manager in Windows and create a new partition for Bliss OS. I chose to resize and create a new partition of 50GB for Bliss OS and I formatted this new partition as NTFS.

9. Reboot the Mac and keep COMMAND + R pressed during the booting process. This will boot into macOS recovery. Go to Applications &gt; Utilities in the top bar and choose Terminal. This will open a terminal shell. Type:

`cd /  
cd Volumes  
cd BLISS-xx (the name of the USB stick)  
cd refind-xx (the name of the refind directory on the stick)  
./refind-install`

This will launch the rEFInd installation script and will install the boot manager.

10. Reboot and now you will see the rEFInd boot screen with multiple options for booting into macOS and Windows that will be automatically detected. Note: For macOS Big Sur and later the option that works is the one that says "Boot macOS from Preboot." You can hide the other option by selecting it and pressing the - \(minus\) key. For Windows it’s the Boot with EFI option. You can hide the rest. You can test that booting into both systems works at this stage before continuing.

11. rEFInd will also provide you with an option to boot into the Bliss OS stick if it’s plugged in. This will launch the GRUB bootloader that comes with Bliss OS and will show you multiple options: to start Bliss OS Live which can be used to test if Bliss works on your system or to install the OS to disk.

12. Choose the install option and go through the normal process \([detailed here](https://docs.blissos.org/install-bliss-os/install-from-bootable-usb)\). Select either the Windows partition or the new partition you created for Bliss \(my case\). Do not reformat. Choose YES when asked to install GRUB \(this will replace rEFInd temporarily\). Create the BlissOS data image with the size you want.

**Notes:** If you created a separate NTFS partition, you could choose to format it as ext3/4 during the Bliss OS installation. If you choose this option the installer will no longer ask you to create a data image.

Also, because you chose to install GRUB, rEFInd will get replaced with GRUB as the default boot manager on the EFI partition at this stage. You can choose not to install GRUB during installation and manually create a boot entry for Bliss in rEFInd which will not be explained in this guide.

However, note that rEFInd does not install an NTFS driver by default and does not install an ext3/4 driver either unless a Linux installation is present on the disk when rEFInd is installed. There are ways to fix this and include drivers \(see rEFInd documentation on drivers\), but it's easier to just use rEFInd to start GRUB which will boot Bliss regardless of whether it's on an NTFS or ext4 partition.

13. At this stage if you reboot your system you will only see GRUB and you will only be able to boot into the Bliss OS installation. You need to reinstall rEFInd to restore booting into macOS and Windows. \(In reality rEFInd is still there on the EFI partition, but not the default option anymore\). Luckily you still have the rEFInd directory on your BlissOS bootable stick, so repeat the instructions from step 9 again to boot into macOS recovery, open terminal, navigate to the USB stick and run the ./refind-install script again. This will restore rEFInd as the default boot manager with all the options it had \(the previous configuration doesn't get replaced.\)

14. Remove the USB stick and reboot your system. You will now see the rEFInd screen again with options to boot into macOS, Windows and multiple options for BlissOS, which will be detected with a Linux icon. Choose the option that says Boot with /EFI/Android/grubx64 and you can hide the rest. This will launch the familiar GRUB screen with all the normal BlissOS boot options.

**Alternative:** It should be possible to skip the Windows installation. You can boot into macOS recovery and use the Disk Utility from there to resize the macOS partition and create a new partition for BlissOS formatted as FAT32 \(macOS does not support many filesystems natively\). You can then create the BlissOS bootable stick from macOS using UNetbootin instead of Rufus and also make sure you download rEFInd, unpack it and copy its directory to the stick. Then boot into recovery again, install rEFInd, boot into rEFInd to start the BlissOS installation. You can reformat the FAT32 partition as NTFS during the BlissOS installation and then follow all the steps. Install Grub. Go into recovery again, install rEFInd again and you're done.

