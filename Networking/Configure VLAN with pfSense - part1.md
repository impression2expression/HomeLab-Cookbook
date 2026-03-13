# Configure VLAN with pfSense - part1
![Vlan-diagram.jpg](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/Vlan-diagram.jpg)


## Purpose
Improve home network environment. VLAN could help you increase your security, also improve performance. If you want to learn more detail can search VLAN on youtube, there are many explanation video can help you understand.

I will separate two part for this recipe, 
- part 1 focus on how to configure VLAN and Firewall rules on pfSense.
- part 2 will implement Firewall rules on Proxmox and configure L3/L2 Switch
## Ingredients
- Proxmox host
- pfSense VM
- Laptop or desktop can access Proxmox and pfSense web ui

## Prepare
- Install pfSense VM on your Proxmox host, check References & Resources, I list some video to help you set up the environment.
- Turn off Firewall for pfSense VM, we will let pfSense configure its own rules.

## Directions
### Step 1 Set up VLAN Tag
1. Go to Interfaces -> Assignments
2. Clicks VLANs 
3. Click +Add button
	1. Parent Interface:  Select your LAN port (like re0, vtnet0)
	2. VLAN TAG: 10
	3. VLAN Priority: keep blank or set 7 if need
	4. Description: IoT
	5. Click Save
![file-20260312214427035](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260312214427035.png)
4. Repeat above step create VLAN 20, 30, 40
![file-20260313102816240](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313102816240.png)

### Step 2 Assigns VLAN to Interfaces
1. Go to Interfaces -> Assignment
2. Under "Available network ports" select VLAN 10 ![file-20260313102918629](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313102918629.png)
3. Click +Add , Click OPT2 (depend on your device) ![file-20260313103000314](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313103000314.png)
4. Check Enable interface
	1. Change Description to IoT (or any description)
	2. Select Static IPv4 on dropbox  , keep IPv6 blank![file-20260313103233921](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313103233921.png)
	3. Setting Static IPv4 
		1. IPv4 Address to 10.10.10.1 , subnet mask 24 (or change to your perference) ![file-20260313103823767](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313103823767.png)
		2. Keep Upstream gateway blank
	4. Keep Reserved Networks unchecked ![file-20260313103556891](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313103556891.png)
	5. Click Save
	6. Click Apply Changes ![file-20260313103741202](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313103741202.png) And it will display like ![file-20260313104527644](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313104527644.png)
5. Repeat above step to create Serers, Clients, MGMT ![file-20260313104457005](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313104457005.png)

### Step 3 Set up DHCP Server
1. Go to Services -> DHCP Server ![file-20260313104740133](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313104740133.png)
2. Select IOT Tab ![file-20260313104836518](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313104836518.png)
3. Enable DHCP server on interface ![file-20260313104926615](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313104926615.png)
4. Add Pool Range from 10.10.10.100 to 10.10.10.200 ![file-20260313105047643](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313105047643.png)
5. Add DNS Servers as need, or keep it blank ![file-20260313105723973](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313105723973.png)
6. Click Save, Apply Changes
7. Repeat above step to configure Server, Client, Management, IP address range will be like:
	1. Server : 10.10.20.100 - 10.10.20.200
	2. Client : 10.10.30.100 - 10.10.30.200
	3. Management : 10.10.40.100 - 10.10.40.200

### Step 4 Set up Firewall Rules for VLAN
1. Go to Firewall -> Rules ![file-20260313110249497](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313110249497.png)
2. Click Add to create rule ![file-20260313110424421](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313110424421.png)
	1. Action : Pass
	2. Interface : IOT
	3. Address Family : IPv4
	4. Protocol : Any ![file-20260313110558287](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313110558287.png)
	5. Source : IOT subnets (Make Sure is IOT subnets, not IOT address)
	6. Destination : Any ![file-20260313110812254](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313110812254.png)
	7. Add Description as need ![file-20260313110844873](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313110844873.png)
	8. Click Save, Apply Changes

> [!Important] 
> This rule allow any device which connected to this VLAN can not only connect to internet but also your private network, such as different VLAN and devices on same VLAN. 
> So, I will set up addition rules for IOT VLAN because we do not want IOT can access your NAS or laptop and so on.

#### More Firewall Rules
- Set up Firewall Aliases 
	1. Management Port : 
		1. Go to Firewall -> Aliases -> Ports ![file-20260313114120433](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313114120433.png)
		2. Click +Add
		3. Add 80 for http, 443 for https, 22 for ssh ![file-20260313114245762](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313114245762.png)
		4. Click Save, Apply Changes
	2. Priveate IPs :
		1. Go to Firewall -> Aliases -> IP ![file-20260313114414896](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313114414896.png)
		2. Make Sure select Type : Networks, add 10.0.0.0/8 , 172.16.0.0/12 , 192.168.0.0/16 ![file-20260313114526351](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313114526351.png)
		3. Click Save, Apply Changes

- Set up Firewall Rules 
1. Deny IOT VLAN access your pfSense web ui
	1. Action : Block
	2. Interface : IOT
	3. Address Family : IPv4
	4. Protocol : TCP ![file-20260313113441675](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313113441675.png)
	5. Source : IOT subnets
	6. Destination : IOT address (this address can use to access pfSense web ui, so is better to deny the packet)
	   Destination Port Range : (other) Management_Ports ![file-20260313114900338](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313114900338.png)
	7. Add Description as need
	8. Save, Apply Changes
2. Allow DNS request
	1. Action : Pass
	2. Interface : IOT
	3. Address Family : IPv4
	4. Protocol : UDP ![file-20260313115758763](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313115758763.png)
	5. Source : IOT subnets
	6. Destination : IOT address
	7. Destination Port Range : DNS(53) ![file-20260313115901538](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313115901538.png)
	8. Save, Apply Changes
3. Block private IPs
	1. Action : Block
	2. Interface : IOT
	3. Address Family : IPv4
	4. Protocol : Any ![file-20260313120304777](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313120304777.png)
	5. Source : IOT subnets
	6. Destination : Address or Aliases Private_IPs ![file-20260313120402675](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313120402675.png)
	7. Save, Apply Changes

> [!Important]
> Firewall Rules order is from top to down, make sure the order look like below ![file-20260313120659943](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313120659943.png) 
> Want to change order, just drag the rule to the place, and click save, apply changes.
> 


## Taste
So far, your VLAN should able to connect to internet.
To verify this, I created a LXC on my Proxmox host. Any LXC or VM could use to taste it. I turn off firewall on Proxmox for this LXC to make verify more easy to implement.
1. Configure Network for LXC, add VLAN Tag : 10 , select DHCP for IPv4 ![file-20260313122300265](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313122300265.png)
2. Use `ip addr` to check are we in proper VLAN and is DHCP working. ![file-20260313122640979](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313122640979.png)
3. Use `ping 8.8.8.8` to check can we access internet. ![file-20260313122758396](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313122758396.png)
4. Use `ping 10.10.10.1` to check can we connect our private IP. This IP is pfSense gateway for this VLAN 10. But we only allow it request DNS Port (53), and ping uses ICMP protocol not UDP which DNS uses. So this address cannot be ping. ![file-20260313122837458](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313122837458.png)
5. Use `dig google.com` to check is DNS working properly. If you did not install dig, install first. ![file-20260313123654558](assets/Configure%20VLAN%20with%20pfSense%20-%20part1/file-20260313123654558.png)

## Notes
If you cannot connect internet. Checking Firewall -> NAT -> Outbound is set to Automatic or Hybird. 
You can modify specific rules for every VLAN, such as not allow Server or Client to access Management VLAN.
> [!Tip]
> The Firewall rules can also modify by Proxmox Firewall. I will implement Proxmox Firewall on part 2. Part 1 showing how to configure Firewall on pfSense.

## References & Resources
I use Gemini to help me set up this environment. Any AI tools can help on troubleshooting, but sometime AI may make mistakes. It's good to understand the whole concept of networking. Below is some good video on Youtube could help you understand.

VLAN explained
https://www.youtube.com/watch?v=jC6MJTh9fRE&pp=ygUEdmxhbg%3D%3D 

Proxmox install
https://www.youtube.com/watch?v=5j0Zb6x_hOk&t=139s&pp=ygUPcHJveG1veCBpbnN0YWxs

pfSense install on Proxmox
https://www.youtube.com/watch?v=hdoBQNI_Ab8&t=124s