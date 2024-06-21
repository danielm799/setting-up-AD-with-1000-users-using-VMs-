# setting-up-AD-with-1000-users-using-VMs-
Just showcasing this side project I did to practice with Active Directory and virtual machines
+ I went ahead and installed VirtualBox v7.0.18 with the extension pack
+ I also downloaded the ISO of Windows 2019 and Windows 10 media installer to get the ISO (took a while) and I downloaded the PS script to add 1000 users to a domain
![1000000729](https://github.com/danielm799/setting-up-AD-with-1000-users-using-VMs-/assets/33638895/429a13a5-f92b-42c0-8ec8-84e2a8de5014)
---
+ I installed Windows server 2019 with its Desktop Experience into a VM named DC. I gave it 2 GB of RAM, 20 GB of storage, and gave it two network adapters: one to connect to the internet and one to point to a virtual internal network
![1000000730](https://github.com/danielm799/setting-up-AD-with-1000-users-using-VMs-/assets/33638895/1267ff1c-7983-46ae-aff2-0e7e4e163816)
---
+ Once the OS was installed, I went to settings to change the names of each network adapter so I could easily identify which one is the internal adapter
+ I set up the IP address of the \_INTERNAL to 172.16.0.1, subnet mask to 255.255.255.0, DNS to 127.0.0.1. I set it to the loopback address because the DNS server was going to be hosted on the server itself, so it would only need to reference itself
---
+ I went to server manager to install active directory, and named the domain testdomain.com. I made an OU called \_ADMINS, made a user named Daniel Medina and put it in the OU, and then made Daniel Medina a member of the Domain Admin group
+ I logged out then logged back into the domain this time as a-dmedina, to do further configurations
![1000000731](https://github.com/danielm799/setting-up-AD-with-1000-users-using-VMs-/assets/33638895/05dbe11e-9201-4c5c-9734-d0487642c9e2)
![1000000732](https://github.com/danielm799/setting-up-AD-with-1000-users-using-VMs-/assets/33638895/ee9ffc98-954a-44c2-8d1c-76c998df2645)
+ I installed remote access to enable RAS and NAT to allow connection to the internet through the DC (the 2019 server VM)
+ I opened Routing and Remote Access in Tools and set up NAT on the \_INTERNET NIC
![1000000733](https://github.com/danielm799/setting-up-AD-with-1000-users-using-VMs-/assets/33638895/0411e423-04ec-4278-a183-8ac42d4e9ea8)
![1000000734](https://github.com/danielm799/setting-up-AD-with-1000-users-using-VMs-/assets/33638895/359ed880-dbef-449d-b082-cfb4317bff96)
---
+ I installed a DHCP server to allow internal clients to get an address from the DC to connect to the internet through the DC.
+ Server manager → tools → DHCP. I made a new “scope”, naming it 172.16.0.100-200. I set range of IP addresses as 172.16.0.100 to 172.16.0.200. The gateway was set to 172.16.0.1 and the subnet mask was 255.255.255.0.
	+ also kept the lease time at the default 8 days
+ I right clicked on dc.testdomain.com, clicked authorize, then clicked refresh. Both IPv4 and IPv6 produced a little green mark
+ I set up router in Server Options in DHCP tool, giving it the address 172.16.0.1
![1000000735](https://github.com/danielm799/setting-up-AD-with-1000-users-using-VMs-/assets/33638895/4b9136e2-c762-4c6d-afd9-e87b69f884e9)
![1000000736](https://github.com/danielm799/setting-up-AD-with-1000-users-using-VMs-/assets/33638895/1622e522-b9db-4e3a-9288-14944ba3ced3)
![1000000737](https://github.com/danielm799/setting-up-AD-with-1000-users-using-VMs-/assets/33638895/ead131b3-54eb-4a2e-b65f-70c35855e046)
![1000000738](https://github.com/danielm799/setting-up-AD-with-1000-users-using-VMs-/assets/33638895/8577a4e1-88a2-4f4a-a77c-4f91872fd87d)
---
+ I downloaded zip of PS code from github (insert link here). Opened PowerShell ISE, `cd` to the directory containing `names.txt` then ran the script called `1_CREATE_USERS.ps1`
+ It's not shown here, but I added my name, Daniel Medina, to `names.txt`
+ double check if the script was successful by looking for the \_USERS OU
![1000000739](https://github.com/danielm799/setting-up-AD-with-1000-users-using-VMs-/assets/33638895/c1671e25-0045-4b9d-93bd-2ff73593fadb)
![1000000740](https://github.com/danielm799/setting-up-AD-with-1000-users-using-VMs-/assets/33638895/1bfd5bb7-a611-4f72-b9a9-e7a1da9ec6ab)
---
+ I created a new VM called Client1, and assigned it one network adapter connected to the internal network. Installed the Windows 10 ISO into it, and selected the Windows 10 Pro version during installation
	+ I made sure to select the “For Business” option during setup, then the "Connect to domain" option
![1000000741](https://github.com/danielm799/setting-up-AD-with-1000-users-using-VMs-/assets/33638895/bcb1b2f2-db8d-40ff-93ac-1af4cfee4e75)
---
+ Once the installation and setup was complete, I ran the command prompt as an admin. I used the command `ipconfig` to see if the client got a valid IP address from the DC, then used `ping www.google.com` to see if both the DNS server is working and if the client can reach the internet (not shown, I had forgottent to take a screenshot :^\ )
![1000000742](https://github.com/danielm799/setting-up-AD-with-1000-users-using-VMs-/assets/33638895/b335e48c-c973-4163-9463-8effed355a4c)
---
+ I went to the “About” page in settings to find the “Rename this PC (advanced)” option. Then next to “To rename the computer…” I hit “Change”
+ I renamed the PC to CLIENT1, and made it a member of the domain, testdomain.com. I allowed the computer to restart
+ I confirmed on the DC if the client got an address from its DHCP server
![1000000743](https://github.com/danielm799/setting-up-AD-with-1000-users-using-VMs-/assets/33638895/d3cd1485-f79a-4014-9d1a-20326aae1b3b)
![1000000744](https://github.com/danielm799/setting-up-AD-with-1000-users-using-VMs-/assets/33638895/2c266bfd-6787-4566-ab9d-e96ec5d94ec2)
---
+ Once the restart is done, I logged into the Client1 VM as a user from testdomain.com to ensure that the functionality is working, then boom, done!!
![1000000745](https://github.com/danielm799/setting-up-AD-with-1000-users-using-VMs-/assets/33638895/d15afa18-949a-48ad-834d-6e41bd4e6a57)
