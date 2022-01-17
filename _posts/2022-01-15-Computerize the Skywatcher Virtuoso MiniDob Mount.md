---
title: Computerize the Skywatcher Virtuoso MiniDob Mount
date: 2022-01-15 00:03:53 +0000
categories: [Interest, Astronomy]
tags: [telescope, DIY]
mermaid: true
---

This post is to illustrate how to computerize the Skywatcher Virtuoso miniDob mount. Assembled and tested in July 2021. Some interesting discussions on the proper way to computerize it can be found in this [discussion](https://stargazerslounge.com/topic/239925-skywatcher-heritage-virtuoso-controlled-via-bluetooth/). Note: since everyone's expectations and budgets may largely differ, no comment will be made here on whether the mount discussed is a rational choice. 

# Background information
The [Skywatcher Virtuoso miniDob mount](https://www.skywatcherusa.com/products/sky-watcher-virtuoso) is a single-arm, alt-azimuth mount sold with a 90mm Maksutov-Cassegrain telescope or a 114mm Newtonian telescope since 2014. Now the price of a used one is typically around £200. It can be driven by built-in motors or by hands. The clutches ensure the manually pushed rotations are tracked and recorded as motor-driven motivations. The mount has already been computerized with a relatively simple program - auto-tracking is utilized if the true North and geographic latitude is set correctly. Besides, it can also cruise along a pre-set path. The [official manual](https://cdn.shopify.com/s/files/1/0080/7095/5123/files/S11750_Virtuoso.pdf) in PDF version is available online. 

* A **3/8"** thread for common camera tripods locates at the bottom of the AZ clutch;  
* A RJ11/12 serial port for communication;  
* A 12V 2A 5.5\*3mm DC port for external power supply;  
* A snap cable port for Canon cameras.  

# Setup

``` mermaid
flowchart LR
    subgraph Telescope
        direction LR
        A(MiniDob mount) -- RJ11/12 serial port --> B(WiFi Adapter)
    end
    subgraph Mobile
        direction LR
        B(WiFi Adapter) -.-> C(SynScan Mobile)
    end
    B(WiFi Adapter) -.-> D(SynScan Windows)
    subgraph Laptop
        direction LR
        D(SynScan Windows) -- drive --> E(ASCOM)
        E(ASCOM) -- drive --> F(Stellarium)
    end
```

The setup for the complete computerization of the miniDob mount is illustrated in the flow chart above. 

# Other options