---
title: How to connect bluetooth headphones automatically in Ubuntu
author: Marcel Bootsman
categories:
- Ubuntu
cover: 
  image: "images/headphones.jpg" # image path/url
  alt: "" # alt text
  caption: "" # display caption under cover
  relative: true # when using page bundles set this to true
  hidden: false # only hide on current single page
date: "2022-12-24T13:49:00+01:00"
draft: false
summary: Bluetooth headphones and Ubuntu, they just don't get along. Here's how to fix auto-connect for your Bluetooth headphones.
showToc: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
---

## Step 1 ##
Make sure your Bluetooth is working.

## Step 2 ##
Make sure AutoEnable is turned on, which makes sure Bluetooth is turned on after every restart

Check if `AutoEnable=true` is present in your Bluetooth config in `/etc/bluetooth/main.conf`. If it isn't add it at the end of the file.

## Step 3 ##

Trust your device. Well, have Ubuntu trust your device to be precise. To do that we have to add the device to the trusted list. We need the MAC address of the Buetooth device and we can get that by going to the Bluetooth settings, look up your device, and click on it.

{{< figure src="images/bluetooth-settings-mac-address.png" caption="" alt="Bluetooth settings for the headset.">}}

There you will find the Address of the device.
Now open a Terminal and execute the following, where you replace `mac_address` with the Address from your Bluetooth settings: 
```
bluetoothctl trust mac_address
```

To verify it is now trusted, execute the following, again replacing `mac_address` with the Address from your Bluetooth settings:
```
sudo bluetoothctl info mac_address
```

You should see `Trusted: yes ` in the output of the command.

## Step 4 ##

Now disconnect your headphones by turning it off. And turn it on again. Your headphones should be connecting automatically now.

## Step 5 ##

Enjoy your music, movie, or whatever you are listening to on your headphpones.