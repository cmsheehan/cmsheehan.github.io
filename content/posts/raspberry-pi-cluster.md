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

I've been eager to experiment with Kubernetes in my free time and my initial instinct was to spin up a managed service like Amazon EKS, but the reality check came quickly: at roughly $72 per month just for the control plane, this was overkill for a hobby project.

That's when I started exploring a more hands-on alternative: running my own Kubernetes cluster on personal hardware. I already had a Raspberry Pi collecting dust in a drawer, so it felt like the perfect opportunity to put it to work.

## The Inspiration

During my research, I discovered an excellent article by Anthony Simon: [Build a Kubernetes Cluster with Raspberry Pi](https://anthonynsimon.com/blog/kubernetes-cluster-raspberry-pi/). What captivated me wasn't just the technical implementation, but the concept of creating a **fully isolated, portable Kubernetes cluster**—something simple, lightweight, portable and contained.

If you're interested in this project, I highly recommend reading Anthony's article first. He explains many of the concepts more thoroughly than I will here and I'm mostly documenting my own experience following his instructions.

## What I Built

![The Cluster](/images/pi-cluster-1.jpeg)

My goal was to create a three-node Kubernetes cluster that I could:
- Power through a single PoE switch to minimize cable clutter
- Use on any network via a portable router
- Host without worrying about cloud costs

## Bill of Materials

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

I already owned the 8GB Raspberry Pi 4 from a CanaKit bundle I'd purchased years earlier, which made it the natural choice for the control plane node. The additional 4GB Pi units provide enough resources for most hobby workloads while keeping costs reasonable. While this setup is inspired by Anthony’s cluster build, it isn’t an exact replica. There are differences in the cluster case, SD cards, and PoE HATs.

## Assembling the Hardware

I began by unpacking the Raspberry Pis and preparing each board for assembly. Once unpacked, I attached the heat sinks and installed the PoE HATs.

<div class="pi-row">
  <img src="/images/pi-1.png" class="pi-img">
  <img src="/images/pi-2.png" class="pi-img">
  <img src="/images/pi-3.png" class="pi-img">
</div>

With the individual Pis fully assembled, I moved on to building the cluster case. Each layer of the case holds a single Pi. After the case was fully assembled, I connected the router and network switch, then powered everything on for the first time.

![Assembled Cluster](/images/pi-cluster-2.png)

## Flashing the OS Image

To set up my Raspberry Pi nodes, I used the **Raspberry Pi Imager** app to flash the latest **Ubuntu Server LTS OS** onto microSD cards via a Uni SD Card Reader.

![Image OS](/images/image-builder-1.png)

Following the approach outlined in the reference article, I leveraged `user-data` files to pre-configure each node. This allowed me to:

* Set a custom hostname for each device.
* Enable SSH access.
* Install essential packages automatically.

Here’s an example of the `user-data` configuration I used:

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

After flashing the OS image, I safely ejected the storage device. I then remounted it to update the `user-data` file, which on macOS is located at:

```
/Volumes/system-boot/user-data
```

I repeated this process for all three nodes.

## Configuring the Router

The referenced article provides great documentation on this part and why it is configured the way it is. I will provide additional detailed instructions on configuring this version of the router on Mac.

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

    * The referenced blog used `10.42.0.0/16`, which caused connection issues when running k3s. After some debugging, I was finally able to enable with the referenced `10.42.0.0/16` CIDR by providing the following parameters in the setup command.

      ```bash
      --cluster-cidr 10.52.0.0/16 --service-cidr 10.53.0.0/16
      ```
    * I chose to reconfigure the VLAN subnet `10.100.0.0/16` to avoid any potential conflicts

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

