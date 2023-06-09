# Remount system as Read/Write

For remounting, we need to use the alt-f1 console or built in terminal.   
**For Bliss 14.x:**  
Open the built in terminal app, then enter:

`su  
mount -o remount,rw /system`

**For Bliss 12.x:**  
Use the alt-f1/f7 console, then enter:

`mount -o remount,rw -t ext4 /dev/loop0 /`  
  
**For Bliss 11:**  
Use the alt-f1/f7 console, then enter:

`mount -o remount,rw -t ext4 /dev/loop0 /system`  
  
After that is done, you can edit the system files you need. When you are done, you can close the terminal app or use alt-f7 to get back to Android if you are using the Console.

Watch this video for detailed instructions.

https://youtu.be/hA1SjE3kTfQ
