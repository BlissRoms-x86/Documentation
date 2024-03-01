# Updating BlissBass Go

We support a variety of methods for updating Bliss Bass builds. Our installwe is simular to Bliss OS, but we add a few product focused options and features. 


### Manually - USB Installer

When installed this way, you can easily update to the newest versions of your Bliss Bass by running the USB installer and selecting the EXT4 partition, and select Do Not Format. This will retain the data from the previous install. 


### Manually - USB OEM Update

If you have installed the OS using the Bootable USB installer, then you can also update the OS manually by inserting the USB with the new version of BlissBass on it, and on Grub boot menu, navigate to OEM Install > OEM Update

Once selected, the device will boot into the OEM installer and auto-update the existing OS on the device. 


### OTA - Local Server

By default, testing OTA on an unsecured HTTP server is disabled. In order to test OTA, you will need to setup a secured HTTPS nginx server, or change this line in the Updater package to **True**: [https://github.com/BlissRoms/platform_packages_apps_BlissUpdater/blob/arcadia-next/AndroidManifest.xml#L23](https://github.com/BlissRoms/platform_packages_apps_BlissUpdater/blob/arcadia-next/AndroidManifest.xml#L23)  \
Then recompile BlissBass to disable this security measure. 


#### Manually setting OTA URI address:

We do allow the ability to manually set the OTA update URI via kernel cmdline interface. This allows you to set the value only when needed for added security. To do so, you should add the following value:



* `SET_CUSTOM_OTA_URI` - Sets the custom URL for OTA updates


```
SET_CUSTOM_OTA_URI=https://192.168.1.1/updates/update.json
```


Or you can set the following system property via ADB:



* `bliss.updater.uri`


```
Setprop bliss.updater.uri https://192.168.1.1/updates/update.json
```


Setting Up The Server:

On the server, we will need to have it setup as a basic nginx web server, with the OTA update .zip located in the same folder as the update.json, with the updates/update.json file formatted like this:


```
{
   "response": [
       {
           "datetime": 1698795869,1698793200
           "filename": "Bliss-Go-v15.8.6-x86_64-OFFICIAL-vanilla-20231031.zip",
           "id": "f017547c509f01b885792faad33b38e2d30380907454e723dcbb257189b8e80d",
           "size": 1194109016,
           "version": "v15.8",
           "variant": "vanilla",
           "url": "https://192.168.1.1/Bliss-Go-v15.8.6-x86_64-OFFICIAL-vanilla-20231031.zip"
       }
   ]
}
```


The values are as follows:



* “**datetime**” - is calculated using the date command. Example: 


```
$ date --date='2023-10-31 19:00' +"%s"

```



* “**id**” -  is the sha256 value. Example:


```
$ sha256sum Bliss-Go-v15.8.6-x86_64-OFFICIAL-vanilla-20231031.zip

```



* “**size**” - is the size in bytes. Example: 


```
$ wc -c Bliss-Go-v15.8.6-x86_64-OFFICIAL-vanilla-20231031.zip
```



### OTA - Dedicated Update Server

**ShipperStack**

BlissLabs has produced [ShipperStack](https://github.com/shipperstack/shipper/tree/master/docs), and that is what we use for our update server needs. You can find more information on how to setup and host on the project documentation page. 

**LineageOS Updater**

The updater we use is also compatible with the LineageOS updater framework. You can also find a version of that that is configured for local web servers as well [here](https://git.libremobileos.com/infrastructure/updater)
