---
layout: post
title: 'Writeup - TryHackMe - [Easy] PickleRick CTF '
published: true
---
What is the first ingredient Rick needs?

 - Deploy the machine, and connect to THM's network using the VPN.

![]({{site.baseurl}}/images/PickleRick-THM-01.png)

 - A quick ifconfig shows that I am connected successfully.
 
![]({{site.baseurl}}/images/PickleRick-THM-03.png)

 - Even though I know that it's a webserver probably running on port 80, I still like to do an nmap scan to get a lay of the land and try to gather as much information that I can about the target.
 
 - In this instance, the IP address of my box is **10.10.76.17**.
 
 - So I run the scan:
        
```sudo nmap -vv -sS -T4 10.10.76.17```
 
-p-     Scans all ports (default nmap scan only scans the first 1024 ports)
-vv     Sets verbosity for the scan (causes the scan to output information during the scans execution)
-T4     The speed of the scan that I want to run (higher speeds tend to be more innacurate)
-sS     is the default scan when nmap is ran with admin priviledges (sudo).
*You can also do a range of IP address and scan entire subnets, this takes a lot more time though.     

    
   If you want to learn more about nmap and it's functionality, you can run the command man nmap or visits nmaps website.
    
   This was the output:
    
![]({{site.baseurl}}/images/PickleRick-THM-04.png)
        
   - Since this is a webserver challenge I will try and connect to the server using the supplied IP address. IP addresses automatically resolve to port 80 so I don't have to specify the port.
    
   - After navigating to the URL, I was greeted by this webpage;
    
![]({{site.baseurl}}/images/PickleRick-THM-05.png)
    
   - Rick says to logon to his computer, since we found a SSH server on port 22 I decided to prod it a bit and try to logon using rick as the username:
    
![]({{site.baseurl}}/images/PickleRick-THM-06.png)
    
   - However I kept getting “Permission denied” errors from the SSH server. That's when I decided to dig around the website a bit more.
    
   - After inspecting element, I found a comment in the HTML containing Ricks username “R1ckRul3s”.
    
   - Since the SSH server was a no go, I went on to further enumerate the box using dirbuster and the /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt wordlist found in default kali installations. There are plenty of others ways to enumerate directories, but I chose to use this method.
    
![]({{site.baseurl}}/images/PickleRick-THM-07.png)

- After the scan ran for a few minutes, we started to get a few 200 responses from the server.

![]({{site.baseurl}}/images/PickleRick-THM-08.png)

- The one that stood out the most was the "\login.php" page that was found, so I decided to give that a try first.

- Lo and Behold! I found the login page for the webserver.

![]({{site.baseurl}}/images/PickleRick-THM-09.png)

- In the list of found directories, I also found a robots.txt file which contains the password “Wubbalubbadubdub”.

- After logging into the website I was greeted by this page:



- The commands tab is the only tab available to me at the moment.

- An ls command shows the contents of the current directory.


    
    
- When I path out to http://10.10.76.17/Sup3rS3cretPickl3Ingred.txt , I get this message: "mr. meeseek hair" , which also happens to be the first flag.

- http://10.10.76.17/clue.txt displays the message “Look around the file system for the other ingredient.”

- I ran a find command to list all of the .txt files in the system, however I could not find anything this way.

- So I decided to try and find other home users home directories. 

- After running:



I found the home directories of “rick” and “ubuntu”



- So I decided to explore the rick directory a bit more.



- After running this, I found this file.



- Since I can't use the command cat , I use less instead.



- Doing so allows me to find the second flag “1 jerry tear”

The final flag gives me a hint of:



- So I run the command sudo -l this gives me a list of commands that my current user can run with admin rights.



Ahhh, so we have all the power in the wolrld (system) pretty much. We are allowed to run (ALL) commands as sudo.

Lets try to look in the root directory of the system. THM has taught me that there are always flags there.

Running the command sudo ls /root lists the files inside of the /root directory.



- And there we are, looks like we found our last flag!!!
