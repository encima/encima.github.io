---
layout: post
title:  "Mounting Network Folders Resiliently in Linux"
date:   2019-11-11 13:05:14 +0100
categories: unix linux nas systemd service mount
author: encima
cover: ""
location: Zurich, Switzerland
description: 
---


# Network Drives, Linux, and You

We have all done it: `mount /dev/vdb1 /mnt/my_net_drive`. Easy, right? Type and forget. No no no, what about reboots? What about outages? What about permissions? `Fstab` will save us, sutrely? Again, no no no. But, Chris, you say, why are you preaching at me? I made that mistake, a network outage and a Kubernetes cluster fallover made me think I had lost nearly 1TB of imported images and backups. 

I go to `/mnt/my_net_drive` and see emptiness. I check `fstab` and reboot but nothing. No worries, I think, I will just reimport the data... Except those data are gone now, backed up onto a different network folder with the same issue...

`Fstab` has been my go to for years and I have not really had to worry about `production` in the real sense of the word. This situation was dangerously close to a real production issue and I had zero time. This is where `systemd` and `automount` saved my bacon.

## Creating a Network Mount in Systemd

Here, we need 2 things: 

1. The mount itself
2. The automounting service

### The Mount

Just look at that syntax, beautiful, is it not? So simple.

```
[Unit]
Description=Data Dir on Network

[Mount]
What=/dev/vdb1
Where=/mnt/my_net_drive
Type=ext4
Options=defaults

[Install]
WantedBy=multi-user.target
```

Create this file in `/etc/systemd/system/mnt-my_net_drive.mount` (using the path of the mount as the file name)

### The Automounting Service

```
[Unit]
Description=Automount Data Dir
Requires=network-online.target
After=network-online.service

[Automount]
Where=/mnt/my_net_drive

[Install]
WantedBy=multi-user.target
```

This file is saved in `/etc/systemd/system/mnt-my_net_drive.automount`

### Mounts Away!

First, make sure you remove your `fstab` entries and **unmount** your drive(s). Now, all you need to do is `systemctl daemon-reload` and `systemctl enable mnt-my_net_drive.automount`, followed by `systemctl start mnt-my_net_drive.automount`.

Boom, done. Check your drive is there and your files are there. Now this drive will be resilient to network outages and will remount when the network comes back up.
