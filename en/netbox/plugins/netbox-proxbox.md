---
title: Proxbox
description: Netbox Plugin for integration between Proxmox and Netbox
published: true
date: 2022-05-12T14:04:52.474Z
tags: proxbox, netbox, netbox-plugin, proxmox
editor: markdown
dateCreated: 2022-05-12T13:32:55.815Z
---

# Proxbox Plugin

- ## Summary
  - ### [Introduction](#introduction)
    - #### [What is Proxbox?](#what-is-proxbox?)
    - #### [Why?](#why?)
{.links-list}
 
<br>
<div align=center>
  
  ## Introduction
</div>




### What is Proxbox?
**Proxbox was created to "mirror" Proxmox on Netbox.** The goal is to make Proxmox GUI unnecessary, allowing to do anything you do on Proxmox GUI, but on Netbox GUI.

<br>

#### Why?
Since I started working with Networking, it always bothered me the amount of softwares and platforms needed to operate the network. Although there is some incredible software that does a lot of things at the same time, it normally is either expensive or vendor-specific.

I think Netbox has the potential to be the brain of the network (or the controller). With the Plugin feature, it makes possible to not necessary replace all softwares and use only Netbox, but integrate it all with Netbox in a way that you can manage your network only using Netbox, one system. With that in mind, I started the work with Proxbox and I expect to collaborate with the community by creating much more Plugins or helping others with their own Plugins.

<br>

### Current features
Proxbox is currently able to get the following information from Proxmox:

- **Cluster name**
- **Nodes (Servers)**
  - Status (online / offline)
  - Name
- **Virtual Machines and Containers**
  - Status (online / offline)
  - Name
  - ID
  - CPU
  - Disk
  - Memory
  - Node (Server)

<br>

<div align=center>
  
  ## Roadmap
</div>

- #### **Virtual Machines and Containers**
  - ##### [**Get Network information from Proxmox** (IP address from VM's and Containers, MAC address, etc...)](https://github.com/netdevopsbr/netbox-proxbox/issues/52)
- #### Metrics and Graphs views (like Hardware usage, Storage occupation, etc...)
  
- #### **Add, Change and Delete actions** (currently, Proxbox only reads information from Proxmox, but can't change anything)
- #### [**Add multi-cluster support**](https://github.com/netdevopsbr/netbox-proxbox/issues/33)
  
  > Proxbox can only connect with a single cluster (Proxmox Datacenter)
  {.is-warning}
  <!-- This comment makes "is-warning" class work --->
{.links-list}


