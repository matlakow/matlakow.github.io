---
layout: post
title:  "Building an AI-Powered IDS/IPS for a Homelab"
author: matlakow
categories: [ Linux, Proxmox, AI, Suricata, Mikrotik, Graylog, Ollama ]
image: assets/images/Activedirectory.png
---

This article shows how you can easily add IDS/IPS functionality to your homelab.
It also explains how to extend the setup with an AI-powered SIEM solution.

My setup is based on:

* MikroTik router — specifically the MikroTik RB5009UG+S+ [link](https://mikrotik.com/product/rb5009ug_s_in)
* SELKS package by Stamus Networks [link](https://www.stamus-networks.com/hubfs/Datasheets/StamusNetworks-DS-SELKS-062024-1.pdf?hsLang=en)
* mikrocata2selks integration project for easier SELKS deployment [link](https://github.com/angolo40/mikrocata2selks)
* Proxmox VE as the hypervisor [link](https://www.proxmox.com/en/products/proxmox-virtual-environment/overview)
* Ollama￼ with GPU acceleration for AI analysis [link](https://docs.ollama.com/gpu)
* Graylog for log collection, normalization, and analysis [link](https://graylog.org/products/source-available/)

⸻

Hardware Requirements

1. Router

* MikroTik RB5009UG+S+

2. Proxmox Host for VMs

2a. Suricata / SELKS VM

(under the hood this runs multiple Docker containers)

Recommended:

* 4 CPU cores
* 16 GB RAM
* 64 GB SSD/NVMe storage

2b. AI VM

Used for Ollama and AI analysis.

Recommended:

* 4 CPU cores
* 8 GB RAM
* GPU acceleration
* 32 GB HDD/SSD storage

2c. Small Linux LXC/LXD Container

Used for automation scripts.

Recommended:

* 2 CPU cores
* 2 GB RAM
* 16 GB storage

2d. Graylog Stack VM

(also running multiple Docker containers)

Recommended:

* 8 CPU cores
* 16 GB RAM
* 64 GB SSD/NVMe storage

⸻

Summary

You will need:

* around 40–50 GB free RAM
* a machine with at least 10 CPU cores
* about 200 GB of fast SSD/NVMe storage
* and a lot of free time, energy, and enthusiasm to work on it ;-)

⸻

How It Works

The MikroTik router uses the built-in Packet Sniffer feature to mirror WAN traffic to Suricata.

Suricata receives the traffic over the TZSP interface and starts deep packet inspection and threat analysis.

When Suricata detects suspicious or malicious activity, it uses the MikroTik API to automatically add the attacker IP address to the firewall block list.
This means malicious traffic gets blocked almost immediately.

Both MikroTik and Suricata send logs to Graylog.

Next, a simple script collects:

* selected Suricata events,
* additional MikroTik logs,
* and other useful security information.

The script then sends this data to  Ollama￼ for AI analysis.

Because the AI has visibility into logs from both systems, it can:

* correlate events,
* analyze attack patterns,
* detect suspicious behavior,
* and identify possible security incidents.

Finally, the system periodically informs me about serious attacks and important security threats detected in the network.
-------------------------------------------------------------------------------------------------------------------------

