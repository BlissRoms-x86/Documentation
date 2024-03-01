# Admin Restriction

Some Bass Lockdown builds come with a password restricted Admin mode. We use a grub flag to pass the password through to the system, which then creates a sha256 hash of that string and compares it against the hash saved within the system.  \
In order to enter Admin mode, you must manually boot into Grub by tapping the ‘shift’ key while booting, and select the Admin mode menu option, then tap ‘e’ to edit. From there, you will add:


```
'BLISSBOOTMODE_PASSWORD=your_companies_complex_password_schema1234' 
```


Where ‘your_companies_complex_password_schema1234’ is replaced with the password set in the source. Failure to enter the correct password here will result in the device rebooting into Lockdown mode.

In order to meet the strict security needs, changes in the bootloader are made to always default to Lockdown mode. So in the event of a failed password attempt, the device will reboot and boot back into the default Lockdown mode unless the user prompts the grub menu to show and enters the correct bootmode password in order to boot into Admin mode or Debugging Admin mode.


### Creating a New Admin Password

To create a new Admin mode password, you will need to generate a new hash and update the source file located at vendor/branding/security/blissmode_password. The supported characters for the password are (24 characters [a-z][A-Z][0-9][@#$%&]). To generate a new hash, we use the simple script: 


```
$ echo -n "your_companies_complex_password_schema1234" | sha256sum > blissmode_password
```


In order to confirm that a set password is working, you can run a check on the hashed sum values from Linux: \



```
$ BLISSMODE_PASSWORD="your_companies_complex_password_schema1234"; if [ "$(echo -n "$BLISSBOOTMODE_PASSWORD" | sha256sum)" != "$(cat blissmode_password)" ]; then echo "its a match"; else echo "password does not match"; fi
its a match
```


We also leave feedback of a password check success or failure in the debugging shell at boot. To access that, please select the **Debugging Admin** boot option from Grub. 
