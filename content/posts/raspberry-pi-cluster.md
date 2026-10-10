---
author: ["cmsheehan"]
title: "Building a Raspberry Pi Kubernetes Cluster"
date: "2026-01-08"
description: ""
tags: ["kubernetes", "pi", "docker"]
ShowToc: true
TocOpen: false
cover:
  image: "images/pi-3.png"
---

I wanted to experiment with Kubernetes in my free time. My first thought was a managed service like Amazon EKS, but at roughly $72 per month for the control plane alone, that was overkill for a hobby project.

So I looked at running my own cluster on hardware I owned. I already had a Raspberry Pi sitting in a drawer, and this was a good reason to put it to work.

## The inspiration

While researching, I found Anthony Simon's article [Build a Kubernetes Cluster with Raspberry Pi](https://anthonynsimon.com/blog/kubernetes-cluster-raspberry-pi/). What I liked most was the idea of a fully isolated, portable cluster that is small, self-contained, and easy to move around.

If this project interests you, read Anthony's article first. He explains the concepts more thoroughly than I do here. This post is mostly a record of my own experience following his instructions.

## What I built

![The Cluster](/images/pi-cluster-1.jpeg)

I wanted a three-node Kubernetes cluster that I could:
- Power through a single PoE switch to minimize cable clutter
- Use on any network via a portable router
- Run without worrying about cloud costs

## Bill of materials

| Item | Quantity | Notes |
|------|----------|-------|
| Raspberry Pi 4 (8GB RAM) | 1 | Control plane node |
| Raspberry Pi 4 (4GB RAM) | 2 | Worker nodes |
| 128GB Micro SD Card (Class 10) | 1 | |
| [Amazon Basics 64GB MicroSDXC Cards](https://www.amazon.com/dp/B08TJTB8XS) | 2 | |
| [uni SD Card Reader](https://www.amazon.com/dp/B081VHSB2V) | 1 | |
| [GeeekPi 6-Layer Cluster Case](https://www.amazon.com/dp/B085XTDDKD) | 1 | |
| [LoveRPi PoE HATs](https://www.amazon.com/dp/B07XB5PR9J) | 3 | Power + network in one cable |
| [TP-Link TL-SG1005P PoE Switch](https://www.amazon.com/dp/B076HZFY3F) | 1 | |
| [TP-Link AC750 Portable Travel Router](https://www.amazon.com/dp/B07V2R7W11) | 1 | Creates isolated network |
| 6-inch CAT6 Ethernet cables | 5 | |

I already owned the 8GB Raspberry Pi 4 from a CanaKit bundle I bought years ago, so it became the control plane node. The 4GB Pis have enough resources for most hobby workloads and cost less. My build is based on Anthony's but isn't an exact copy: the cluster case, SD cards, and PoE HATs are different.

## Assembling the hardware

I unpacked the Pis, attached the heat sinks, and installed the PoE HATs.

<div class="pi-row">
  <img src="/images/pi-1.png" class="pi-img">
  <img src="/images/pi-2.png" class="pi-img">
  <img src="/images/pi-3.png" class="pi-img">
</div>

Next I built the cluster case, which holds one Pi per layer. Then I connected the router and network switch and powered everything on for the first time.

![Assembled Cluster](/images/pi-cluster-2.png)

## Flashing the OS image

I used the **Raspberry Pi Imager** app and a uni SD card reader to flash the latest **Ubuntu Server LTS** onto each microSD card.

![Image OS](/images/image-builder-1.png)

As in Anthony's article, I used a `user-data` file to pre-configure each node. It lets you:

* Set a custom hostname for each device.
* Enable SSH access.
* Install packages automatically.

Here's an example of the `user-data` configuration I used:

```yaml
#cloud-config
hostname: node-1
manage_etc_hosts: true
packages:
  - avahi-daemon
apt:
  conf: |
    Acquire {
      Check-Date "false";
    };
timezone: America/New_York
keyboard:
  model: pc105
  layout: "us"
enable_ssh: true
users:
  - name: pi
    groups: users,adm,dialout,audio,netdev,video,plugdev,cdrom,games,input,gpio,spi,i2c,render,sudo
    shell: /bin/bash
    lock_passwd: false
    passwd: <replace>
    ssh_authorized_keys:
      - <replace>
    sudo: ALL=(ALL) NOPASSWD:ALL
runcmd:
  - [ rfkill, unblock, wifi ]
  - [ sh, -c, "for f in /var/lib/systemd/rfkill/*:wlan; do echo 0 > \"$f\"; done" ]
```

After flashing, I ejected the card, then remounted it to update the `user-data` file. On macOS it's at:

```
/Volumes/system-boot/user-data
```

I repeated this for all three nodes.

## Configuring the router

Anthony's article documents this part well, including why it's configured the way it is. Here are the detailed steps for this particular router on a Mac.

* **Initial Setup**

  * Follow the instructions that came with your router and plug it into your gateway/modem.
  * Find the router’s IP address:

    * Connect to the router's wifi
    * Go to **System Settings → Network**
    * Select **Ethernet** (or **Wi-Fi**) → **Details… → TCP/IP**
    * Note the router IP and open it in a web browser

* **Secure Your Router**

  * Generate a new **router admin password**
  * Optionally, update your **Wi-Fi password** under **Wireless Security**

    * Update both 2.4 GHz and 5 GHz networks or just one (I’m using only 5 GHz)
    ![router-1.png](/images/router-1.png)

* **Connect Your Devices**

  * Navigate to **Basic Settings → Client Settings → Scan**
  * Connect to your Wi-Fi network from this interface
  * Save your settings

  ![router-3.png](/images/router-3.png)

* **Position and Connect Hardware**

  * Move the router to its final location
  * Connect your Raspberry Pi via USB and Ethernet
  * Connect your computer to the router’s Wi-Fi

* **Adjust Network Settings**

  * Go to **Network → LAN** to configure the IP range
  * Avoid subnet conflicts:

    * The referenced blog used `10.42.0.0/16`, which caused connection issues for me when running k3s. After some debugging, I got it working on that range by passing these flags to the k3s setup command:

      ```bash
      --cluster-cidr 10.52.0.0/16 --service-cidr 10.53.0.0/16
      ```
    * In the end I changed the router's subnet to `10.100.0.0/16` to avoid any conflicts.

    ![router-2.png](/images/router-2.png)

* **Restart and Verify**

  * Power off all Raspberry Pis and the router, then turn everything back on
  * Reconnect to the router and access the router UI using the new IP

* **Reserve Static IPs for Raspberry Pis**

  * Go to **DHCP → DHCP Clients List** to see all Pis and their MAC addresses
  * Under **Address Reservation**, assign static IPs to each Pi
  * Example `~/.ssh/config` for SSH access:

    ```ssh
    Host node-1
      HostName 10.100.0.100
      User pi
      IdentityFile ~/.ssh/id_ed25519

    Host node-2
      HostName 10.100.0.101
      User pi
      IdentityFile ~/.ssh/id_ed25519

    Host node-3
      HostName 10.100.0.102
      User pi
      IdentityFile ~/.ssh/id_ed25519
    ```

* **Confirm Connectivity**

  * Power everything off again, then back on
  * Test that the reserved IPs work by SSHing into a Pi:

    ```bash
    ssh node-1
    ```

