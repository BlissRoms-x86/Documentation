# Install from Windows - UEFI/ESP \(64-bit\)

### Before you begin

**You must turn off Secure Boot from your BIOS if you have it enabled. Otherwise it won't boot.**
**There is no need to format or partition the disk unless you want to. Installation along with windows won't wipe the drive and works fine**

For 32bit or MBR/Legacy install, Axon from Supreme-Gamers put together a great installer that works with MBR or EFI and installs on NTFS or EXT4. Check it out here: [https://supreme-gamers.com/t/advanced-android-x86-installer-for-windows.300/](https://supreme-gamers.com/t/advanced-android-x86-installer-for-windows.300/)

## Windows-based installer - UEFI/ESP \(64-bit\)

**Proceed at your own risk.** This method might be the easiest currently if you understand what you are doing.

### Part 1 - Downloading and installing the Grub2Win Bootloader.

Download the file from here - https://sourceforge.net/projects/grub2win/ and proceed to install it. 
This bootloader helps use to boot the OS. It is recommended that bootloader be installed in the default directory (C:). 

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

<img width="572" alt="1" src="https://user-images.githubusercontent.com/19780503/225529366-8cebb14f-bc1b-4e40-8221-b2ed18f3f5ee.png">

<img width="593" alt="2" src="https://user-images.githubusercontent.com/19780503/225529384-39102d34-c6c8-4687-9a3b-70071eaffb88.png">

<img width="575" alt="3" src="https://user-images.githubusercontent.com/19780503/225529385-20862b50-8fc4-4205-907e-343ee03d0874.png">

Reboot and select android and enjoy your BlissOS install.

By Default, the AndroidOS wouold be the last option and if it nothing is selected it will boot Windows.

*This is for the installation in the C: drive.

**Troubleshooting:**

For most of the issues, changing the boot flag options is answer. You can temperoraly edit the boot flags by press "E" when AndroidOS is highlighted.


<img width="572" alt="1" src="https://user-images.githubusercontent.com/19780503/225529366-8cebb14f-bc1b-4e40-8221-b2ed18f3f5ee.png">
<img width="593" alt="2" src="https://user-images.githubusercontent.com/19780503/225529384-39102d34-c6c8-4687-9a3b-70071eaffb88.png">
<img width="575" alt="3" src="https://user-images.githubusercontent.com/19780503/225529385-20862b50-8fc4-4205-907e-343ee03d0874.png">

Updated by Bilawal(https://github.com/FrozenBrick)
