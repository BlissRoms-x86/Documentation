<img src="https://i.ibb.co/0VWm25C/Colors-2-OS.png">
<p align="center">
<a href="https://https://blissos.org">Website</a> |
<a href="https://sourceforge.net/projects/blissos-x86">Download</a> |
<a href="https://www.paypal.com/donate/?hosted_button_id=J5SLZ7MQNCT24">Donate</a> |
<a href="https://docs.blissos.org/">Documentation</a> |
<a href="https://t.me/blissx86">Telegram</a>

## BlissOS
---------------------------------------------------
Download the BlissOS source code, based on [AOSP](https://android.googlesource.com) & [Android-x86](http://android-x86.org/)

<div align="center">
<strong><i>Modified for PC build using Android-Generic Project</i></strong>
<br>
<img src="https://i.ibb.co/rf2rv3M/Yep1l4L.png">
<br>
</div>


Please read the [AOSP building instructions](http://source.android.com/source/index.html) before proceeding.

## Build Requirements for [BlissOS](https://github.com/BlissRoms-x86/manifest)
-----------------------
```
Ubuntu LTS Release (NOT 24.04 as libncurses5 cannot be installed) https://www.ubuntu.com/download/server
Decent CPU (16 Cores or better for a faster performance)
16GB RAM (32GB for Virtual Machine)
350GB Hard Drive (about 170GB for the Repo and then building space needed)
```

Time to compile:
- Laptop: 6-8 hours
- Desktop/Workstation: 4-6 hours
- Server: 1-2 hours


{% hint style="info" %}
These instructions assume the use of Ubuntu 22.04 (jammy).  Using a different version of Ubuntu may require changes to the steps below as packages and package names may change from version to version.
{% endhint %}

## Installing Dependencies
-----------------------
Before installing packages, you will need to enable i386 architecture with `dpkg`.
```bash
dpkg --add-architecture i386
```

```bash
sudo apt-get install git-core gnupg flex bison gperf build-essential zip curl \
    zlib1g-dev gcc-multilib g++-multilib libc6-dev-i386 lib32ncurses5-dev \
    x11proto-core-dev libx11-dev lib32z-dev ccache libgl1-mesa-dev libxml2-utils \
    xsltproc unzip squashfs-tools python3-mako libssl-dev ninja-build lunzip \
    syslinux syslinux-utils gettext genisoimage gettext bc xorriso xmlstarlet \
    meson glslang-tools git-lfs libncurses5 libncurses5:i386 libelf-dev aapt zstd \
    rdfind nasm rustc bindgen rsync wget pkg-config
```

## Cloning the Repository
-----------------------
Initialize the local repository:
```bash
repo init -u https://github.com/BlissRoms-x86/manifest.git -b arcadia-x86 --git-lfs
```
Fetch all the project modules:
```bash
repo sync -c --force-sync --no-tags --no-clone-bundle -j$(nproc --all) --optimized-fetch --prune
```

## Setup FOSS apps or OpenGapps
----------------------------

To build with FOSS (this will include microG Services & some extra apps):
```bash
cd vendor/foss
./update.sh # And then choose 1 (x86/x86_64) to fetch all the apps.
```
 
To include Bromite Webview instead:
```bash
cd vendor/foss
./update.sh "" bromite
```

## Building BlissOS
--------

### Build ISO Image
```bash
. build/envsetup.sh
lunch bliss_x86_64-userdebug
make blissify iso_img -j$(nproc --all)
```

### Build Environment Options

Before running `make iso_img`, you can adding variables into the build to integrate more stuff into the image.
Note that you can put different variables into the build.

#### `BLISS_BUILD_VARIANT`

##### Description
Specify what type of extra apps and services to include in the build.

##### Options
1. `vanilla` **(default)**
2. `foss`
3. `opengapps`

##### Example Usage
```bash
export BLISS_BUILD_VARIANT=foss
```

#### `BLISS_SPECIAL_VARIANT`

##### Description
Build a version for a specific device.

##### Options
1. `-Jupiter` - Steam Deck
2. `-surface` - Microsoft Surface series

##### Example Usage
```bash
export BLISS_SPECIAL_VARIANT=-Jupiter
```

#### `ANDROID_USE_INTEL_HOUDINI`

##### Description
Build with proprietary libhoudini extracted from WSA

##### Example Usage
```bash
export ANDROID_USE_INTEL_HOUDINI=true
```

#### `BOARD_IS_SURFACE_BUILD`

##### Description
Build the special "surface" variant which include kernel with patches from linux-surface and the iptsd userspace touchscreen daemon.

##### Example Usage
```bash
export BOARD_IS_SURFACE_BUILD=true
```

#### `BOARD_IS_GO_BUILD`
##### Description
Build the special "go" variant for BlissOS Go

##### Example Usage
```bash
export BOARD_IS_GO_BUILD=true
```

**More build options will be in Extras part including proprietary native-bridge/widevine libraries**

## Extras
-------

We do offer some extra libraries that can be compiled into the build. These include:

### Prebuilt Widevine from Windows Subsystem for Android
```bash
mkdir -p vendor/google/propietary
git clone https://github.com/supremegamers/vendor_google_proprietary_widevine-prebuilt vendor/google/propietary/widevine-prebuilt
```
The variable to activate this is `USE_WIDEVINE=true`

### Windows Subsystem for Android's libhoudini
```bash
mkdir -p vendor/intel/proprietary
git clone https://github.com/supremegamers/vendor_intel_proprietary_houdini vendor/intel/proprietary/houdini
```
The variable to activate this is `ANDROID_USE_INTEL_HOUDINI=true`

## Report build issues
-------
You can reach us via [Telegram (Android™-Generic (x86 PC) Community Development)](https://t.me/androidgenericpc)

