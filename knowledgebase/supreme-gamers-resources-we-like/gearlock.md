# Gearlock

## Gearlock Info

* Gearlock Developer Documentation: [https://supreme-gamers.com/gearlock/](https://supreme-gamers.com/gearlock/) 
* Gearlock-dev-kit git link \(for cloning\): [https://github.com/axonasif/gearlock-dev-kit](https://github.com/axonasif/gearlock-dev-kit) 
* Gearlock-kernel-pkg: [https://github.com/axonasif/gearlock-kernel-pkg](https://github.com/axonasif/gearlock-kernel-pkg) 
* Gearlock \(main repo for the new vendor/gearlock used in Bliss OS 11/14/AG 11 builds\): [https://github.com/axonasif/gearlock](https://github.com/axonasif/gearlock)

### Supported Gearlock Extensions

Bliss OS devs do occasionally create Gearlock Extensions also, and those can be found here: [https://sourceforge.net/projects/blissos-dev/files/Android-Generic/PC/gearlock\_extensions/](https://sourceforge.net/projects/blissos-dev/files/Android-Generic/PC/gearlock_extensions/)

You can also find more extensions on the [https://supreme-gamers.com/r/](https://supreme-gamers.com/r/) site. Not all of those are compatible with our versions of Android, so please read carefully. 

#### Gearlock Kernel Commands

\(these are passed through Grub CLI\)

Gearlock kernel command lines: 

* NOSC=0 Disables supercharging 
* NOGFX=0 Skips loading Gearlock GPU drivers at recovery-mode \(needed for vulkan and old-modprobe modes\) 
* NORECOVERY=0 Disables recovery countdown 
* ALWAYSRECOVERY=0 Enters Recovery by default

To disable bootsound you have to use gearlock &gt; more &gt; settings &gt; bootsound Or you can also use your own by editing /\[data\] \[system\] /ghome/.gearlockrc

