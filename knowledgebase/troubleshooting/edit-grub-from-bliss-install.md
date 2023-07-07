# How to edit grub from your Bliss OS install

Follow these steps to edit EFI from rooted Bliss OS

### Step 1:
Boot into Bliss OS and select "DEBUG mode" from the grub menu. Once boot completes, press enter

### Step 2:
Mount your efi partition (usually first partition in disk):

  mount /dev/sda1 /mnt

### Step 3:
Change directory to /mnt/efi/boot:

  cd /mnt/efi/boot

### Step 4:
Edit the android.cfg using vi editor:

  vi android.cfg

### Step 5:
Use Vi Editor to add your changes and save 
-  Press 'insert' to enter edit mode
-  Press 'Esc', type ':w', and press 'Enter' to save.
-  Press 'Esc', type ':q', and press 'Enter' to exit.
- Also you can combine save & exit with ':wq'

### Step 6:
Now you can umount efi partiton and reboot to see your changes:
  umount /dev/sda1
  reboot -f
