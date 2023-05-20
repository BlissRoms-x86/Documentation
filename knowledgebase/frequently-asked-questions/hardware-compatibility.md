# Hardware Compatibility

**Supported CPU's:**

Intel:

* Intel i Series \(i3/5/7/9\) - Fully Supported
* Intel Celeron M - Fully Supported \(Kernel 5.4+ recommended\) 
* Intel Atom - Mostly Supported \(Kernel 5.10+ recommended\)
* Core2Duo - Not Fully Supported \(Needs SSE4.2\) \(May require 32bit builds\)

AMD:

* A Series - Mostly Supported \(Needs SSE4.2\) \(Kernel 5.10+ recommended\)
* Ryzen Series \(1k-7k\) - Fully Supported \(Kernel 5.10+ recommended\)
* Athlon Series - Mostly Supported \(Needs SSE4.2\) \(Kernel 5.10+ recommended\)

**Supported GPU's:** 

* Intel / AMD iGPU - all supported + Vulkan \(Iris requires Mesa 21.1.3+\)
* AMD Desktop GPUs - mostly supported + Vulkan 
* Nvidia Desktop GPUs - poor support - no vulkan \(Uses Nouveau drivers\)

**Native-Bridge:**

Native-Bridge is an ARM translation layer for android x86 developed by Intel and Google to run ARM apps on x86 architecture.  
  
How to identify Native-Bridge Types in ISO file ?

* **houdini** - Includes Intel's Houdini \(Works with most Intel CPU's and some recent AMD CPU's\)
* **libndk** - Includes Google's libndk-translation \(Works on all CPU's, but not as efficient as Houdini\) 

