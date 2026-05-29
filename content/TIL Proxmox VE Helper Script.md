---
Title: TIL Proxmox VE Helper Script
TQ_show_created_date:
tags:
  - proxmox
  - "#Homelab"
  - selfhosted
  - today-i-learned
draft: false
---
One of the thing that I love about IT is that sometimes you go searched for something, and that search drag you in a rabbit hole that you didn't expect!

I know to many people this does not sounds something new, but being someone new to Proxmox, the existence of a website like[Proxmox VE Helper Scripts](https://community-scripts.org/) is something very close to blow my mind. 

### What is Proxmox VE Helper Script?
So, Proxmox is an open-source server management and virtualization platform. That means that we can create VMs and LXC Containers to host various services. Depending on what we want to install, often the installation of such services may require several steps in the Proxmox shell terminal, that can be confusing to the "new" and prone to error. 

[Proxmox VE Helper Scripts](https://community-scripts.org/) is a maintained as open-source project where it is possible to find community developed services that can be installed with a simple line of code in the Proxmox shell terminal. 

I found this website because I discovered that there is a community version of N8N that can be installed and use limitless for free if self-hosted, in my case in Proxmox. When I approached the idea to self-host N8N, firstly I asked my AI of preference about it, it quickly told me about the community version and gave me the line of code to paste in the Proxmox terminal. 

But then, I remembered my previous post [[From books to AI - How (my) learning evolved]], and I told myself "Yuri, let's try the old way". Well, I searched on google and try to overlook the google AI summary that appear for every search and then I found a link of [this YouTube video](https://youtu.be/zMvhBeN6t1U?si=B9jIf7lDVmujI1Ya) that shows 2 ways to install N8N on Proxmox. The second part is exactly where he talks about [Proxmox VE Helper Scripts](https://community-scripts.org/)

Finding this website was a wonderful discovery. At the top of the homepage we can see the most popular script in the last 30 days. Scrolling down it will show the recently added, the upcoming one but not released yet. 

The header allows you to search for a specific service you want to install, and when you click on a service card, a pop-up appears with description, the line of code to use and links to documentation and website where often you can find a demo. 

The various services are also divided in different categories, and it was a pleasure to spend some time to see what's on it. 

When I first installed Proxmox, my thought was to create a personal SOC labs to acquire hands-on experience, but now I can see myself indulging in trying other services that may become or not part of my Proxmox server, like a private, self-hosted budgeting app, or Nextcloud to have your own cloud for images and videos. I mean there are even app to keep track of your groceries, pet care, collections and of course a ton of tech stuff of which I still do not know what they do. 

Really, is like discovering new flavours of your favourite candy!


