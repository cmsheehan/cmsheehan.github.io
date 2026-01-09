---
author: ["cmsheehan"]
title: "Building a Raspberry Pi Kubernetes Cluster"
date: "2026-01-08"
description: ""
tags: ["kubernetes", "pi", "docker"]
ShowToc: true
TocOpen: false
cover:
  image: "images/pi.png"
---

I've been eager to experiment with Kubernetes in my free time and my initial instinct was to spin up a managed service like Amazon EKS, but the reality check came quickly: at roughly $72 per month just for the control plane, this was overkill for a hobby project.

That's when I started exploring a more hands-on alternative: running my own Kubernetes cluster on personal hardware. I already had a Raspberry Pi collecting dust in a drawer, so it felt like the perfect opportunity to put it to work.

## The Inspiration

During my research, I discovered an excellent article by Anthony Simon: [Build a Kubernetes Cluster with Raspberry Pi](https://anthonynsimon.com/blog/kubernetes-cluster-raspberry-pi/). What captivated me wasn't just the technical implementation, but the concept of creating a **fully isolated, portable Kubernetes cluster**—something simple, lightwirhgt, portable and contained.

If you're interested in this project, I highly recommend reading Anthony's article first. He explains many of the concepts more thoroughly than I will here and I'm mostly documenting my own experience following his instructions.

## What I Built

![The Cluster](/images/pi-cluster.jpeg)

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

I already owned the 8GB Raspberry Pi 4 from a CanaKit bundle I'd purchased years earlier, which made it the natural choice for the control plane node. The additional 4GB Pi units provide enough resources for most hobby workloads while keeping costs reasonable.

