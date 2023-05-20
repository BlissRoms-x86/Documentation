# Not booting after install on Linux

For Linux users that are able to boot our Android 10/11 builds in Live mode, but things fail to boot once installed, this is caused by a bug in our data detection scripts.   
try to remove the data/ folder from the install location, and replace it with an ext4 data.img

**Example for making a 8gb image:** 

`dd if=/dev/zero of=data.img bs=1 count=0 seek=$[1G*8]`

