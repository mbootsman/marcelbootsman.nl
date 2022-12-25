---
title: How to solve Home Assistant '400 Bad Request' error with Synology Reverse Proxy
author: Marcel Bootsman
categories:
- Home Assistant
cover: 
  image: "images/home-assistant-bad-request-synology.png" # image path/url
  alt: "Home Assistant logo and text with all sorts of devices on the background" # alt text
  caption: "" # display caption under cover
  relative: true # when using page bundles set this to true
  hidden: false # only hide on current single page
date: "2022-12-25T22:09:00+01:00"
draft: false
summary: When you want to run Home Assistant from behind a reverse proxy, running on a Synology, you might encounter a '400 Bad Request' error. Here's how to solve it.
showToc: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
---

## Some assumptions
1. You have Home Assistant running nicely on your internal network.
2. You have a Synology Diskstation running.
3. You have a (sub)domain pointing to your home network IP / Synology.
4. You have already created a Reverse Proxy rule for Home Assistant.


## Edit Reverse Proxy rule for Home Assistant 
Since you already made a Reverse Proxy rule for your Home Assistant server, it is reachable from outside your internal network, but you get that error message. Here's how to solve it.

## Step 1
Open Synology DSM and navigate to Control Panel -> Login Portal -> Advanced. There click on the Reverse Proxy button.
You should see your Reverse Proxy rule for Home Assistant, select it and click Edit.

## Step 2
In the modal window click on Custom Header. Now click the Creat button and select WebSocket.
{{< figure src="images/web-socket.png" caption="" alt="Adding WebSocket headers to the Reverse Proxy rule">}}
Now you should have these two headers added to the configuration:
```
Upgrade: $http_upgrade
Connection: $connection_upgrade
```

{{< figure src="images/web-socket-created.png" caption="" alt="Adding WebSocket headers to the Reverse Proxy rule">}}

## Step 3
Since we now have the Reverse Proxy configration set up, we need to tell Home Assistant that a trusted proxy is used. [Read more on this in the Home Assistant documentation](https://www.home-assistant.io/integrations/http/). 

Add the following to your `configuation.yaml` file:

```
http:
  use_x_forwarded_for: true
  trusted_proxies:
    - xxx.xxx.xxx.xxx # IP of Synology
```
Now restart Home Assistant, and all should be working!