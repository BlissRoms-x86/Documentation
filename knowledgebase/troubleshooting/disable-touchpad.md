# How to disable touchpad
Accidental touches due to palm contact with the touchpad can be quite frustrating. Fortunately, the Linux kernel allows you to disable the touchpad or any input device temporarily using sysfs by triggering the uevent. This guide will walk you through the process.

### Step 1: Access a Root Shell
In order to disable the touchpad, you'll need to access a root shell. Here's how to get root privileges.

1. **Open KernelSU:**
   - From the app drawer, locate and open **KernelSU** app.
   
2. **Enable Superuser for Termux:**
   - In the KernelSU interface, find **Termux** in the list of installed apps.
   - Enable superuser privileges for Termux.

### Step 2: Launch Termux and Switch to Root User

1. **Open Termux:**
   - **Termux** app is pre-installed in Bliss OS.

2. **Open a root shell:**
   - In Termux, type `su` and press **Enter** to open a root shell.

### Step 3: Identify the device file of the touchpad and trigger the uevent 

To disable the touchpad, we need to identify which event device corresponds to it. There are two ways to do this:

#### Method 1: Using `getevent -pl`
1. In the superuser shell, run the command:  
   ```bash
   getevent -pl
   ```
2. This command will list all input devices attached to the system along with their event IDs. Look for the touchpad device by identifying the descriptions, usually labeled as something like "touchpad".

3. Once you've identified the correct event, note down the event ID (e.g., **eventX**, where X is the number associated with the touchpad).

4. To disable the touchpad, execute the following command:  
   ```bash
   echo remove > /sys/class/input/eventX/uevent
   ```
   Replace **X** with the appropriate number identified earlier.

#### Method 2: Using `getevent -ql`

1. Alternatively, you can use the following command:  
   ```bash
   getevent -ql
   ```
2. Tap on the touchpad, and the command will display input events associated with that touchpad.
3. Once you've determined the correct event number **X** (eventX), use the same command to disable it:  
   ```bash
   echo remove > /sys/class/input/eventX/uevent
   ```


Congratulations, now you should have successfully disabled your laptop's touchpad. Should you need to re-enable the touchpad, reboot the system or trigger the uevent to re-add the device:
   ```bash
   echo add > /sys/class/input/eventX/uevent
   ```
