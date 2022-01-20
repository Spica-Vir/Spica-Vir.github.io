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

Available connectors or ports for extensions are listed below:

* A **3/8"** thread for common camera tripods locates at the bottom of the AZ clutch;  
* A standard dovetail slot;  
* An extension arm with **1/4"** thread for common cameras;  
* A RJ11/12 serial port for communication;  
* A 12V 2A 5.5\*3mm DC port for external power supply;  
* A snap cable port for Canon cameras.  

# Setup

The setup for the complete computerization of the miniDob mount is illustrated in the flow chart below. 

``` mermaid
flowchart LR
    subgraph Telescope
        direction LR
        A(MiniDob mount) -- RJ11/12 serial port --> B(WiFi Adapter)
    end
    subgraph Cellphone
        direction LR
        B(WiFi Adapter) -.-> C(SynScan Android)
    end
    B(WiFi Adapter) -.-> D(SynScan Windows)
    subgraph PC
        direction LR
        D(SynScan Windows) -- drive --> E(ASCOM)
        E(ASCOM) -- drive --> F(Stellarium)
    end
```

## Setup the connection
To utilize the computerization, the official solution is adopted to avoid unnecessary troubles. In fact, non-official WiFi/Bluetooth solutions are not noticeably competitive in costs. For the official solution, two critical parts are required: 

1. [SynScan WiFi adaptor](http://skywatcher.com/product/synscan-wi-fi-adapter/), plugged into the RJ11/12 serial port for wireless communication;  
2. [SynScan App](https://www.skywatcher.com/download/software/synscan-app/), installed on your computer or cellphone (Android only) for connection to the mount.   

After setting up the connection, your laptop or cellphone can already function as a hand controller for GOTO via SynScan App. For cellphone users, that is the end of story due to the limited choice of applications. For personal computer (PC) users, it is possible to track and control the current field of view (FoV) in the realistic sky via [Stellarium](https://stellarium.org/). 

## Live tracking on personal computers
It is possible to achieve the live tracking of the telescope FoV on personal computers. Two software should be installed: 

1. [ASCOM](https://www.ascom-standards.org/), the platform bridge between the SynScan driver and the virtual planetarium software;  
2. [Stellarium](https://stellarium.org/), a virtual planetarium supporting ASCOM.  


# Other options
## Hand controller
The most direct way to computerize the mount. However, it is also the most expensive option. Considering the conditions of field observations, especially in winters, its robustness makes it worth that price. 

Only the old version, [SynScan V3 hand controller](https://www.skywatcher.com/download/software/synscan-v3-hand-controller-firmware/), is supported. Price for used ones varies, typically \~￡120.   

## Wired connection
Connecting to the telescope mount via a serial-to-USB cable is possible and cheaper. However, its flexibility will be influenced. Besides, such connection is currently known to support Windows PC only. Another issue is that the tested [serial-to-USB cable](https://teleskopy.pl/product_info.php?currency=EUR&products_id=3376&language=en&lunety=Przew%C3%B3d_kabel_USB_do_SkyWatcher_Virtuoso,_d%C2%B3ugo%C2%B6%C3%A6_2_metry) with the PL2303 chip is no longer available any more. The alternative with the FT232RL chip currently on the market does not fit the mount according to my test in July 2021. 

If, by any chance, the wired connection is set up properly, the driver, [AZ GOTO do Armazém](https://sourceforge.net/projects/driverazgoto/) should be installed to make ASCOM recognize the mount and setup the connection to Stellarium, in a similar fashion to the SynScan driver. 

## Other software
Stellarium is currently the most widely-supported and well-maintained virtual planetarium on Windows. Another software, [Cartes du Ciel](https://astronomytechnologytoday.com/2019/10/28/cartes-du-ciel/) is also [tested effective](https://www.youtube.com/watch?v=r23mG2xPiBU&t=56s) for both wireless and wired connections. 
