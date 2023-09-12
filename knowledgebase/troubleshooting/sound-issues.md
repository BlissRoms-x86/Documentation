# Sound Issues

When running our builds on real hardware, sometimes the sound is not detected properly. We can troubleshoot this issue a number of ways. 

**Option 1**

The initial way we should troubleshoot is to first see if it is a volume issue. Typically, you can install an app like Goodev Volume Booster, and that will add gain to the main sound channels. 

1. Open Play Store
2. Install "Volume Booster GOODEV" or any other sound booster app
3. Open the AudioFX app if installed and turn it off
4. Go to Home, open the Volume Boost app and boost it to 15%-25% and test

This will solve most sound issues.

**Option 2**

Using the alt-f1/f7 console from Android 9 or a terminal app from Android 11/12, we can run:

```text
$ lsmod
```

This will list all the devices that have been detected on the device. Search the output for anything sound related. If you see multiple audio or sound drivers loaded, refer to Option 1 to boost gain, otherwise you can reload the modules:

```text
rmmod snd_hda_codec_hdmi
modprobe snd_hda_codec_hdmi
```

**Option 3** 

There is also tools added to Gearlock Recovery that can help with most sound issues. Read the details about that [here](https://supreme-gamers.com/threads/how-to-fix-mic-sound-issues-in-phoenixos-darkmatter.62/page-2).

**Option 4 - \(Advanced\)**

For some users, the simple sound fixes don't work. This means we will need to go into experimental mode on the issue. This method was provided by cjeu100 From XDA-Developers threads.

Start by using the alt-f1 console \(use alt-f7 to get back\) or the Terminal app \(may require you to run [Remount System as Read/Write](https://docs.blissos.org/troubleshooting/remount-system-as-read-write) first\) and type following commands:

```text
su
cat /proc/asound/cards
```

`0 [HDMI ]: HDA-Intel - HDA Intel HDMI HDA Intel HDMI at 0xb0610000 irq 46 1 [PCH ]: HDA-Intel - HDA Intel PCH HDA Intel PCH at 0xb0614000 irq 44`

```text
ls -l /dev/snd
```

now you see your sound devices

In this example, the we picked the top one, \(pcmC0D7\). If that doesn't work, repeat these steps and choose a different card.

On Bliss OS builds < 15.8.8:

```text
setprop hal.audio.out pcmC0D7p
pkill audioserver
```

On Bliss OS builds <= 15.8.8:

```text
snd-intel-dspcfg.dsp_driver=1
pkill audioserver
```

This will restart the audio server and hopefully result in sound working with the right output. Use alt-f7 to get back to the Android UI and test.

