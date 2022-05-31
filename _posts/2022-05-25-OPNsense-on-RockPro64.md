---
title: OPNsense on RockPro64 (and ARM64)
tags: opnsense sbc security project
---

___
This is a WIP and may not currently have complete info

___

# <b><u>Info</u></b>
Although not officially supported, OPNsense can be built for several arm64 devices using the [OPNsense tools](https://github.com/opnsense/tools). I maintain a publicly accessible [arm64 repo](https://opnsense.one8three.xyz/) with an image built for the RockPro64. Since OPNsense on arm64 is not officially supported, it should be considered experimental. Use it at your own risk.

Most of the information on this page does apply to any arm64 device running OPNsense but it is focused on the RockPro64. If you are running OPNsense on another arm64 device (built yourself or downloaded elsewhere), you can use the one8three repo for package updates/installations. However, I am unsure how often it will be updated.

## <b><u>Requirements</u></b>
  - RockPro64 
  - PCIe NIC - A list of confirmed compatible PCIe cards can be found [here](https://wiki.freebsd.org/arm/RockChip#Tested_PCIe_devices_on_RockPro64)
  - Boot media (flash drive, MicroSD card, USB SSD, eMMC, etc). 
    - 4GB bare minimum
    - 16GB recommended minimum 

A USB Ethernet adapter can likely be used instead of the PCIe NIC but is not recommended.

## <b><u>Note</u></b>
My RockPro64 image includes a few extras compared to the default OPNsense images:
  - vim
  - wireguard
  - iperf3
  - mdns-repeater
  - ddclient

# <b><u>Setup</u></b>
The moment you've all been waiting for!

## <b><u>1. Download</u></b>
  The latest build can be found here: [https://opnsense.one8three.xyz/opnsense/FreeBSD%3A13%3Aaarch64/22.1/images/](https://opnsense.one8three.xyz/opnsense/FreeBSD%3A13%3Aaarch64/22.1/images/)

## <b><u>2. Install</u></b>
  Write the image to a SD card or other boot media.

  [Etcher](https://www.balena.io/etcher/) is an easy and reliable way to write the image to your boot media. On MacOS or Linux, you can also just use `dd if=/path/to/OPNsense.img of=/dev/sdcard` 

## <b><u>3. Post-istall</u></b>
  After installing, you'll need to configure a few things. The initial configuration will be done by connecting an ethernet cable between the RockPro64 and another computer. In my case, the `igb0` port of my NIC was accessible at `192.168.1.1` with default username/password of `root/opnsense`. 

## <b><u>4. Configure the updates repo</u></b>
<b>4a.</b> SSH into OPNsense with root privileges (this will need to be enabled through the web UI at System > Settings > Administration). Run this command to accept the fingerprint
~~~    
curl https://opnsense.one8three.xyz/opnsense/fingerprint.txt -o /usr/local/etc/pkg/fingerprints/OPNsense/trusted/opnsense.one8three.xyz
~~~

<b>4b.</b> Back in the web GUI, go to System > Firmware > Settings. Change the Mirror setting to:
  - (Other)
  - https://opnsense.one8three.xyz/opnsense

## <b><u>5. Enable the system fan (if you are using one)</u></b>
This is specific to the RockPro64. SSH into the system with root privileges, make a new file and mark it as executable:
~~~
touch /usr/local/etc/rc.syshook.d/start/22-startfan
chmod +x /usr/local/etc/rc.syshook.d/start/22-startfan
~~~
Using your favorite text editor, insert the following text into that file
~~~
pwm -E -f pwmc1.0 -p 50000 -d 37000
~~~
Adjusting the second number will change the fan speed. Lower is faster.

## <b><u>6. Grow the filesystem</u></b>
This is necessary to use the full storage space of the drive you are using. SSH into OPNsense with root privileges and run the following command:
~~~
service growfs onestart
~~~
It's a good idea to reboot after this step.

## <b><u>7. Finish configuration!</u></b>
This part is up to you! Add some VLANs, configure a VPN, set up DHCP, DNS ad-blocking, etc...the OPNsense world is now yours!



___