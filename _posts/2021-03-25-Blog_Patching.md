---
layout: post
title: Patching - A Necessary Evil
published: true
categories: blog
comments: true
---


![]({{site.baseurl}}/images/patching.png)

As a security-oriented system administrator, it is my responsibility to create and maintain a safe and stable environment for my company. Patching plays a vital role in helping me stay compliant in this area. Despite the many benefits that come from regularly patching our systems, there are also many pitfalls and obstacles accompanied with this critical part of my job.

## Patching is critical and always needs to be addressed in some manner.
The Cybersecurity nerd in me always tries to keep a close ear to the ground and stay up to date on everything Cybersecurity related, I do this by following popular security feeds on twitter, listening to podcasts, and perusing major security blogs. With this massive influx of information, it is easy to get swept away with all the news about 0-days, vulnerabilities, and non-ethical hacking that is in the media. I mention this because whenever these security issues are uncovered, there is usually a swift response from manufacturers and vendors to release a patch to remediate the problem (as seen recently with the Microsoft exchange server vulnerabilities). Having a good vulnerability management program in place helps to reveal if these vulnerabilities exist within your systems, then you can assess the risk they pose and remediate them as you see fit. 

### A risk management program that includes patching can help mitigate risks such as:
-	Data Breaches
-	Ransomware
-	Malware

# Pros:

## Increased system stability: 
Patches can help to minimize the possibility of crashes and downtime, allowing your environ-ment to run more efficiently, which in turn can increase productivity. 

## Compliance:
Some industries have strict stipulations regarding their systems and how much risk is allowed. Keeping systems up to date and patched is usually a staple of most compliance requirements. 

## Added features:
Sometimes vendors/manufacturers release feature updates that fix bugs but can also include new features and functionalities for your systems. Some of these feature updates include quali-ty of life updates, which can help your company do their work more efficiently and with less frustration. 

# Cons:

## Patching causes downtime (production environment)

In our environment, production is running nearly 24/7. With this amount of up time, it can be difficult to coordinate with the production manager to create maintenance windows to patch/update systems in our environment. This is especially difficult when dealing with always on systems. When dealing with systems such as this, the infrastructure must include some sort of redundancy/failover to ensure that patches are being installed without causing interruptions to services.  

## Patching is time consuming:

Automating the patching/updating process is extremely important. No system administrator has time to manually update/patch every device on their network. Not only is this extremely time consuming, but it is also inefficient. Having a centralized management platform that automates patch distribution deploys updates helps to make this process manageable. 

## Legacy systems: 

In production environments, it’s not uncommon to find that specialized machinery uses deprecated software/Operating systems.


## Testing patches before approving them for installation: 

Sometimes updates and patches break things. Best practice is to create a lab environment (preferably virtualized) to test patches and updates before deploying them to the production environment. 

Overall, it’s best practice, and probably in your best interest to maintain a patch management system to keep your infrastructure safe and stable. The benefits of keeping your systems patched and updated outweigh the issues by a large margin. 
