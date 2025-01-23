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
Click windows defender Firewall Propeities and set fire wall state to off on the Domain, private, public profile and hit apply.

**Only turning off the firewall for ease of the tutorial, don't do this in real life**

Now cilent-1 needs DNS settings needs to be set DC-1's Private IP Address. Back in go Azure go to Dc-1 ( Home > Virtual machine > Dc-1 > overview) copy the private IP Address under networking. the go cilent-1 > networking and click on the network interface. On the left under settings go to DNS Severs and click custom server. Then paste the private IP address.

</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now from the Virtual machine page restart cilent-1. Once cilent-1 restarts, remotely connect to it and open powershell.To ensure that dc-1 and cilent-1 are properly connected ping them using Powershell.  In Powrshell type "ping (DC-1 Private IP Address)". If the ping is succcessful, type "ipconfig /all" to see the output for the DNS servers is dc-1's private IP Address (10.0.0.4).

**If you want to take a break and save money in your Azure account Go back to Azure portal > Virtual machine then select your VM and click stop. When you are ready to continue select Start on this page.**
</p>
<br />

<h3>Install Active Directory</h3>

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Next connect to dc-1 and open server manager. Click add roles and features then hit next until you get to server selection. Then, click Active Directory Domain Services > add features. Click next umtil the confirmation page, check "restart...". and hit install.
</p>
<br />


<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Once installed, back in server manager, Click the flag on the top right then click domain "Promte this server to a domain controller". Select add a new forest and name the root domain "mydomain.com". click next and set the password to "Password1". then hit next until the prerequisites check. then hit install. It should automatically restart. Now relog in to dc-1 but this time the username will "mydomain.com\labuser". This is neccessary to log in as a domain user.
</
<br />

  <h3>Create a Domain Admin user with the domain</h3>

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Open active directory users and computers. Then right-click mydomain.com > New > Organizational Unit (OU). Name the new unit "_EMPLOYEES" and create another named "_ADMINS" (they must be spelled exactly like this, so feel free to copy and paste). the next step is to add users to these folders. Right-click on _ADMINS > new > User. Put in the following: 

  - name: Peter Quill
  - Logon: peter_admin
  - Password "Cyberlab123!"

Save this in a note

  ***Only do the following for this tutorial, don't do this in real life:***
  - Uncheck " User must change password at next logon"
  - check "password never expires" 

</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
In order to make Peter an admin he must be added to the "domain Admin" security group. Right-click on Peter > properties > members of. Type "Domain admins", click domain names, and click ok. Then log out of labuser and relog in as Peter_ admin. 
</p>
<br />

 <h3>Join cilent-1 tp your domain(mydomain.com)</h3>

 <p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
log into clint-1 as labuser. Right-click on the start menu > system. On right side click on "rename this pc advance". Then click change, select domain under member of, and type "mydomain.com". In order to change domains an Admin log in must used. Log with mydomain.com\peter_admin click ok to restart the VM.
</p>
<br />

 <p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Log back into dc-1 and confirm that cilent-1 appears in active directory. open active directory user and compueters > mydomain.com > computers. Clinet-1 should be in this OU.
  **If you want to take a break and save money in your Azure account Go back to Azure portal > Virtual machine then select your VM and click stop. When you are ready to continue select Start on this page.**
</p>
<br />

 <p>
   <h3>Setup Remote Desktop for Non-adminstrative users on cilent-1</h3>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Next Log into cilent-1 as mydomain.com\jane_admin. Right-click on the start menu > system. Then on the right click remote desktop > select user that can remotely access this PC. Then click add and type "domain users" and click ok. By doing this non-administrative users are now able to remotely access Cilent-1.
</p>
<br />

 <p>
   <h3>Create a additional users and attempt to log into cilent-1 with one users</h3>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
log in to dc-1 as Peter_admin. Next, open Powershell_ise as a administrator by right-clicking then clcking run as adminastrator. Copy this 
  
  [script](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1) to create users. In Powershell, create a new file (top left paper icon) and paste the script. Press CRTL+S and save the script to the desktop. Name it "create-users". By running this script it will create a 10000 users with random names. Then click play button to run the script
</p>
<br />

 <p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now back in Active directory user and employees in the _EMPLOYEES OU there should be a ton of employees. Pick a random name and save it in your notes. Next log in cilent-1 with that employee.

  - Username: mydomain.com\(empolyee name)

      EX: mydomain.com\bev.xij

- Password: Password1

open file explorer and go to This PC > windows(C:) > Users. your employee local folder should be there. Log out of cilent-1 and back on dc-1 stop the script if its still running.
  
</p>
<br />








