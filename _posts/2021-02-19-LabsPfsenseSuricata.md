---
layout: post
title: Creating Basic Home-Lab with VMware ESXI and Pfsense
categories: labs
published: true
comments: true
---

![]({{site.baseurl}}/images/PfSense_logo.png)


This is a guide on how to install Pfsense into your VMware ESXI lab environment. After installation and configuration, this will replace my Netgear AC1900 Router. Afterwards, I will convert the router into a simple access point. I am in need of a more robust solution for network segmentation and security controls, so I am choosing to use Pfsense instead. Also, it’s a great learning experience. This is actually not my first time using Pfsense, I previously had a virtual Pfsense instance configured as my primary firewall/router, however I was having a lot of issues with the USB NICs that were being used previously. To replace these NICs and add more stability to the system, I decided to purchase a dedicated quad port PCIe NIC.  

I am currently running ESXI on an Optiplex 7010 (16GB of RAM w/ a 2TB HDD), with a Quad port Gigabit NIC (HP NC364T). This whole setup can be had relatively inexpensively. If you want to copy my setup, you can scour eBay for a good deal for the PC and NIC. 

[Optiplex 7010]( https://www.ebay.com/sch/i.html?_from=R40&_trksid=p2380057.m570.l1313&_nkw=optiplex+7010&_sacat=0/)
[HP NC364T]( https://www.ebay.com/sch/i.html?_from=R40&_trksid=p2334524.m570.l1312&_nkw=HP+NC364T&_sacat=0&LH_TitleDesc=0&_osacat=0&_odkw=optiplex+7010/)

## Pfsense Download/Install:



