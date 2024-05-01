---
title: Proxmox Homebuild Part 1
date: 2024-04-30 12:00:00 +0100
categories: [proxmox, homelab, hardware]
tags: [servers,networking]
---

As a novice Proxmox admin I've been playing around with a few Proxmox configurations and setups and made a few "messes" in my homelab.

After much research to get familiar with Proxmox networking I decided that the basic network I had at home protected by an old "refurbished" Cisco ASA that was barely running anymore was not sufficient. Hence decided to start a new project to migrate my network and security into a virtual OPNSense inside of Proxmox

During the research, I noticed many post on Reddit and the Proxmox forums asking about this particular setup with many variations, hence I have decided to share mine.

## Requirements

Since I was upgrading my network and security, I had a few requirements in place:

1. The virtual router would be connected directly to the internet and dialing up the PPPoE.
2. As much as possible I would like to have "highly available" VMs, in case I need to reboot or "take-down" a server for any kind of maintenance.
3. VLANs are a must to keep separate networks. VMs must be able to comminucate with external computers (WiFi or cabled).
4. Unifi Controller to be virtualised also (moving from an old Raspberry Pi 3B).
5. Everything, including Proxmox management to be "behind" the virtual firewall.
6. I must be able to "self-host" services on an specific VLAN.
7. Be able to remote from outside without the need to poen any extra ports.
8. Minimise network disruption to avoid "family complains".

## Pre-Requisites and Hardware

With that list in mind, I got my hands on a couple of old Dell Servers sporting a Xeon E3-1270 CPU, 32GB RAM and 3x 1TB disks each. Not a high performance setup, but more than enough to run quite some VMs and CTs.

In order to achieve the required networking and high availability, a Layer 2 switch was required, so I opted for a Unifi USW-16-PoE to match my U6 and U6+ access points.

## Architecture

### Logical View

![alt text](/images/image.png)

### Physiscal View

![alt text](/images/image-1.png)

### List of VLANs and Subnets

    1. VLAN20 - ISP connection
        No IP subnet
        - ISP router
        - OPNSense Outside
    2. VLAN100 - MGT management
        192.168.10.0/26
        - OPN LAN1
        - Proxmox Servers (01 and 02)
        - Unifi Controller (LXC)
        - Access Points
        - Switch
    3. VLAN200 - Internal Servers
        192.168.20.0/26
        - JellyFin/Plex
        - FileShare
        - Minecraft Server
    4. VLAN300 - Users
        192.168.30.0/26
        - User devices (WiFi and User VMs)
    5. VLAN400 - DMZ
        192.168.40.0/26
        - NGINX Proxy Manager
        - CloudFlare Tunnel Docker
    6. VLAN500 - ZeroTier
        192.168.50.0/26
        - Remote devices

## To Be continued...

Planning is one of the most critical parts of any build, and for this first part, we have had probably enough thinking. In the next part we will deploy the first Proxmox server, configure the networking and deploy our first VM.
