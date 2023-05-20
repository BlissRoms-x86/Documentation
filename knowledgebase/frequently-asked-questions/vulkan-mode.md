# Vulkan Mode

In our Android 9+ versions, we include the ability to use a Grub command for enabling Vulkan mode on supported graphics hardware.
If you are testing using Bliss OS 15.8.5 or newer, you may not need to worry about triggering this value, as it should be automatic. 

#### To initially test:

When your PC reboots, it's initially shows a grub menu. tap "e" to edit the current selection and add the VULKAN=1 to the end of the line that starts with "kernel", then follow the on-screen prompts to boot with those edits.

If it works, then edit Grub from your main OS, and add the flag there. 

#### Here is an example Grub menuentry (Bliss OS 15.8.x):

`menuentry 'Bliss-x86 Test-Vulkan' --class bliss { search --file --no-floppy --set=root /AndroidOS/android.boot linux /AndroidOS/kernel root=/dev/ram0 SRC=/AndroidOS androidboot.selinux=permissive GRALLOC=minigbm HWC=drm_minigbm quiet DATA= VULKAN=1 initrd /AndroidOS/initrd.img }`

