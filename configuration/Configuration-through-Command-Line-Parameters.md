# Configuration Through Command Line Parameters

Bliss OS (and Bliss-Bass) utilizes an expanded configuration layer, our Broad Apparatus Support System (Bass for short) allows one generic .iso to be configured through kernel command line parameters on a per-device basis. This allows end users to fine tune the performance of the OS to the capabilities of the device.

## Intro to Kernel Command Line Parameters

We use Grub, Refind, or other Linux bootloaders to boot the device once installed. The menu entry is how we also pass many of our parameters for configuring the device as well. Here is an example of a typical menu entry for Grub for Bliss OS 15.8.x:

```
menuentry 'Android-x86' --class android-x86 {

   search --file --no-floppy --set=root /AndroidOS/android.boot

   linux /AndroidOS/kernel root=/dev/ram0 SRC=/AndroidOS fbcon=rotate:1 mem_sleep_default=shallow acpi_sleep=s3_mode HWC=drm_minigbm_celadon GRALLOC=minigbm androidboot.selinux=permissive quiet DATA=

   initrd /AndroidOS/initrd.img

}
```

The line we are targeting for our configuration is the one that starts with “`linux /AndroidOS/kernel`”. Here are some of the explanations for the configs we support.

If your Grub menu entry looks a little different, and has some of the commands at the top, you can also edit there to add your configuration preferences. 
```
setparams 'Android-x86 Intel' 'fbcon=rotate:1' 'mem_sleep_default=shallow' 'acpi_sleep=s3_mode' 'HWC=drm_minigbm_celadon' 'GRALLOC=minigbm' 'quiet'
   savedefault
   set root=$android
   ....
```

## Supported Parameters:

**Warning**: Note that not all of the configs listed here are available in the open-source Bliss OS source, but are available through licensing the Android-Generic Project add-on for that feature, or licensing the use of Bliss Bass (which comes with most available AG add-on's) for your organization

Here are a few of the supported parameters that we support, organized by stack.

### Debugging:

This will enable logging or low level debugging console before the system starts to boot Android.

This is a root console, and can be used for testing or troubleshooting. Pressing exit, twice will continue with the Android boot process.

Command:

`DEBUG=*`

Options:

*   1 - high-level debugging console (logs to /tmp/log)
    
*   2 - low-level debugging console (logs to /data/log.txt & /tmp/log)
    

Example:

```
DEBUG=2
```

### Graphics Stack:

This includes a number pf parameters that we can use to customize the stack to our hardware's capabilities.

#### Hardware Composer:

This allows us to include multiple hardware-composer options, and select the ones we want to target per-device.

By default, if you set HWC without anything like `HWC=""`, Bliss will use [drmfb-composer](https://github.com/me176c-dev/drmfb-composer)

Command:

`HWC=*`

Options:
    
*   `drm` - Use [drm_hwcomposer](https://gitlab.freedesktop.org/drm-hwcomposer/drm-hwcomposer)

*   `drm_celadon` - Use [Project Celadon's fork of drm_hwcomposer](https://github.com/projectceladon/drm-hwcomposer)    
    
*   `drm_minigbm` - Use [drm_hwcomposer](https://gitlab.freedesktop.org/drm-hwcomposer/drm-hwcomposer) with minigbm support
    
*   `drm_minigbm_celadon` - Use [Project Celadon's fork of drm_hwcomposer](https://github.com/projectceladon/drm-hwcomposer) with minigbm support

Example:

```
HWC=drm_minigbm_celadon
```

#### Gralloc:

We pair up our Gralloc options to work with certain HWC options.

Command:

`GRALLOC=*`

Options:

*   `gbm` - This is [gbm_gralloc](https://github.com/BlissRoms-x86/gbm_gralloc) and it's compatible with drm & drm\_celadon

*   `gbm_hack` - This is [gbm_gralloc](https://github.com/BlissRoms-x86/gbm_gralloc) but with a [HACK commit](https://github.com/BlissRoms-x86/gbm_gralloc/commit/e70acfc3f5acc64168a172987f34d141e55f6b54) to fix some issue with iris or nouveau. It's compatible with drm & drm\_celadon
    
*   `minigbm` - This is [minigbm](https://github.com/supremegamers/minigbm) and it's compatible with drm\_minigbm, drm\_minigbm\_celadon

*   `minigbm_arcvm` - This is [minigbm](https://github.com/supremegamers/minigbm) but made specifically for virgl by Google, compatible with drm\_minigbm, drm\_minigbm\_celadon
    
*   `minigbm_gbm_mesa` - This is [minigbm](https://github.com/supremegamers/minigbm/tree/13-x86/gbm_mesa_driver) made by [rsglobal](https://github.com/rsglobal) which he tried to port [gbm_gralloc philosophy to minigbm](https://github.com/robherring/gbm_gralloc/issues/26). Compatible with drm\_minigbm, drm\_minigbm\_celadon

Example:

```
GRALLOC=minigbm_gbm_mesa
```
##### Gralloc4 Configurations:

In some cases, we are working with hardware that requires Gralloc4 specs. We can force minigbm to use Gralloc4 by using:

*   `GRALLOC4_MINIGBM` - Force using gralloc4 with minigbm (only compatible with drm_minigbm_celadon)

#### ANGLE & software rendering

- [ANGLE](https://github.com/google/angle) is available in BlissOS 14.10 and above, if you have a device that can use Vulkan, you can try ANGLE with Vulkan backend using `ANGLE=1`.

- Alternatively, you can also use [SwiftShader Vulkan](https://github.com/google/swiftshader) with ANGLE as a software rendering solution if you are running it inside something like a virtual machine by setting `nomodeset ANGLE=1` or `HWACCEL=0 ANGLE=1`

- There's one more option for software rendering, which is the legacy [SwiftShader EGL](https://gitlab.com/hmtheboy154/swiftshader/-/tree/legacy_11-x86/src/OpenGL), simply set `nomodeset` or `HWACCEL=0` to use it.

**NOTE** : You may want to turn on Color Inversion when using software rendering because the color might be inverted

### Media Stack:

#### Video Encoders/Decoders:

By default, BlissOS will use AOSP's codec2 software decoder. We also offer a few various options that allow you to select different video decoders stack options:

*   `CODEC2_LEVEL` - This will set the C2 level (default value is `4`, you can disable codeec2 completely with '0').
*   `FFMPEG_OMX_CODEC` - This will enable OMX version of FFMPEG codecs (disable codec2 with `CODEC2_LEVEL=0` to use this codecs).
*   `FFMPEG_CODEC2_PREFER` - This will force Bliss to use codec2 version of FFMPEG codecs by default.
*   `FFMPEG_HWACCEL_DISABLE` - This will disable hardware accelleration for the FFMPEG codecs.
*   `FFMPEG_CODEC_LOG` - This will show more log of FFMPEG codecs, enable if you want to debug it
*   `FFMPEG_CODEC2_DEINTERLACE` & `FFMPEG_CODEC2_DEINTERLACE_VAAPI` - configuring deinterlacing option for FFMPEG codec2, you can find the options in this [commit made by Micheal Goffioul](https://github.com/supremegamers/stagefright-plugins/commit/48e5d4e8c6e577c7f56c1da3aa90897444422d33)
*   `OMX_NO_YUV420` - This will force the system to not use YUV420 color format on OMX codec (fixes some black or glitchy screens, use it with `CODEC2_LEVEL=0`)

### Networking:

We include the ability to have Ethernet appear as WiFi as some appli/cations will not work without a WiFi or Cell connection present. In order to enable this mode, we use the VIRT_WIFI boot flag:
  `VIRT_WIFI=1`

### USB/PCI:

#### USB:

##### USB Modes:

(Available through Android-Generic Add-On & Bliss Bass)

Switch USB mode (ADB/Storage) !! Requires kernel configs !!:
* `FORCE_USE_ADB_CLIENT_MODE` - Forces client mode adb settings
* `FORCE_USE_ADB_MASS_STORAGE` - Forces USB mass_storage mode

### Power & Memory:

#### Power:

##### Default Sleep Mode Options:

The following are the options for **mem\_sleep\_default** (suggested values are in bold):

*   deep - This option will choose the deepest sleep state supported by the hardware.
    
*   **shallow** - This option will choose the shallowest sleep state supported by the hardware.
    
*   **s2idle** - This option will choose the S2 idle state.
    
*   s3bios - This option will choose the S3 BIOS state.
    
*   **s3standby** - This option will choose the S3 standby state.
    
*   mem - This option will choose the memory state.
    
*   off - This option will choose the off state.
    

The default value for mem\_sleep\_default is deep, and the suggested value for most devices is **shallow**

Example:

```
mem_sleep_default=shallow
```

The following are the options for **acpi\_sleep** (suggested values are in bold):

*   s3\_bios - This option will use the BIOS-provided S3 sleep state.
    
*   **s3\_mode** - This option will use the kernel's own S3 sleep state.
    
*   s4\_nohwsig - This option will disable the ACPI hardware wake signal for S4 sleep.
    
*   old\_ordering - This option will use the old ACPI sleep state ordering.
    
*   nonvs - This option will not save non-volatile storage (NVS) to disk before entering S4 sleep.
    
*   sci\_force\_enable - This option will force the ACPI SCI interrupt to be enabled.
    

The default value for acpi\_sleep is s3\_bios, and the suggested value for most devices is **s3\_mode**.

Example:

```
acpi_sleep=s3_mode
```

##### Other Sleep Mode Options

There are a number of other Linux kernel command line options that can be used to configure sleep mode settings. Some of these options include:

*   acpi\_sleep\_default: This option specifies the default sleep mode for the system. The possible values are:
    
    *   **s3** - Standby mode
        
    *   s4 - Hibernate mode
        
    *   s5 - Off mode
        

Example:

```
acpi_sleep_default=s3
```

*   suspend\_console: This option specifies whether to suspend the console when the system enters sleep mode. The possible values are:
    
    *   on - The console will be suspended.
        
    *   off - The console will not be suspended.
        

Example:

```
suspend_console=on
```

*   no\_console\_suspend: This option specifies that the console should not be suspended when the system enters sleep mode. This option overrides the value of the suspend\_console option.
    

Example:

```
no_console_suspend
```

#### Memory:

(Available through Android-Generic Add-On & Bliss Bass)

*   `FORCE_MEMORY_TRIM_ENABLE` - Sets the memory\_trim\_enable value  
    Options: (true|false)
    
*   `FORCE_MIN_FREE_MEMORY` - Sets the minimum free ram limit
    
    Example value: "512M"
    
*   `FORCE_MAX_FREE_MEMORY` - Sets the max free ram limit
    
    Example value: "1024M"
    
*   `FORCE_OOM_SCORE_ADJ` - Sets the oom\_score\_adj value
    
    Example value: "100"
    
*   `FORCE_ENFORCE_MIN_FREE_MB` - Sets the enforce\_min\_free\_mb value
    
    Example value: "1024"
    
*   `FORCE_SCHED_MIN_FREE_PAGES` - Sets the sched\_min\_free\_pages value
    
    Example value: "1024"
    
*   `FORCE_SCHED_MIN_ACTIVE_PAGES` - Sets the sched\_min\_active\_pages value
    
    Example value: "512"
    
*   `FORCE_SCHED_MIN_INACTIVE_PAGES` - Sets the sched\_min\_inactive\_pages value
    
    Example value: "256"
    
*   `FORCE_SCHED_MIN_DIRTY_PAGES` - Sets the sched\_min\_dirty\_pages value
    
    Example value: "128"
    
*   `FORCE_SCHED_MIN_WRITEBACK_PAGES` - Sets the sched\_min\_writeback\_pages value
    
    Example value: "64"
    
*   `FORCE_SCHED_MIN_RECLAIMABLE_PAGES` - Sets the sched\_min\_reclaimable\_pages value
    
    Example value: "32"
    
*   `FORCE_SCHED_MIN_UNRECLAIMABLE_PAGES` - Sets the sched\_min\_unreclaimable\_pages value
    
    Example value: "16"
    

### LMKD:

(Available through Android-Generic Add-On & Bliss Bass)

*   `FORCE_LOW_MEM` - Forces the low\_mem mode in Android to the specific true/false value  
    Default = false (unless device is detected to have less than 2GB RAM)
    
*   `FORCE_LMK_ENABLE` - Force enable LMK Daemon  
    Default = false (unless device is detected to have less than 2GB RAM)
    
*   `FORCE_KILL_HEAVIEST_TASK` - Kill heaviest eligible task (best decision) vs. any eligible task (fast decision).  
    Default = false
    
*   `FORCE_SWAP_FREE_LOW_PERCENTAGE` - Level of free swap as a percentage of the total swap space used as a threshold to consider the system as swap space starved.
    
    Default for low-RAM devices = 10, for high-end devices = 20
    
*   `FORCE_THRASHING_LIMIT` - Number of working set defaults as a percentage of the file-backed pagecache size used as a threshold to consider system thrashing its pagecache.
    
    Default for low-RAM devices = 30, for high-end devices = 100
    
*   `FORCE_LMK_CRITICAL` - The possible values of oom\_adj range from -17 to +15 (Default=0).
    
    The higher the score, more likely the associated process is to be killed by OOM-killer. If oom\_adj is set to -17, the process is not considered for OOM-killing.
    
*   `FORCE_PSI_PARTIAL_STALL_THRESHOLD` - The partial PSI stall threshold, in milliseconds, for triggering low memory notification. If the device receives memory pressure notifications too late, decrease this value to trigger earlier notifications. If memory pressure notifications trigger unnecessarily, increase this value to make the device less sensitive to noise. 
    
    High end: 70  Low end: 200
    
*   `FORCE_PSI_COMPLETE_STALL_THRESHOLD` - The complete PSI stall threshold, in milliseconds, for triggering critical memory notifications. If the device receives critical memory pressure notifications too late, decrease this value to trigger earlier notifications. If critical memory pressure notifications trigger unnecessarily, increase this value to make the device less sensitive to noise.
    
    Recommended: 700
    
*   `FORCE_THRASHING_LIMIT_DECAY` - The thrashing threshold decay expressed as a percentage of the original threshold used to lower the threshold when the system doesn’t recover, even after a kill. If continuous thrashing produces unnecessary kills, decrease the value. If the response to continuous thrashing after a kill is too slow, increase the value. 
    
    High end: 10  Low end: 50
    
*   `FORCE_SWAP_UTIL_MAX` - The max amount of swapped memory as a percentage of the total swappable memory. When swapped memory grows over this limit, it means that the system swapped most of its swappable memory and is still under pressure. This can happen when non-swappable allocations are generating memory pressure which can not be relieved by swapping because most of the swappable memory is already swapped out. The default value is 100, which effectively disables this check. If the performance of the device is affected during memory pressure while swap utilization is high and the free swap level is not dropping to Sets the swap\_free\_low\_percentage, decrease the value to limit swap utilization. 
    
    High end: 100  Low end: 100
    

### Performance:

(Available through Android-Generic Add-On & Bliss Bass)

*   `FORCE_POWER_PROFILE` - Sets the power\_profile value
    
    *   Options: ondemand, hotplug, interactive, performance
        
*   `FORCE_IO_PROFILE` - Sets the io\_profile value
    
    *   Options: ondemand, hotplug, interactive, performance
        
*   `FORCE_CPU_GOV` - Sets the cpu\_governor value
    
    *   Options: ondemand, hotplug, interactive, performance
        
*   `FORCE_CPU_SCALING_GOV` - Sets the cpu\_scaling\_governor value
    
    *   Options: ondemand, hotplug, interactive, performance
        
*   `FORCE_GPU_SCALING_GOV` - Sets the gpu\_scaling\_governor value
    
    *   Options: ondemand, hotplug, interactive, performance
        
*   `FORCE_THERMAL_THROTTLE_ENABLE` - Sets the thermal\_throttle\_enable value  
    Options: (true|false)
    

### Features:

(Available through Android-Generic Add-On & Bliss Bass)

*   `FORCE_HIDE_NAVBAR` - Set the config\_hideNavBarButtons value  
    Options: (true|false)
    
*   `FORCE_ENABLE_RECENTS_GESTURE` - Sets the config\_enableRecentsGestureOnNavBar value  
    Options: (true|false)
    
*   `FORCE_SET_MAX_RECENTS` - Sets the config\_maxRecents value  
    Options:
    
    *   `0`: Disables the recent apps menu.
        
    *   `1`: Displays up to 1 app in the recent apps menu.
        
    *   `2`: Displays up to 2 apps in the recent apps menu.
        
    *   `3`: Displays up to 3 apps in the recent apps menu.
        
    *   `4`: Displays up to 4 apps in the recent apps menu.
        
    *   `5`: Displays up to 5 apps in the recent apps menu.
        
*   `FORCE_ENABLE_CLEAR_ALL_RECENTS` - Sets the config\_enableClearAllRecents value  
    Options: (true|false)
    
*   `FORCE_DEFAULT_QS_TILES` - Sets the config\_defaultQsTiles value  
    Options:
    
    *   **none:** This will remove all default quick settings tiles.
        
    *   **all:** This will restore all default quick settings tiles.
        
    *   **<tile\_id>:** This will add or remove the specified quick settings tile.
        
#### Rotation/Orientation:
   
* `SET_SF_ROTATION=*` - Sets surfaceflinger hardware rotation property to the value passed
* 
* `SET_OVERRIDE_FORCED_ORIENT=*` - Override forced orientation (true/false)
* 
* `SET_SYS_APP_ROTATION=*` - Forces system app rotation, and has three cases:   
  * 1.force_land: always show with landscape, if a portrait apk, system will scale up it
  * 2.middle_port: if a portrait apk, will show in the middle of the screen, left and right will show black
  * 3.original: original orientation, if a portrait apk, will rotate 270 degree


### Misc:

(Available through Android-Generic Add-On & Bliss Bass)

#### Battery Stats:

*   `SET_FAKE_BATTERY_LEVEL` - Let us fake the total battery percentage  
    Options: (0-100)
    
*   `SET_FAKE_CHARGING_STATUS` - Allow forcing battery charging status  
    Options: (0|1)
    

### Package Management:

(Available through Android-Generic Add-On & Bliss Bass)

With our package management features, we have the ability to also enable/disable various packages included in the system by default using the kernel cmdline.

Example: `ENABLE_VENDING=1`

##### AOSP Default Apps:

*   `ENABLE_SEARCHBOX` - Enables com.android.quicksearchbox
    
*   `ENABLE_RSSREADER` - Enables com.example.android.rssreader
    
*   `ENABLE_WEBVIEW_SHELL` - Enables org.chromium.webview\_shell
    
*   `ENABLE_CONTACTS` - Enables com.android.contacts
    
*   `ENABLE_CAMERA2` - Enables com.android.camera2
    
*   `ENABLE_CALENDAR` - Enables com.android.calendar
    
*   `ENABLE_NOTEPAD` - Enables com.example.android.notepad
    
*   `ENABLE_DIALER` - Enables com.android.dialer
    
*   `ENABLE_GALLERY` - Enables com.android.gallery3d
    
*   `ENABLE_WALLPAPER_PICKER` - Enables com.android.wallpaperpicker
    

##### Google Apps:

*   `ENABLE_GOOGLESEARCHBOX` - Enables com.google.android.googlequicksearchbox
    
*   `ENABLE_VENDING` - Enables com.android.vending (Play Store)
    

##### Bliss OS / FOSS Apps:

*   `ENABLE_TSCAL` - Enables org.zeroxlab.util.tscal (Touch Calibration app)
    
*   `ENABLE_ELEVEN` - Enables org.lineageos.eleven (Lineage OS Music player)
    
*   `ENABLE_SETORIENTATION` - Enables com.googlecode.eyesfree.setorientation (Manual control of screen orientation)
    
*   `ENABLE_PHONOGRAPH` - Enables com.kabouzeid.gramophone (FOSS music player)
    
*   `ENABLE_ABOUT_BLISS` - Enables com.blissroms.aboutbliss (About Bliss app)
    
*   `ENABLE_KERNELSU` - Enables me.weishu.kernelsu (Root management)
    
*   `ENABLE_SIMPLEGALLERY` - Enables com.simplemobiletools.gallery.pro (FOSS Gallery app)
    
*   `ENABLE_AURORA_SERVICES` - Enables com.aurora.services (FOSS install service used with Aurora Store/Aurora Droid)
    
*   `ENABLE_GAMESPACE` - Enables io.chaldeaprjkt.gamespace (FOSS Game Mode service)
    

##### Settings Access Restrictions:

*   `HIDE_SETTINGS` - Hides com.android.settings

### USB Mode Functions:

(Available through Android-Generic Add-On & Bliss Bass)

Allows switching default USB/ADB functions via cmdline 

*   `FORCE_USE_ADB_CLIENT_MODE` - Forces USB into ADB Client mode (0=off, 1=on)
*   `FORCE_USE_ADB_MASS_STORAGE` - Force enable ADB Mass Storage mode ofver USB (0=off, 1=on)

### IIO Options Configuration:

(Available through Android-Generic Add-On & Bliss Bass)

This allows us to set ro.iio.* propertied through the following flags:

*   `SET_IIO_ORDER` - Sets ro.iio.accel.order property
*   `SET_IIO_ACCEL_QUIRKS` - Sets ro.iio.accel.quirks ptoperty
*   `SET_IIO_ACCEL_X_OPT_SCALE` - Sets ro.iio.accel.x.opt_scale property
*   `SET_IIO_ACCEL_Y_OPT_SCALE` - Sets ro.iio.accel.y.opt_scale property
*   `SET_IIO_ANGLVEL_QUIRKS` - Sets ro.iio.anglvel.quirks property
*   `SET_IGNORE_ATKBD` - Sets ro.ignore_atkbd property

### Set Timezone:

(Available through Android-Generic Add-On & Bliss Bass)

To use this change, pass the grub/cmdline value for your timezone using the SET_TZ_LOCATION flag.
Timezone reference can be found here: https://en.wikipedia.org/wiki/List_of_tz_database_time_zones

Example:
  ```
   SET_TZ_LOCATION=America/New_York
  ```
### Logging Levels:

(Available through Android-Generic Add-On & Bliss Bass)

Allows overriding default logging/logd configuration

*    `SET_LOGCAT_DEBUG` - Overrides system default logcat debigging level (0=off, 1=on)
*    `SET_MAX_LOGD` - Will force max logging buffer (0=off,1=on) 
