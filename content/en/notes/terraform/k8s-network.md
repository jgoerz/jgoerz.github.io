---
title: "k8s network"
linkTitle: "k8s network"
date: 2023-12-18T10:10:00-05:00
weight: 20
---

## Overview

Have two sets of subnets that make it simple to upgrade k8s in the network
without touching the existing k8s.  Break this into 2 phases.

1. Phase 1: initial deployment
1. Phase 2: upgrade cycle
1. Phase 1: upgrade cycle
1. and so on

The idea is you can deploy phase 2 alongside phase 1 without disruption.   Use
DNS and/or load balancers to switch active k8s cluster.   This is a manual
blue/green for upgrading k8s (not apps) as k8s issues are cross cutting.

- Use class A CIDR
- All subnets use /20 or 255.255.240.0 netmask
- 4,096 total hosts per subnet
- The third octet of the public subnet address is always 2 digits
- The third octet of the private subnet address is always 3 digits

[IP calculator for the following table](https://www.calculator.net/ip-subnet-calculator.html?cclass=any&csubnet=20&cip=10.10.0.0&ctype=ipv4&x=Calculate)


| Type | zone/phase |   Address   |        Host Range           |   Broadcast   |
|------|------------|-------------|-----------------------------|---------------|
| pub  |   a / 1    | 10.10.0.0   | 10.10.0.1 - 10.10.15.254    | 10.10.15.255  |
| pub  |   b / 1    | 10.10.16.0  | 10.10.16.1 - 10.10.31.254   | 10.10.31.255  |
| pub  |   c / 1    | 10.10.32.0  | 10.10.32.1 - 10.10.47.254   | 10.10.47.255  |
| pub  |   a / 2    | 10.10.48.0  | 10.10.48.1 - 10.10.63.254   | 10.10.63.255  |
| pub  |   b / 2    | 10.10.64.0  | 10.10.64.1 - 10.10.79.254   | 10.10.79.255  |
| pub  |   c / 2    | 10.10.80.0  | 10.10.80.1 - 10.10.95.254   | 10.10.95.255  |
| N/A  |  unused    | 10.10.96.0  | 10.10.96.1 - 10.10.111.254  | 10.10.111.255 |
| priv |   a / 1    | 10.10.112.0 | 10.10.112.1 - 10.10.127.254 | 10.10.127.255 |
| priv |   b / 1    | 10.10.128.0 | 10.10.128.1 - 10.10.143.254 | 10.10.143.255 |
| priv |   c / 1    | 10.10.144.0 | 10.10.144.1 - 10.10.159.254 | 10.10.159.255 |
| priv |   a / 2    | 10.10.160.0 | 10.10.160.1 - 10.10.175.254 | 10.10.175.255 |
| priv |   b / 2    | 10.10.176.0 | 10.10.176.1 - 10.10.191.254 | 10.10.191.255 |
| priv |   c / 2    | 10.10.192.0 | 10.10.192.1 - 10.10.207.254 | 10.10.207.255 |
| N/A  |  unused    | 10.10.208.0 | 10.10.208.1 - 10.10.223.254 | 10.10.223.255 |
| N/A  |  unused    | 10.10.224.0 | 10.10.224.1 - 10.10.239.254 | 10.10.239.255 |
| N/A  |  unused    | 10.10.240.0 | 10.10.240.1 - 10.10.255.254 | 10.10.255.255 |

