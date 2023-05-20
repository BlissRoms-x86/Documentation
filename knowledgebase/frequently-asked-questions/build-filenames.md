# Build Filenames

The filenames we use contain some important information about what all is included in the builds.

BlissOS-\(**Version**\)-\(**Device Type**\)-\(**Build Date**\)\_k-\(**Kernel**\)\_m-\(**Mesa**\)\_\(**App Services**\)\_\(**Extras**\).iso

**Version:**

This will be Bliss OS 11.x or 14.x, depending on what version of Android we are compiling.

**Device Type:**

* x86/x86\_64 :- Device Type 32bit/64bit 

**Build Date:**

This will be the julian date-time from when the compile started. 

**Kernel & Mesa:**

* \_k-xxxxx :- Kernel Branch - ex: k-kernel-5.4 
* \_m-xxxxx :- Mesa Branch - ex: m-r-x86 \(r/r-x86 means stock Mesa branch in current manifest\) 

**App Services:**

Stock - Normally barebones, minimal apps added. Perfect for product testing  
FOSS - Includes Free and Open Source app store solutions  
Gapps/GMS - Includes Google Play Services

* foss/gms/emugapps :- Apps Type 

**Extras:**

Sometimes we will include extras into the builds, and that identifier will show in the last section. 

Our Native-Bridge variations are also added to this section. 

* houdini/libvndk :- Native-Bridge Type

