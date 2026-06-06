---
layout: post
title:  "Visualizing Suricata-Blocked Hosts on a Grafana World Map"
author: matlakow
categories: [ linux, grafana, suricata, mikrotik ]
image: assets/images/intel.png
---
## Introduction

One of the things I like about running my own network security stack is being able to see where attacks are coming from. In this setup, I use Suricata to detect malicious activity, MikroTik to automatically block offending hosts, Prometheus to collect metrics, and Grafana Geomap to visualize blocked IP addresses on a world map.

The result is a live map showing the geographic locations of hosts that have been blocked by Suricata.

![1780748064447](images/2026-04-15-VisualizingSuricata-BlockedHostsonaGrafanaWorldMap/1780748064447.png)

## **Architecture Overview**

The workflow is relatively simple:

1. Suricata detects suspicious traffic.
2. Offending IP addresses are added to a MikroTik firewall address list named `Suricata`.
3. A Linux helper server periodically retrieves the address list from MikroTik.
4. The server performs GeoIP lookups using the MaxMind GeoLite2 database.
5. A lightweight Python exporter exposes the data in Prometheus format.
6. Prometheus scrapes the exporter.
7. Grafana Geomap displays the blocked hosts on a world map.

```
Suricata

    ↓

MikroTik Address List

    ↓

Helper Server

    ↓

GeoIP Lookup

    ↓

Python Exporter

    ↓

Prometheus

    ↓

Grafana Geomap
```


## **Collecting Blocked IP Addresses**

Every 10 minutes a cron job executes two scripts:First, I checked what disks were available in the enclosures attached to controller c0

/opt/ips/get.sh
/opt/ips/convert.sh

The first script connects to the MikroTik router and exports all dynamically blocked addresses from the `Suricata` address list.This displays all drives connected to enclosure e4 and e11.

---
