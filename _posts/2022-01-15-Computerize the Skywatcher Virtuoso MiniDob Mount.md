---
title: Computerize the Skywatcher Virtuoso MiniDob Mount
date: 2022-01-15 00:03:53 +0000
categories: [Interest, Astronomy]
tags: [telescope, DIY]
mermaid: true
---

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
