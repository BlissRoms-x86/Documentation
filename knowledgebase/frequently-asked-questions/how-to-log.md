# How to Log

For grabbing manual logs of issues in Android-x86/Bliss OS, we need to use the alt-f1 console or built in terminal and we need to know the storage location we want to save in. For the examples below, we have our external storage mounted at sdcard/.  

**For Bliss 15.x & 16.x:**  
Use KernelSU app to grant su permissions to Termux, then open the built in Termux app, then enter:

`su  
logcat > sdcard/log.txt`

Or, boot into the OS using `DEBUG=2` from Grub, and the logs will be saved in /data after boot, or /tmp/log during early boot stages
   
**For Bliss 14.x:**  
Open the built in terminal app, then enter:

`su  
logcat > sdcard/log.txt`

**For Bliss 12.x:**  
Use the alt-f1/f7 console, then enter:

`logcat > sdcard/log.txt`  
  
**For Bliss 11:**  
Use the alt-f1/f7 console, then enter:

`logcat > sdcard/log.txt`  
  
After that is done, you can close the terminal app or use alt-f7 to get back to Android if you are using the Console, and use the File Manager to access your log and share it where needed.

