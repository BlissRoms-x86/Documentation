# Advanced Installation

This is only for **advanced users**. For regular users, please visit our main [Installation Guide found here.](installation-guide.md)

## Custom Install - Bliss OS 8.x/10.x/11.x UEFI/ESP \(64-bit\)

Just as a reminder, Team Bliss is **NOT** responsible for any damage caused by this guide. By continuing, you automatically agree to these terms.

### Part 1 - Mounting Your UEFI/ESP Partition

You will want to make sure you can view hidden and system files in Explorer options. Once you do that, hit the start menu, and type in `cmd`. Once "Command Prompt" shows up, right click on it and choose "Open as administrator".

#### `cmd` is not showing up, what should I do?

Press the Windows key and the R key to bring up the "Run..." dialog. Type in `cmd`, and then press Ctrl-Shift-Enter. Press "Yes" on the UAC popup.

Run the following:

```text
mountvol X: /S
```

Then check to see if it is mounted already. Run "Task Manager" by either

* Pressing Ctrl-Alt-Del and then clicking on "Task Manager", or
* Pressing Ctrl-Shift-Esc

Click on "File", "Run new task", "Browse", "This computer", and SYSTEM \(X or type in `X:` in the filepath bar. If you cannot access `X:`, then that could mean one of three things.

* You have an ESP setup \(follow the installation method below\)
* You have a legacy MBR setup
* Your setup has a custom boot sequence

### Part 1 \(alternate\) - ESP setup

Windows 10 sometimes has an EFI partition already mounted under drive letter `Z:`, hidden. A very quick and easy way to access the ESP \(EFI System Partition\) in Windows 10 without using the command line is to start "Task Manager" \(check above if you forgot the steps\), and then click on "File", "Run new task", "Browse", "This computer", and SYSTEM \(Z or type in `Z:` in the filepath bar\).

Now go to `boot/grub/grub.cfg` and edit it accordingly with Notepad++ or another text editor. Save the file and your're ready to go!

### Part 1 \(alternate\) - Killing the `explorer.exe`

Run `cmd` as admin and enter the following command:

```text
taskkill /im explorer.exe /f
```

This will kill the `explorer.exe` process - don't be surprised if it shows a warning. This step is sometimes required, because by default `explorer.exe` is ran by the currently logged in user, and it has to be run by the "Administrator" in order to view the mounted system drive. **The "Administrator" account is not the same as an account with administrative privileges.**

```text
mountvol X: /s
```

This will mount the system partition that usually consists of UEFI related files. `X:` is the letter of the drive - you can use whatever letter you want, but it has to be free for assignment. Then type:

```text
explorer
```

This will run `explorer` as "Administrator" and will allow you to browse the mounted system partition.

The above may not work for all devices, as some handle UEFI differently.

## Part 2 - UEFI installation

Let's start by downloading the required files. [Here is a customized UEFI boot for 32/64-bit machines.](https://www.androidfilehost.com/?w=files&flid=143191)

Please note that if you came from our Nougat builds to our Bliss OS 8.x builds, you will have to edit the `grub.cfg`.

If you are using Bliss OS 8.x/10.x, please use the `grub` entry below as a guide:

```text
menuentry 'Bliss-x86' --class android {
    search --file --no-floppy --set=root /AndroidOS/system.sfs
    linux /AndroidOS/kernel root=/dev/ram0 SRC=/AndroidOS androidboot.selinux=permissive androidboot.hardware=android_x86_64 quiet DATA=
    initrd /AndroidOS/initrd.img
}
```

If you are installing on `ext3`/`ext4`, due to a bug in the install you will have to use the following `grub` entry setup:

```text
menuentry 'Bliss-x86' --class android {
    search --file --no-floppy --set=root /AndroidOS/system.sfs
    linux /AndroidOS/kernel root=/dev/ram0 SRC=/AndroidOS  androidboot.selinux=permissive androidboot.hardware=android_x86_64 quiet DATA=
    initrd /AndroidOS/initrd.img
}
```

Now that we have the partition mounted, we can copy that `BOOT` directory to your UEFI partition using `explorer` as the Administrator or by using the "New Task" dialog from Task Manager. \(See above if you forgot the steps!\) Once it is copied, go back to the Administrator `cmd` prompt and type:

```text
mountvol X: /D
```

or if you used `Z:`, type:

```text
mountvol Z: /D
```

This will dismount the UEFI/ESP volume for safe reboot. We then suggest you use EasyUEFI here to create the UEFI boot entry. Open the app, and create a new entry. Select your UEFI partition, and in the "File" Path, click "Browse" and use the file manager window to browse to your `BOOT/grub/grubx64.efi` file. Click OK, and then choose the new `grub` entry and move it to the top. Make sure Secure Boot is turned off or else it likely will just boot back to Windows.

### Part 4 - The Manual Blissification of Your PC

To do a manual "Wubi like" install of Bliss OS after you install the UEFI entry, you will need to open the Bliss OS `.iso`/`.img` with 7zip, and then drag all the `.img` & `.sfs` files to `C:/android-x86` or whatever your target drive is \(make sure your `grub` entries match where you are putting these\). Then create your `data.img`. We suggest using a tool like RMXtools \(use version 1.7\) from XDA to create it. Check the tool's thread for detailed instructions. You will want to create your `data.img` inside that `android-x86` folder.

You can now reboot if you have installed the custom UEFI entry right and selected it using EasyUEFI. You should boot right to the Android-x86 `grub` theme. There, you can use up and down to select, and return to boot that entry. You can also hit `e` to edit the selected entry. You will want to pay attention to which entry you select, since there will be one for `Bliss-x86(32bit)` and one or `Bliss-x86_64(64bit)`.

