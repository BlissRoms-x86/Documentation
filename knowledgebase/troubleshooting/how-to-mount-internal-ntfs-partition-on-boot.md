# How to mount internal NTFS partition on boot

## **Method 1**

It is possible to automatically mount disks at boot. There may be other ways to do this, but one way to do it is:

1\) Write the commands for creating a folder and mounting the disk in a text file which will be a bash script file. 

`#!/system/bin/sh  
  
mkdir /mnt/folder_name mount.ntfs /dev/block/partition_name /mnt/folder_name`

Here 'partition\_name' could be any of the disk you want to mount like for example sda1 devb1 etc. and 'folder\_name' is the folder where you want to mount the disk.

2\) Place the script file under /system/etc/init.d. You can name the file anything you want, but it should not have any extension i.e. .txt or any other extension must be removed.

3\) Reboot and enjoy ! Use a file manager like Mixplorer which can bookmark the mounted locations for easy access. The disks should now be mounted automatically at every boot.

## **Method 2**

Mount all NTFS Partitions on boot \(mostly automated script\)

**Steps:**

1\) Open Terminal   
2\) create a "mountfs" file with the conetents below and place it under /system/bin   
3\) Then from a terminal:  
`su  
chmod 777 /system/bin/mountfs`   
4\) Then type   
`mountfs`   
5\) Done

```text
#!/system/bin/bash
MNT="/mnt/runtime/default/"
mount_drives() {
FILENAME="/system/etc/drives"
N=1
blkid | grep ntfs | cut -d : -f 1 > $FILENAME
while read BLOCK; do
echo "BlissOS_NTFS: Mounting blocks: $BLOCK"
LABEL="$( blkid -s LABEL $BLOCK | cut -d : -f 2 | cut -d '"' -f 2 )"
if [ -z $LABEL ]; then
LABEL="BlissOS_NTFS"
fi
mkdir /mnt/"$LABEL"
mount.ntfs $BLOCK /mnt/"$LABEL"
((N++))
done < $FILENAME
rm $FILENAME 
}

mount_drives >/dev/null 2>&1 
sleep 2
echo "All NTFS partitions / drivers mounted at $MNT" && echo
echo "Making some arrangements so that all NTFS partitions could be mounted at boot" && clear
mv $(pwd)/mountfs /system/etc/init.d/ 
chmod 777 /system/etc/init.d/mountfs
sleep 2
echo "(R) Reboot (E) Exit" && echo 
read -n 1 -p "Choose any option :- " X
case $X in
R | r)
echo "Rebooting" && sleep 2
chvt 7 && sleep 2 && reboot
;;
E | e)
echo "Exiting" && sleep 2
chvt 7 && exit 0
;;
esacs
```

