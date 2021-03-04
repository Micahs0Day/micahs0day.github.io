---
layout: post
title: Creating Basic Home-Lab with VMware ESXI and pfSense
categories: labs
published: true
---

![]({{site.baseurl}}/images/PfSense_logo.png)

## Intro:

This is a guide showcasing the installation of pfSense into my VMware ESXI lab environment. After installation and configuration I will use pfSense as a virtual machine to route all web traffic in and out of my home network, this will replace the router functionality of my Netgear AC1900, and will allow me to use the Netgear AC1900 as a simple wireless access point to connect all of our wireless devices. In a separate post, I will create VLANs for all categories of traffic on my network and then segment the traffic with firewall rules. 

This is a great learning experience and can help you learn a lot about routing protocols and virtual networking. This is actually not my first time using pfSense, I previously had a virtual pfSense instance configured as my primary firewall/router, however I was having a lot of issues with the USB NIC’s that I was using previously so I decided to get a dedicated quad port PCIe NIC.  

I am currently running ESXI on an OptiPlex 7010 (16GB of RAM w/ a 2TB HDD), with a Quad port Gigabit NIC (HP NC364T). This whole setup can be had relatively inexpensively. If you want to copy my setup, you can scour eBay for a good deal for the PC and NIC. 

[OptiPlex 7010]( https://www.ebay.com/sch/i.html?_from=R40&_trksid=p2380057.m570.l1313&_nkw=optiplex+7010&_sacat=0/)

[HP NC364T]( https://www.ebay.com/sch/i.html?_from=R40&_trksid=p2334524.m570.l1312&_nkw=HP+NC364T&_sacat=0&LH_TitleDesc=0&_osacat=0&_odkw=optiplex+7010/)

## Networking Planning:

A wise man once said, “By failing to prepare, you are preparing to fail”, so taking these wise words to heart, I used a program called yEd Graph Editor to map out my network’s topography. I am a visual learner, so this helps tremendously when configuring and installing network devices. Everything you see is not yet in place, but I hope to add on to my network little by little until it looks like this. As you can see by the color schema, I will have three physical network connections, and several virtual and wireless connections in my topography. My main PC will be on my management network, this is vital because you never want to be unable to connect to your ESXI server.

(Right Click and Open in New Tab for Full Resolution)
![]({{site.baseurl}}/images/networkgraph.png)

## Download/Install pfSense:

[pfSense]( https://www.pfsense.org/download/)

[CheckSum Utility]( https://download.cnet.com/MD5-SHA-Checksum-Utility/3000-2092_4-10911445.html/)

The next step is downloading pfSense from their website. Since this is a Cyber-Security channel, we will be verifying the zip files checksum to verify that no integrity has been lost in the upload/download process. This can be done using the command line, or you can install a program like “MD5 & SHA Checksum Utility” 

![]({{site.baseurl}}/images/pfsensedownload.png)

After we have our ISO downloaded and verified, we will unzip the ISO, navigate to our ESXI datastore then upload the ISO to the datastore. I find it helpful to create a “ISOS” directory on the datastore to store uploaded ISO files.

![]({{site.baseurl}}/images/datastore.png)

![]({{site.baseurl}}/images/datastoreupload.png)

## Virtual Switches:

Next, we will be configuring the virtual switches and port groups of ESXI server to use as WAN and LAN uplinks for our pfSense machine. For my installation, the only device on my management network (LAN) will be my desktop PC.

![]({{site.baseurl}}/images/physicalnics.png)

Vmnic0 is already configured as our management network (LAN) on vSwitch0, so all we need to do is add a virtual port group to use in our VM. 

![]({{site.baseurl}}/images/vmnic0.png)

![]({{site.baseurl}}/images/LANportgroup.png)

![]({{site.baseurl}}/images/LANportgroup3.png)

Next is the port group for our WAN, we will create a virtual switch, and a port group the same way we did for the LAN, but this time we will be using a different physical NIC for the virtual switch, in my case this is vmnic1 & vSwitch1.

![]({{site.baseurl}}/images/vSwitch1.png)

![]({{site.baseurl}}/images/virtualswitches1.png)

![]({{site.baseurl}}/images/WANportgroup.png)

![]({{site.baseurl}}/images/WANportgroup2.png)

Now we are done with configuring our virtual network for now, let’s move onto creating our PfSense VM and connecting our uplinks.
## Virtual Machine Creation:
Navigate back to the virtual machines tab, then open the “Create/Register VM” wizard.

![]({{site.baseurl}}/images/vmwizard0.png)

Select “Create new Virtual machine”, then hit next.

![]({{site.baseurl}}/images/vmwizard1.png)

Enter a name for your new PfSense machine. In my case I will just name it PfSense, doesn’t really matter what you name it. Select the Version of ESXI that you are using, in my case, ESXI 6.7. OS Family is “Other”, and my Guest OS Version is FreeBSD 12 (64-bit) since I’m installing version 2.5.

![]({{site.baseurl}}/images/vmwizard2.png)

Select the datastore you want to use for storage. Then hit next.

![]({{site.baseurl}}/images/vmwizard3.png)

This is the fun part! Allocating resources. So, you want to scale your resources based on your deployment. If you head to the PfSense website and view their products, it will give you a sense of what hardware requirements are needed for all types of deployments. In my case, I will be installing this as my home router/firewall, so it won’t require much, the default settings are sufficient in my case. While we are here, go ahead and choose LAN as our first network adapter and then add a second which we will choose as WAN from the dropdown menu. 

[pfSense Hardware Requirements](https://www.pfsense.org/products/)

![]({{site.baseurl}}/images/vmwizard4.png)

Also, we will mount our PfSense ISO to run at first startup.

![]({{site.baseurl}}/images/vmwizard5.png)

![]({{site.baseurl}}/images/vmwizard6.png)

Hit “Next”, then review your settings, then if everything looks good, hit “Finish” to complete the VM setup.

Find your newly deployed VM in your virtual machine list, right click it, then power it on.

![]({{site.baseurl}}/images/vmwizard7.png)

![]({{site.baseurl}}/images/vmwizard8.png)

Select your language. 

When you get to the partitioning screen, select “Guided Disk Setup using BIOS boot method”.
Let the Installer do its thing.

![]({{site.baseurl}}/images/vmwizard9.png)

At this screen select “no”. 

![]({{site.baseurl}}/images/vmwizard10.png)

Reboot the system.

![]({{site.baseurl}}/images/vmwizard11.png)

When it’s done, it will autoboot you into the default configuration prompt. 
We aren’t setting up VLAN’s at this point so type “n”.

![]({{site.baseurl}}/images/vmwizard12.png)

If you remember from earlier, our WAN interface is vmx1 and our LAN interface will be vmx0.

![]({{site.baseurl}}/images/vmwizard13.png)

![]({{site.baseurl}}/images/vmwizard14.png)

![]({{site.baseurl}}/images/vmwizard15.png)

## Routing

After the machine gets through setting our configurations you will be greeted with this screen. There’s so much that we can change here, but I will keep everything default as of now. Don’t worry about the WAN address for now, I will explain that in a few. 

The only thing left to do now is to connect my PC’s LAN cable to the MNGMT port on the back of my server and the WAN port to the uplink from my modem. 

Wait a few minutes and your ESXI interface should come back up if you are using the same IP schema. If you changed IP schema, you would have to search for the IP address of ESXI using something like Angry IP scanner or simply connect a monitor to your ESXI machine, the IP address should be on the home screen. Also, you may have to disable/re-enable your PC’s NIC to get your IP to change, or do an ipconfig /release, ipconfig/renew. If your modem is annoying like mine, you may have to reset your modem to get a WAN IP.

![]({{site.baseurl}}/images/Pfsensewanuplink.png)

## Web Interface

Now we can navigate to our gateway’s IP address, in my case 192.168.1.1, and we should be greeted with a login interface. The default username is “admin” and the default password is “pfsense”.

![]({{site.baseurl}}/images/pfsenseweb0.png)

Hit next a few times, then when you get to the General Information screen. As you can see my Hostname is PfSense and my Domain is M0d.com, configure this as you wish. Also, I will be using Google’s DNS servers. Popular DNS addresses are Google’s (8.8.8.8/8.8.4.4) and Cloudfare’s (1.1.1.1).

![]({{site.baseurl}}/images/pfsenseweb1.png)

Read more about DNS here:

[DNS](https://www.lifewire.com/what-is-a-dns-server-2625854/)

You can leave the Time Server hostname as default but change the Time zone to match where you live.

![]({{site.baseurl}}/images/pfsenseweb2.png)

Hit Next on step 4, unless you know what you are doing.

Since I will be keeping the default IP schema, I hit next on Step 5 as well.

Step 6: Change the Admin password!! Kudos to PfSense for making this mandatory during the installation process. This ensures that you don’t leave the default admin credentials in place, which is a BIG no no.

Step 7: Hit “Reload to reload PfSense with your new changes.”
Step 8: Wait for your router to reload.

Step 9: Check for updates. It’s important to keep your servers up to date. Manufactures release security and quality patches all the time to keep your machines safe and running smoothly, so be on the lookout for those. Since we are on the latest version, there is nothing to update, but better to be safe than sorry.

You should be greeted with the wonderful PfSense dashboard as we are finally finished with our deployment!!!

## Done
You (hopefully) have successfully completed setting up PfSense in a virtual environment. Take a moment to pat yourself on the back, breathe a breath of relief, then get ready for the next step (coming soon)!! Feel free to go out and research things to install on your Hypervisor that may interest or be of benefit to you.

I suggest you go check out these resources:

[r/homelab]( https://www.reddit.com/r/homelab/ )

[PiMyLifeUp]( https://pimylifeup.com/ )

[VulnHub]( https://www.vulnhub.com/)

 





