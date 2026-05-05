# Build Bliss OS (v14.x)

![](https://i.imgur.com/pOad4eK.png)

## Getting Started
------

{% hint style="info" %}
We also offer [Android-Generic Project 2.0](https://github.com/android-generic/vendor_ag) as an easy to learn method of building Android 11 for PC's. Checkout the project README.md for more info. 
{% endhint %}

Download the BlissRoms source code, based on [AOSP](https://android.googlesource.com), [phhusson](https://github.com/phhusson/treble_manifest) & [BlissRoms](https://github.com/BlissRoms/platform_manifest)

Please read the [AOSP building instructions](http://source.android.com/source/index.html) before proceeding.

## Build Requirements for [BlissOS](https://github.com/BlissRoms-x86/manifest)
------

```text
Latest Ubuntu LTS Releases https://www.ubuntu.com/download/server
Decent CPU (Dual Core or better for a faster performance)
8GB RAM (16GB for Virtual Machine)
250GB Hard Drive (about 170GB for the Repo and then building space needed)
```

{% hint style="info" %}
These instructions assume the use of Ubuntu 22.04 (jammy).  Using a different version of Ubuntu may require changes to the steps below as packages and package names may change from version to version.
{% endhint %}

## Installing Dependencies

```bash
sudo apt-get install -y git git-lfs gnupg flex bison maven gperf build-essential \
    zip curl zlib1g-dev gcc-multilib g++-multilib libc6-dev-i386 lib32ncurses-dev \
    x11proto-core-dev libx11-dev ccache libgl1-mesa-dev libxml2-utils xsltproc unzip \
    squashfs-tools libssl-dev ninja-build lunzip syslinux syslinux-utils gettext \
    genisoimage gettext bc xorriso libncurses5 xmlstarlet build-essential git \
    imagemagick lib32readline-dev lib32z1-dev liblz4-tool libncurses5-dev libsdl1.2-dev \
    libxml2 lzop pngcrush rsync schedtool python3-mako libelf-dev aapt zstd rdfind nasm \
    rustc bindgen
```

If you plan on building the kernel with the `NO_KERNEL_CROSS_COMPILE` flag, you will need to also have `gcc` and `g++` installed:

```bash
sudo apt-get install gcc-10 g++-10
```

### Installing Java 8
If a different version of the JDK is needed from what is available in the Ubuntu repositories, the PPA can be added:
```bash
sudo add-apt-repository ppa:openjdk/ppa
```

To install and configure Java:
```bash
sudo apt-get update && upgrade
sudo apt-get install openjdk-8-jdk
update-alternatives --config java  # (make sure Java 8 is selected)
update-alternatives --config javac # (make sure Java 8 is selected)
```

## Cloning the Repository

Initialize the local repository:
```bash
## Releases Repo ##
repo init -u https://github.com/BlissRoms-x86/manifest.git -b r11-x86 --git-lfs
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

To build with OpenGapps, first make sure to get `git-lfs` (already listed in above). Once you got `git-lfs`, run the command below to fetch the packages.
```bash
repo forall -c git lfs pull
```

## Setup aaropa Installer
From the root of the project directory, clone the aaropa repository into the `bootable` directory.
```bash
git clone https://github.com/BlissOS/bootable_aaropa.git bootable/aaropa
```

## Building BlissOS
--------

### Build ISO Image
```bash
. build/envsetup.sh
lunch bliss_x86_64-userdebug
make iso_img
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

### ChromeOS's libhoudini/Widevine DRM L3 

```bash
mkdir -p vendor/google
git clone https://github.com/supremegamers/android_vendor_google_chromeos-x86 vendor/google/chromeos-x86
cd vendor/google/chromeos-x86
./extract-files.sh
```

The variables to activate this is `USE_CROS_HOUDINI_NB=true` for libhoudini and `USE_WIDEVINE=true` for Widevine.

### Prebuilt Widevine from Windows Subsystem for Android
```bash
mkdir -p vendor/google/proprietary
git clone https://github.com/supremegamers/vendor_google_proprietary_widevine-prebuilt vendor/google/proprietary/widevine-prebuilt
```
To activate this, use the environment variable `USE_WIDEVINE=true`

### Windows Subsystem for Android's libhoudini 
```bash
mkdir -p vendor/intel
git clone https://github.com/supremegamers/vendor_intel_proprietary_houdini vendor/intel/proprietary
```
To activate this, use the environment variable `ANDROID_USE_INTEL_HOUDINI=true`

## Report build issues
You can reach us via [Telegram (Android™-Generic (x86 PC) Community Development)](https://t.me/androidgenericpc)

