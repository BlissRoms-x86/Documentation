## Touch Orientation

### Requirements:
- System mounted as RW
- Booted into Debug mode (or using root and terminal)

### Process:
First, you are going to want to get your device uevent from /sys/class/dmi/id/uevent
Then find a good string to look for from it, like we do [here](https://github.com/BlissRoms-x86/device_generic_common/blob/d6f6c278f26ce05b0db205767ba5656ca2aa6533/init.sh#L493)

Your device may also require a couple accel opt_scale options too. Example:
  ```
  *TECLAST*X4*)
      set_property ro.iio.accel.order 102
      set_property ro.iio.accel.x.opt_scale -1
      set_property ro.iio.accel.y.opt_scale -1
      ;;
  ```
Once done, save the file and reboot. 
If it works, please submit the changes as a PR to our [device_generic_common repo](https://github.com/BlissRoms-x86/device_generic_common/tree/d6f6c278f26ce05b0db205767ba5656ca2aa6533)
