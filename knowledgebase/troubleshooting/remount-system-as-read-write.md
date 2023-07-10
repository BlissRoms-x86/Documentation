# Remount system as Read/Write
(Credits go to Knoxville and others from Telegram for this method)

For remounting, we need to use the alt-f1 console or built in terminal. 

**For Bliss 15.x:**
Boot with DEBUG=1, and mount remount the install partition:

```
mount -o remount,rw /dev/block/sd(x)
cd /src
mount system.sfs /tmp
cp /tmp/system.img system.img
umount /tmp
rm -f system.sfs 
```

or if you want to backup original system image
```
mv system.sfs system.bak 
```
Now we sync the filesystem and reboot:
```
sync
reboot -f
```
Upon reboot, you will have R/W on system

Once we have a system.img and no system.sfs, we need to use the built in terminal app (with su permissions granted), and type:
```
cat /proc/mounts
```

Then look for the `/dev/loop#` where we see mounted at "/", likely followed by "ext4 ro....". Note that as we will need to use that for the next command. 
Now type:
```
mount -o remount,rw -t ext4 /dev/loop# /
```

Or for older Bliss versions (**14.x, 11.x**) use:
```
mount -o remount,rw -t ext4 /dev/loop# /system
```

Then you can edit the system files you need. When you are done, you can remount as RO:
```
mount -o remount,ro -t ext4 /dev/loop# /
```

Then test to verify changes stick

**Video Walkthrough**
Watch this video for detailed instructions.

https://youtu.be/hA1SjE3kTfQ
