# Internet Restriction (DNS)

In some cases, the system is equipped with an internet restriction method that is active while the device is in Lockdown mode.


### Updating the DNS configuration

In order to configure what websites and IP addresses are allowed to be accessed, we use a DNS configuration that follows the server rules found in Googles [dnsmasq.conf example](https://android.googlesource.com/platform/external/dnsmasq/+/refs/heads/main/dnsmasq.conf.example) . By default, the blissdns.conf file contains the following settings:


```
no-resolv
server=1.1.1.1

# Add domains here:
server=/github.com/1.1.1.1
server=/blissos.org/1.1.1.1
server=/vendorname_update.blisscolabs.dev/1.1.1.1
server=/yourcompanysite.com/1.1.1.1
server=/yourcustomprovidersite.com/1.1.1.1

address=/#/127.0.0.1
```
