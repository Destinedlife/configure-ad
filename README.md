<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />


<h2>Video Demonstration</h2>

- ### [YouTube: How to Deploy on-premises Active Directory within Azure Compute](https://www.youtube.com)

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Setup Domain Controler Controller in Azure
- Setup Clinet-1 in Azure
- 
- Step 4

<h2>Deployment and Configuration Steps</h2>

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
First the step seting up Active Directory (AD) is to create a Domain Controller. Open Azure Protal and create a resource group named "Active-Directory-lab". Choose any region you want. Next create A virtual netowrk and name it "Active-Directory-Vnet". There is no need to change any settings. Now go virtual machine (VM) and create new Azure virtual machine. Change these settings

  - **Resource Group**: Active-Directory-lab
  - **name**: dc-1
  - **Region**: (use the same region as the resource group)
  - **Image** (Operating System): Windows Server 2022 Datacenter: Azure Edition - x64 Gen2
  - **Size**: Standard_D2s_v3 - 2 vcpus, 8 GiB memory

  - **Username:** labuser
  - **Password:** Cyberlab123!
    
    ****Only use this username and password for this tutorial!!!****

    Then check both boxes under Licensing
    Next on the netowrking page of the virtaul machine pick "Active-Directory-Vnet" then hit create.
    
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Once the Dc-1 VM deploys, create another VM with all the same settings except these:

- **Name:** cilent-1
- **Image:** Windows 10 Pro, version 22H2 - x64 Gen2

- Use the same username and password from dc-1.
- Check the box under licensing

  On the networking panel make sure the virtual network is set to "Active-Directory-Vnet"
  
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now that both of the VM are deployed the Private IP address must be changed. Go home > Virtual Machine > dc-1 > Network > Network settings. On this page, click on the Network interface towards the top. In the new window click "ipconfig1" then change allocation to 
static. then click save.
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now copy dc-1 public IP Address from the virtual machine page. Then remotely connect to it using Remote Desktop Connection and the username and password from earlier. If everything was setup correctly sever manager will launch automatically. Then Right-click the start menu > run. Then type "wf.msc" for windows firewall. 
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Click windows defender Firewall Propeities and set fire wall state to off and hit apply.

**Only turning off the firewall for ease of the tutorial, don't do this in real life**

Now cilent-1 needs DNS settings needs to be set DC-1's Private IP Address. Back in go Azure go to Dc-1 ( Home > Virtual machine > Dc-1 > overview) copy the private IP Address under networking. the go cilent-1 > networking and click on the network interface. On the left under settings go to DNS Severs and click custom server. Then paste the private IP address.

</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now from the Virtual machine page restart cilent-1. 

</p>
<br />

