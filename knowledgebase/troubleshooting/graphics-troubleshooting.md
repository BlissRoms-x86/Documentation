# Debug Graphics

When booting into Bliss for the first time, some users may see a blinking cursor, scrambled graphics, or black screen. These are all signs that your device requires some configuration to get things to run. 

Android-x86, AG & Bliss OS builds all come with a configurable graphics stack that can be used through Grub CLI. 

### The main options for configuration are as follows,

#### Gralloc:

* GRALLOC=gbm - Uses GBM  interface \(Vulkan mode default\)
* GRALLOC=minigbm - Uses Minigbm interface
* GRALLOC=intel - Uses Intel minigbm interface
* GRALLOC=none - Force no Gralloc interface

#### HW-Composer:

* HWC=drm - Standard for versions &lt;= Android 9
* HWC=drmfb - Use drm-framebuffer insead
* HWC=intel - Used Intel IA HWC
* HWC=none - Force no hwcomposer definition 

### Grub Examples:

```text
label debug
	menu label Live CD - ^Debug mode
	kernel /kernel
	append initrd=/initrd.img CMDLINE DEBUG=2 SRC= DATA=

label debug_gbm
	menu label Live CD - Debug mode gralloc.gbm
	kernel /kernel
	append initrd=/initrd.img CMDLINE DEBUG=2 SRC= DATA= GRALLOC=gbm

label debug_drmfb-composer
	menu label Live CD - Debug mode drmfb-composer gralloc.gbm
	kernel /kernel
	append initrd=/initrd.img CMDLINE DEBUG=2 SRC= DATA= HWC=drmfb GRALLOC=gbm

label debug_hwc_gbm
	menu label Live CD - Debug mode hwcomposer.drm gralloc.gbm
	kernel /kernel
	append initrd=/initrd.img CMDLINE DEBUG=2 SRC= DATA= HWC=drm GRALLOC=gbm

label debug_minigbm
	menu label Live CD - Debug mode gralloc.minigbm
	kernel /kernel
	append initrd=/initrd.img CMDLINE DEBUG=2 SRC= DATA= GRALLOC=minigbm

label debug_hwc_minigbm
	menu label Live CD - Debug mode hwcomposer.drm_minigbm gralloc.minigbm
	kernel /kernel
	append initrd=/initrd.img CMDLINE DEBUG=2 SRC= DATA= HWC=drm_minigbm GRALLOC=minigbm

label debug_intel
	menu label Live CD - Debug mode hwcomposer.intel gralloc.intel
	kernel /kernel
	append initrd=/initrd.img CMDLINE DEBUG=2 SRC= DATA= HWC=intel GRALLOC=intel
```

