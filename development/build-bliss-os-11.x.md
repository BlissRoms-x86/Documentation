# Build Bliss OS 11.x

## Introduction

This is the official guide to build Bliss OS for PCs. In this guide, we will only cover building for x86 & x86\_64 devices. We will also go over the details of using the patch system for testing and recompiling a build with a different kernel branch.

The golden rule to building is patience. If something breaks, pay attention to the console output or take logs of the issue and ask for guidance in our build support chat.

Let’s get started.

## Preparation

To get started, you need a computer with Ubuntu 18.04 \(LTS\), at least 200GB space of HDD, and at least 8GB RAM. A decent CPU \(or CPUs if you have a server motherboard\) is recommended. Other distros can work but is not officially supported in this guide.

Underpowered machines may crash during compilation. If that happens, you may try and restart the build as most crashes are caused by lack of memory. If your storage space has run out, then you will need to build on a different hard drive.

## Install the JDK

Install OpenJDK:

```text
sudo apt install openjdk-8-jdk
```

## Install build tools

To install the required build tools, run the following command:

```text
sudo apt-get install git-core gnupg flex bison maven gperf build-essential zip curl zlib1g-dev gcc-multilib g++-multilib libc6-dev-i386  lib32ncurses5-dev x11proto-core-dev libx11-dev lib32z-dev ccache libgl1-mesa-dev libxml2-utils xsltproc unzip squashfs-tools python-mako libssl-dev ninja-build lunzip syslinux syslinux-utils gettext genisoimage gettext bc xorriso libncurses5
```

## Install source code tools

Now we need to get the source code via a program named `repo`, made by Google. The primary function of `repo` is to read a manifest file located in Bliss OS's GitHub organization, and find what repositories you need to actually build Android.

Create a `~/bin` directory for `repo`:

```text
mkdir -p ~/bin
```

The `-p` flag instructs `mkdir` to _only_ create the directory if it does not exist in the first place. Now download the `repo` tool into `~/bin`:

```text
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
```

Make `repo` executable:

```text
chmod a+x ~/bin/repo
```

And add it to PATH:

```text
nano .bashrc
```

Scroll to the end of the file and type these lines:

```text
# Export ~/bin
export PATH=~/bin:$PATH
```

Ctrl-O and enter to save, then Ctrl-X to exit nano. Now either logout and login again \(or reboot\), or `source` the file:

```text
source .bashrc
```

Which can be shortened to:

```text
. .bashrc
```

### What is `source`?

`source` is a `bash` command to read aliases, functions, and commands from the specified file. Typically, you'll supply `bash` with a configuration file such as `.bashrc` or `.bash_profile`, or an initialization file such as `envsetup.sh`. The difference is that while the configuration file lists configuration and user-defined aliases and functions, initialization files typically hold build commands such as `breakfast`, `brunch`, and `lunch`. Without those commands building would be significantly harder as you would have to memorize the long command to invoke a build manually!

But why do you need to run it after modifying a file? Well, `bash` cannot automatically detect changes in our files. To solve this, we either `source` it or log out and log back in, forcing `bash` to reload configuration files. Keep this in mind, because unlike configuration files, if you forget to `source` initialization files, build commands will not function!

### What if I need `repo` globally?

If you need the `repo` tool to be available anywhere, you will need to first download `repo` to your home directory, move it with `sudo` and give it executable permissions. The exact commands are as follows:

```text
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/repo
sudo mv ~/repo /usr/bin/
chmod a+x /usr/bin/repo
```

`repo` will now work anywhere, without any `.bashrc` modifications. However, these steps aren’t recommended as `repo` might become a security risk if a vulnerability is found.

Now we’re ready to download the source code.

## Download

Create a directory for the source:

```text
mkdir -p ~/blissos/p9.0
cd ~/blissos/p9.0
```

Before we download, we need to tell `repo` and `git` who we are. Run the following commands, substituting your information:

```text
git config --global user.email “randy.mcrandyface@hotmail.net”
git config --global user.name “Randy McRandyface”
```

Now, we’re ready to initialize. We need to tell `repo` which manifest to read:

```text
repo init -u https://github.com/BlissRoms-x86/manifest.git -b p9.0-x86
```

`-b` is for the branch, and we’re on `p9.0-x86`, Android Pie. It’ll take a couple of seconds. You may need to type `y` for the color prompt.

Sync repo :

```text
repo sync -j24 -c --no-tags --no-clone-bundle
```

Problems syncing? :

```text
repo sync -j4 -c --no-tags --no-clone-bundle --force-sync
```

`-j` is for threads. Typically, your CPU core count is your thread count, unless you’re using an older Intel CPU with hyperthreading. In that case, the thread count is double the count of your CPU cores. Newer CPUs have dropped hyperthreading unless you have the i9, so check how many threads you have. If you have four threads, you would run:

```text
repo sync -j4 -c
```

`-c` is for pulling in only the current branch, instead of the entire history. This is useful if you need the downloads fast and don’t want the entire history to be downloaded. This is used by default unless specified otherwise.

`repo` will start downloading all the code. That’s going to be slow, even on a fiber network. Expect downloads to take more than a couple hours.

{% hint style="info" %}
To find out how many CPU threads you have, run `nproc`.
{% endhint %}

## Easy build instructions

This will build an x86 based .ISO for PCs.

Usage: `$ bash build-x86.sh options buildVariants blissBranch extraOptions` Options:

```text
-c | --clean : Does make clean && make clobber and resets the efi device tree
-s | --sync: Repo syncs the rom (clears out patches), then reapplies patches to needed repos
-p | --patch: Just applies patches to needed repos
-r | --proprietary: build needed items from proprietary vendor (non-public)
```

BuildVariants:

```text
android_x86-user : Make user build
android_x86-userdebug |: Make userdebug build
android_x86-eng : Make eng build
android_x86_64-user : Make user build
android_x86_64-userdebug |: Make userdebug build
android_x86_64-eng : Make eng build
```

BlissBranch:

```text
select which bliss branch to sync, default is p9.0
```

ExtraOptions:

```text
foss : packages microG & FDroid with the build
go : packages Gapps Go with the build
gapps : packages OpenGapps with the build
gms : packages GMS with the build (requires private repo access)
none : force all extraOption flags to false.
```

To start, you must first use the -s \(--sync\) flag, then on following builds, it is not needed. Initial generation of the proprietary files from ChromeOS are also needed on the first build. We are able to use the -r \(--proprietary\) flag for that. **This step needs to be on its own because the mounting process requires root permissions, so keep a look out for it asking for your root password**.

First you must sync with the new manifest changes:

```text
bash build-x86.sh -p
```

This will do initial patching. Some of the patches will show as `already applied` or `conflict`. This is normal behavior and will not effect the build process if you continue to the next step without addressing any of the conflicts.

**The only times you should worry about the conflicts is when you are adding or changing patches in `vendor/x86`**.

Next step is to download the proprietary files from ChromeOS:

```text
mkdir vendor/bliss_priv/proprietary
mkdir vendor/bliss_priv/source
bash build-x86.sh -r android_x86_64-userdebug
```

After that, you can build your release file:

```text
bash build-x86.sh android_x86_64-userdebug (to build the userdebug version for x86_64 CPUs)
```

## Advanced build instructions

Set up the build environment:

```text
. build/envsetup.sh
```

This is the initialization file we talked about earlier up top. This "initializes" the environment, and imports a bunch of useful build commands required to build your device. Again, you need to remember to `source` this file every time you log out and log back in, or launch a new `bash`/Terminal instance.

Define what device you’re going to build. For example, the Nexus 5X, has a codename of `bullhead`. You can check your specific device's codename on GitHub or on Google. Execute:

For 32 bit devices:

```text
lunch android_x86-userdebug
```

For 64 bit devices:

```text
lunch android_x86_64-userdebug
```

Let's break down the command. `lunch` initializes the proper environmental variables required for the build tools to build your specific device. Things like `BLISS_DEVICE` and other variables are set in this stage, and the changed variables will be shown as output. `x86` or `x86_64` is the specific device we are building. Finally, `userdebug` means that we will build a user-debuggable variant. This is usually what most ROMs use for publishing their builds. Manufacturers typically use `user` which disables most of the useful Android Logcats.

### My device isn't booting, and `userdebug` won't print any `adb logcat`s. What gives?

There is a third build variant called `eng`, short for engineering builds. These builds will activate `adb logcat` during boot, and will show you exactly what is going wrong, where, and why. However, these builds are **NOT** recommended for normal usage as they are not securely hardened, have log spam that will slow down your device, and other unexpected problems like userspace utilities crashing during runtime. If you want to submit your device for mainline, do **NOT** submit an `eng` build!

If this is the first time you're running the build, you're going to want to run the proprietary build command first from the easy build instructions. Alternatively, you could also run those commands manually.

```text
mkdir vendor/bliss_priv/proprietary && mkdir vendor/bliss_priv/source
```

Then:

```text
lunch android_x86_64-userdebug
mka update_engine_applier
mka proprietary
```

After that is complete, we can start the main building process. Run:

`mka iso_img`

And the build should start. The build process will take a long time. Prepare to wait a few hours, even on a decent machine.

### Why `mka` and not `make`?

`make` only runs with 1 thread. `mka` is properly aliased to use all of your threads by checking `nproc`.

If you want to customize your thread count \(maybe you're building with a fan-screaming laptop in a quiet coffee shop\), use `make -j#`, replacing the hash with the number of threads \(example of `make -j4`\).

## After building

There are two outcomes to a build - either it fails and you get a red error message from `make`, or it succeeds and you see the Bliss logo in ASCII. If you encounter the former, you need to go back and fix whatever it's complaining about. Typically, 90% of the time the problem will be in your device tree. For the other 10%, submit a bug report to the ROM developers. Be sure to include the full log of your build to help diagnose the problem, and your device tree.

If you face the latter, congratulations! You've successfully built Bliss ROM for your device. Grab the artifacts for your device:

```text
cd out/target/product/x86_64/
```

In here, you’ll find an `.iso` that goes along the lines of `Bliss-v11.9--OFFICIAL-20190801-1619_x86_64_k-k4.9.153_m-18.3.5-pie-x86-llvm80_f-dev-kernel.org.iso`.

## Changing the target kernel branch

Sometimes, you might be working on a device that requires a different kernel branch. In order to switch kernels effectively on Bliss OS, they need to be compiled with the OS at build time.

### Switching the kernel branch

Start off by entering the kernel folder

```text
cd kernel
```

Then pull all the available kernel branched from your target repo. In this case, we are using the default `BR-x86` repo

```text
git fetch BR-x86
```

Then after that is finished, we need to checkout our target branch, and in this example we are choosing our `k4.19.50-ax86-ga` branch, which has added commits from the GalliumOS project for Chromebooks

```text
git checkout BR-x86/k4.19.50-ax86-ga
```

Next step is to clean out any configs or generated files from the previously checked out kernel. To do this, run these commands

```text
make clean
make mrproper
```

Once that is done, we can `cd` back to our main project folder

```text
cd ..
```

And run our build command again to generate the `.iso` with the target kernel we selected

```text
rm -rf out/target/product/x86_64/kernel
mka iso_img
```

## Using the patch system for testing

We use a patching method we adapted for Bliss from Intel's Project Celadon & phh-treble. This patching system allows us to bring in a good number of commits to add to the OS, and test how they apply or if there are any conflicts.

Our intention was to make a system that can add all the needed x86/x86\_64 commits to Bliss ROM, as well as other ROMs too.

The majority of this system is found in `vendor/x86/utils`.

From here, you simply generate the `.patch` files from your additions, and add them to the mix. In the following example, we are going to generate patches from `packages/apps/Settings` and add them to the proper folder for live testing.

From your Project folder:

```text
cd packages/apps/Settings
```

And generate your `.patch` files. For this example, we've added four commits on top of what was there after sync

```text
git format-patch -4
```

Then copy those files to the proper folder in `vendor/x86`. In this case, you will find it here:

```text
vendor/x86/utils/android_p/google_diff/x86
```

After that is complete, you can `make clean` and run the patch system from your main project folder.

```text
make clean
bash build-x86.sh -p
```

This should start patching all the source files. Once that is complete, you can rebuild.

## Troubleshooting

If your build failed, there are a couple things you can try.

* Try a fresh `repo sync` to make your repository up to date. Sometimes, the Internet connection between you and GitHub can be flaky. In rare cases a commit merge might be ongoing, and you might've grabbed an incomplete merge. Mostly, this should fix the issue 70% of the time.
* Make sure your dependencies are installed correctly. Error messages help out a lot here! Often it will say `shared/linked library not found` or something along those lines.
* Make sure you sourced `build/envsetup.sh`. This is especially common and worth suspecting if none of the build commands like `breakfast` and `lunch` work. If you have `repo sync`ed do this again.
* Make sure you’re at the root of the build tree. Again, to quickly jump there, use `croot`.
* Make sure your computer itself isn’t faulty. HDDs usually die first, followed by RAM. SSDs rarely die but failure is not unheard of.  In extremely rare cases, your CPU may have a defect. If you're unsure, run a stress test using a program like Prime95.

If something goes wrong and you've tried everything above, first use Google to look up your error! Most of the errors you encounter is due to misconfiguration and wrong commands entered. More often than not, Google will have the answer you are looking for. If you're still stuck and nothing fixes the problem, then ask us via our Telegram Build Support chat.

## Conclusion

Building a ROM is very hard and tedious and the results are very rewarding! If you managed to follow along, congratulations!

After you finish building, you can try out the Git Started guide. Make changes, commit, and send them off to our GitHub for Bliss OS repos & our Gerrit for review on Bliss ROM repos! Or better yet, download experimental commits not ready for the mainline repositories and review them! Again, ROM building is a fun project you can work with. I hope this guide was a lot of fun to run through!
