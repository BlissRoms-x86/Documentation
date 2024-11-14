---
layout: post
title: 'Bliss OS: How to install Bliss OS on Proxmox'
date: '2024-04-30 20:52 +0800'
---

# Install in Proxmox

Installing in Proxmox as a Virtual Machine is rather straight forward, and performs quite well. Before beginning, ensure that you are familar with how to setup Virtual Machines in Proxmox, and have understanding of basic terminology.

This has been tested in Proxmox 8.1 and 8.2

## Proxmox Resources

1. [Proxmox manual](https://pve.proxmox.com/pve-docs/)
2. [Promox Wiki - KVM Virtual Machines](https://pve.proxmox.com/wiki/Qemu/KVM_Virtual_Machines)

## Create a new VM

1. Download the release of BlissOS that you would like to use, tested 14/15/16 as of April 2024, and place the file in the ISO directory (default `/var/lib/vz/template/iso`)
2. On the Proxmox WebUI, click `Create VM`. *DO NOT START THE VM AFTER CONFIGURING.*
3. Click the `Advanced` checkbox on the popup that appears.
4. Fill in the following settings for the General tab:

`Name:` whateveryouwant
`Shutdown timeout:` `5` (This is because by default it cannot shutdown the VM, so it will kill the VM after 5 seconds. Adjust as per your needs.)
`Start at boot:` Checked, if you would like it to start when the host machine starts.

5. Click `Next`, and fill in the following settings for the OS tab:
`Use CD/DVD disc image file (iso)`: <ISO OF BLISS OS>
`Guest OS Type`: `Linux`
`Guest OS Version`: `6.x`

6. Click `Next`, and fill in the following settings for the System tab:
`Graphics Card`: `VMware compatible` (The default does not work with BlissOS)
`Machine`: Q35 (This allows you to pass through devices)
`BIOS`: `SeaBIOS` or `OVMF (UEFI)` both work.

7. Click `Next`, and fill in the following settings for the Disks tab:
`Disk size:` 50GB (or whatever size disk you would like)

8. Click `Next`, and fill in the following settings for the CPU tab:
`Cores`: How many cores you would like, depending on host/goals of using the Android VM.
`Type`: `x86-64-v2-AES`
`Extra CPU Flags`: `aes+` (click the  `+` next to `aes`, it's at the bottom of the flags, ensures AES is enabled)

9. Click `Next`, and fill in the following settings for the Memory tab:
`Memory (MiB)`: How much memory you would like, depending on host/goals of using the Android VM.
`Ballooning Device`: Unchecked

10. Click `Next`, and fill in the following settings for the Network tab:
`Bridge`: Your network bridge (default is `vmbr0`)
`Model`: `VirtIO (paravirtualized)`

11. Click `Next`, and in the `Confirm` tab, ensure everything is as you configured it. Click `Confirm`. DO NOT START THE VM.

12. Select the VM on the left-hand side window of the Proxmox WebUI

13. Click the `Options` menu item in the Virtual Machine settings.

14. Ensure `QEMU Guest Agent` is `Disabled`

15. Click the `Console` menu item in the Virtual Machine settings.

16. Click `Start Now`

17. Depending on your configuration (BIOS vs UEFI, etc), you will install it differently. These instructions are pretty much exactly the same as bare-metal BlissOS installs, so you can install using that page - however ensure you select `Grub2` as your boot manager during install, if it asks for it, as we will need to select a custom option (18) once you've installed.

18. Once BlissOS has been installed, stop the Virtual Machine.

19. Click the `Hardware` menu item in the Virtual Machine settings.

20. Remove the `CD/DVD Drive` (so it doesn't try and boot to the installer)

21. Click the `Console` menu item in the Virtual Machine settings.

22. Click `Start Now`

23. Quickly press the `DOWN` key when you see the Grub2 boot menu

24. Select `VM Options` (arrow keys, then `ENTER`)

25. Select `Blissos-xx.x.x - xxxx-xx-xx Vbox/VMWare - No HW Acceleration` (arrow keys, then `ENTER`)

> This is because there is no hardware acceleration by default with the `VMware compatible` graphic card. When starting/rebooting the VM in future, it will use this option by default.

## Additional Configuration

### Audio

If you would like audio, or if audio is giving you issues in logcat, in the `Hardware` tab in the Proxmox WebUI:

1. Select `Add Hardware`
2. Click `Audio`
3. In the `Add: Audio Device` window:
`Audio Device`: `AC97`
`Backend Driver`: `none`

### Force Stopping the Virtual Machine

Sometimes when rebooting the host, or performing a shutdown, the shutdown process can hang, as the Virtual Machine can get stuck on the shutdown menu in Android.

You can configure a [hookscript](https://pve.proxmox.com/pve-docs/pve-admin-guide.html#_hookscripts) which will run when you shutdown a VM (eg when the host reboots, etc).

To do so, on the host Proxmox, via SSH:

1. Create the directory `/var/lib/vz/snippets`, if it does not already exist.
2. Create a file called `/var/lib/vz/snippets/forcestop-blissos.sh`, with the following contents:

```sh
#!/bin/bash
if [ "$2" == "pre-stop" ]
then
  nohup /usr/sbin/qm stop "$1" &>/dev/null &
fi
```

3. `chmod a+x /var/lib/vz/snippets/forcestop-blissos.sh`

4. Configure the script, for the given VMID:

```sh
qm set VMID --hookscript local:snippets/forcestop.sh
```

This will stop the Virtual Machine after the timeout in the `Start/Shutdown order` under `Options` in the Proxmox UI.

> To remove it: `qm set VMID --delete hookscript`