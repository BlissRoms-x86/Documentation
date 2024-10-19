## Troubleshooting TouchScreen Driver Loading

After a succesfully install of Bliss OS I noticed that my touchscreen didn't work. While inspecting the `dmesg` logs, I discovered that the system was attempting to load the incorrect driver, indicated by the following error:
```
silead_ts i2c-MSSL0017:00: Direct firmware load for silead/mssl0017.fw failed with error -2
```

This problem can be resolved by following a procedure similar to the one detailed in [this GitHub issue](https://github.com/danielotero/linux-on-hi10/issues/20) but applied specifically to BlissOS.

### Instrunctions

1. **Mount the System as Read-Write:**
   You must first remount your system as read-write. Instructions can be found on the [BlissOS documentation site](https://docs.blissos.org/knowledgebase/troubleshooting/remount-system-as-read-write/).

   In my case, I already had `system.img` so I simply followed the instructions starting from the command `reboot -f`.

2. **Replace the Firmware File:**
   Once you can modify the system partition, rename the firmware file:
   ```
   mv /vendor/firmware/silead/gsl1680-chuwi-hi10plus.fw /vendor/firmware/silead/mssl0017.fw
   ```

3. **Reload the Silead Module:**
   ```
   rmmod silead
   modprobe silead
   ```

4. **Calibration Tool:**
   Now try to run built-in calibration tool, if it is still failing do the following:
   ```
   su
   mkdir -p /data/misc/tscal
   touch /data/misc/tscal/pointercal
   chown 1000:1000 /data/misc/tscal /data/misc/tscal/*
   chmod 775 /data/misc/tscal
   chmod 664 /data/misc/tscal/pointercal
   reboot
   ```
   After rebooting, try running the built-in calibration tool again.
