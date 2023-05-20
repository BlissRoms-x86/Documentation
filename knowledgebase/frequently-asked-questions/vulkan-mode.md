# Vulkan Mode

In our Android 9+ versions, we include the ability to use a Grub command for enabling Vulkan mode on supported graphics hardware.

#### To initially test:

When your PC reboots, it's initially shows a grub menu. tap "e" to edit the current selection and add the VULKAN=1 to the end of the line that starts with "kernel", then follow the on-screen prompts to boot with those edits.

If it works, then edit Grub from your main OS, and add the flag there. 

#### Here is an example Grub menuentry:

`menuentry 'Bliss-x86 Test-Vulkan' --class bliss { search --file --no-floppy --set=root /AndroidOS/android.boot linux /AndroidOS/kernel root=/dev/ram0 SRC=/AndroidOS androidboot.selinux=permissive androidboot.hardware=android_x86_64 buildvariant=eng quiet DATA= VULKAN=1 initrd /AndroidOS/initrd.img }`

