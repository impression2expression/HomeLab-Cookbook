# HomeLab-Cookbook
This is a HomeLab document included my implement recipe.


> [!Important]
>**⚠️ Disclaimer** This repository is for educational and documentation purposes only. The configurations provided (script, config files etc.) are tailored for my specific HomeLab environment. **Use them at your own risk.** I am not responsible for any data loss, hardware damage, or security vulnerabilities caused by the use of these "recipes." **Always backup your configurations before implementation.**


## Why?
This cookbook is recording what I done on my own home lab, and share the whole process let others can reproduce and practice them on their own equipment. 
I use my machine to learn Linux, Networking, Firewall, Cybersecurity and more.
Hope my knowledge can help you guys have fun with your home lab.

## What is on my machine?
I use a consumer desktop with some server parts, but your home lab can just use what you have now. If you have an old laptop, it's okay to start your journal with it. I chose Proxmox for OS, there are some good video on youtube to learn how to setup Proxmox for your home server. You can search them by your own.

These hardwares are what I use on my server:
- CPU: AMD 3600 (6C12T)
- Motherboard: ASRock B450M
- RAM: 4* 16GB DDR4
- Storage:
	- 2 SATA SSD RAID1 for Proxmox OS
	- 3 SATA HDD RAIDZ1 for TrueNas 
	- 1 STAT SSD for TrueNas cache
- NIC:
	- 2.5G Realtek  1 RJ45 port 
	- HP 530T 10G 2 RJ45 port  
- GPU: Dell Nvidia GTX 645 1GB 
	- Note: 
		- this just for install Proxmox, I didn't need this for after that. Only I need to check Proxmox console directly, I may need to put it on.

Some recipes may need network equipment, I use : 
- Cisco C3650CX 8 port PoE switch
- Cisco 892FSP Router
- Cisco AIR-AP1852I

# How to use these recipes?
I document my process like a recipe. 
For ingredients, I will list the devices or environment we will work on.
In prepare parts, some setup need to be done before we start cooking.
Directions are the whole process  we will walk through.
Taste is where some process to verify your work so far.
I will put some notes or explain the concept in the last part to let you understand how it work.

So let's start it.

# Table of content

## Networking
[Configure VLAN with pfSense - part1](/Networking/Configure%20VLAN%20with%20pfSense%20-%20part1.md)
[Configure VLAN with pfSense - part2](/Networking/Configure%20VLAN%20with%20pfSense%20-%20part2.md)


