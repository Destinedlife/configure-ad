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

![Artboard 1](https://github.com/user-attachments/assets/e770331d-6a3d-4cb1-83f4-4ec12aa42c51)

<p>
First, the step in setting up Active Directory (AD) is to create a Domain Controller. Open Azure Portal and create a resource group named "Active-Directory-lab". Choose any region you want. Next, create A virtual network and name it "Active-Directory-Vnet". There is no need to change any settings. Now, go the virtual machine (VM) and create a new Azure virtual machine. Change these settings

  - **Resource Group**: Active-Directory-lab
  - **name**: dc-1
  - **Region**: (use the same region as the resource group)
  - **Image** (Operating System): Windows Server 2022 Datacenter: Azure Edition - x64 Gen2
  - **Size**: Standard_D2s_v3 - 2 vcpus, 8 GiB memory

  - **Username:** labuser
  - **Password:** Cyberlab123!
    
    ****Only use this username and password for this tutorial!!!****

    Then check both boxes under Licensing
    Next on the networking page of the virtaul machine pick "Active-Directory-Vnet" then hit create.
    
<br />

![Artboard 2](https://github.com/user-attachments/assets/84bafdb0-43bd-4ab4-927d-ea8d984a2760)


<p>
Once the Dc-1 VM deploys, create another VM with all the same settings except these:

- **Name:** cilent-1
- **Image:** Windows 10 Pro, version 22H2 - x64 Gen2

- Use the same username and password from dc-1.
- Check the box under licensing

  On the networking panel make sure the virtual network is set to "Active-Directory-Vnet"
  
</p>
<br />

![Artboard 3](https://github.com/user-attachments/assets/eba5f521-0217-40c7-88f6-82b3ced39e99)

<p>
Now that both VMs are deployed, the Private IP addresses must be changed. Go home > Virtual Machine > dc-1 > Network > Network settings. On this page, click on "Network interface" towards the top. In the new window click "ipconfig1" and then change allocation to 
static. Then click save.
</p>
<br />

![Artboard 4](https://github.com/user-attachments/assets/e8190187-f748-4a48-8a74-d4ca37e11c2f)

<p>
Now copy dc-1 public IP Address from the virtual machine page. Then remotely connect to it using Remote Desktop Connection and the username and password from earlier. If everything was setup correctly sever manager will launch automatically. Then Right-click the start menu > run. Then type "wf.msc" for windows firewall. 
</p>
<br />

![Artboard 5](https://github.com/user-attachments/assets/6f8ba43c-8e09-4fba-9243-a154d17a636d)

<p>
Click Windows Defender Firewall Properties and set the firewall state to off on the Domain, private, public profile, and hit apply.

**Only turning off the firewall for ease of the tutorial, don't do this in real life**

Now, client-1 DNS settings need to be set to DC-1's Private IP Address. Back in Azure, go to Dc-1 ( Home > Virtual machine > Dc-1 > overview) and copy the private IP Address under networking. Then, go to client-1> networking and click on the network interface. Navigate to settings on the left, go to DNS Severs, and click Custom Server. Then, paste the private IP address.

</p>
<br />


![Artboard 6](https://github.com/user-attachments/assets/6f74acd2-6c5e-4ad2-a4bb-13aeb2fa1743)

<p>
From the Virtual machine page restart cilent-1. Once client-1 restarts, remotely connect to it and open Powershell. To ensure that dc-1 and cilent-1 are properly connected ping them using Powershell.  In Powrshell type "ping (DC-1 Private IP Address)". If the ping is successful, type "ipconfig /all" to see the output for the DNS servers is dc-1's private IP Address (10.0.0.4).

**If you want to take a break and save money in your Azure account Go back to Azure portal > Virtual machine then select your VM and click stop. When you are ready to continue select Start on this page.**
</p>
<br />

<h3>Install Active Directory</h3>

![Artboard 7](https://github.com/user-attachments/assets/69a8f19c-f4fc-42f7-95ff-4ae94b37eedf)

<p>
Next, connect to dc-1 and open the server manager. Click Add Roles and Features then hit Next until you get to server selection. Then, click Active Directory Domain Services > add features. Click next until the confirmation page, check "restart...". and hit install.
</p>
<br />

![Artboard 8](https://github.com/user-attachments/assets/f0fad61b-415f-4c85-a38f-4f0cafa547d7)


<p>
Once installed, in server manager, Click the flag on the top right then click domain "Promte this server to a domain controller". Select Add a new forest and name the root domain "mydomain.com". Click next and set the password to "Password1". Then hit next until the prerequisites check. Then hit install. It should automatically restart. Now log in to dc-1 but this time the username will be "mydomain.com\labuser". This is necessary to log in as a domain user.
</
<br />

  <h3>Create a Domain Admin user with the domain</h3>

![Artboard 9](https://github.com/user-attachments/assets/aceb95fa-29f4-4326-9f6b-fdf0253e00f8)

<p>
Open active directory users and computers. Then right-click mydomain.com > New > Organizational Unit (OU). Name the new unit "_EMPLOYEES" and create another named "_ADMINS" (they must be spelled exactly like this, so feel free to copy and paste). The next step is to add users to these folders. Right-click on _ADMINS > new > User. Put in the following: 

  - name: Peter Quill
  - username: peter_admin
  - Password "Cyberlab123!"

Save this in a note

  ***Only do the following for this tutorial, don't do this in real life:***
  - Uncheck " User must change password at next logon"
  - check "password never expires" 

</p>
<br />

![Artboard 10](https://github.com/user-attachments/assets/10b0f07b-5c3f-496c-b76d-d4372e550e2e)


<p>
In order to make Peter an admin he must be added to the "domain Admin" security group. Right-click on Peter > properties > members of. Type "Domain admins", click domain names, and click ok. Then log out of labuser and log in as Peter_ admin
</p>
<br />

 <h3>Join cilent-1 to your domain(mydomain.com)</h3>

![Artboard 11](https://github.com/user-attachments/assets/127c5c7d-f7fe-42f5-8469-8d73f2ba2168)

<p>
Log into client-1 as labuser. Right-click on the start menu > system. On the right side click on "Rename this pc advance". Then click change, select domain under "member of", and type "mydomain.com". In order to change domains an Admin login must used. Log in with mydomain.com\peter_admin and click ok to restart the VM.
</p>
<br />


![Artboard 12](https://github.com/user-attachments/assets/f295b19c-b1d9-4a97-9bd1-68a4274b136a)

<p>
Log back into dc-1 and confirm that client-1 appears in Active Directory. Open Active Directory user and computers> mydomain.com > computers. Client-1 should be in this OU.
  **If you want to take a break and save money in your Azure account Go back to Azure portal > Virtual machine then select your VM and click stop. When you are ready to continue select Start on this page.**
</p>
<br />

 <p>
   <h3>Setup Remote Desktop for Non-adminstrative users on cilent-1</h3>


![Artboard 13](https://github.com/user-attachments/assets/6046450e-dd9b-409b-8eda-229152b0c623)

</p>
<p>
Next, Log into client-1 as mydomain.com\jane_admin. Right-click on the start menu > system. Then on the right, click remote desktop > select user that can remotely access this PC. Then click add and type "domain users" and click ok. By doing this non-administrative users are now able to remotely access Cilent-1.
</p>
<br />

 <p>
   <h3>Create a additional users and attempt to log into cilent-1 with one users</h3>

![Artboard 14](https://github.com/user-attachments/assets/a23635e0-b152-4ca2-a0f5-08334ba134c1)

</p>
<p>
log in to dc-1 as Peter_admin. Next, open Powershell_ise as an administrator by right-clicking and then clicking run as administrator. Copy this 
  
  [script](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1) to create users. In Powershell, create a new file (top left paper icon) and paste the script. Press CRTL+S and save the script to the desktop. Name it "create-users". By running this script it will create 10000 users with random names. Then click the play button to run the script.
</p>
<br />

![Artboard 15](https://github.com/user-attachments/assets/6442ada9-7b71-4fff-b7bc-8edd734ae8df)

<p>
Now back in Active directory user and employees in the _EMPLOYEES OU, there should be a ton of employees. Pick a random name and save it in your notes. Next log in cilent-1 with that employee.

  - Username: mydomain.com\(employee name)

      EX: mydomain.com\bev.xij

- Password: Password1

Open File Explorer and go to This PC > Windows (C:) > Users. Your employee's local folder should be there. Log out of client-1 and back on dc-1 stop the script if it's still running.
</p>
<br />

<h3>Setup Group policies and How to Handle Account Lockouts</h3>

![Artboard 16](https://github.com/user-attachments/assets/ffe7c54c-c6da-4b59-9ffe-6a13fba3a4ad)

<p>
Log in to dc-1 and open Group Policy Management. Go to Forest > Domains > mydomain.com. Right-click the default domain Policy and hit edit. Then go to Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies > Account Lockout Policy.
  Double-click account lockout duration. Set the time to 30 mins and hit apply. The rest of the settings will automatically be changed to the following:
 
  - account lockout threshold: 5 attempts
  - Reset account lockout after: 10 mins

Enable "allow administrator account lockout."
  
</p>
<br />

![Artboard 17](https://github.com/user-attachments/assets/64d8cf49-10a5-4ab1-8903-a653b0385d23)

<p>
Log in to client-1 as Peter_admin and open CMD. Type "gpupdate /force" to force the group policy to update on the domain. log out of peter_ admin on cilent-1.
</p>
<br />

![Artboard 18](https://github.com/user-attachments/assets/ee707a05-41b4-4b26-9d64-52bf94f14627)

<p>
Log in to dc-1 and pick a random user that was created previously (save this user in your notes for later). Then, log in with the user you picked and use a bad password 6 times. A message should appear that the account has been locked out.
</p>
<br />

![Artboard 19](https://github.com/user-attachments/assets/9cad6b1f-f764-488d-b1d5-4285ce5c0627)

<p>
Back in active directory, find your user and open properties. You can search for the user by right-clicking Mydomain.com and go to find. Inside the user properties go to account, check unlock account, and hit apply. Then log in to client-1 as the user correctly.
</p>
<br />

<h3>Enabling and disabling accounts</h3>

![Artboard 20](https://github.com/user-attachments/assets/b04e51c3-f10d-4a2f-b84e-93392583da06)

<p>
Go to active directory and find the user again. Right-click on the user and click disable account. Then try to log into the user and view the error message. Then reenable the account in active directory</p>
<br />


<h3>observe the log</h3>
 <p>
![Artboard 21](https://github.com/user-attachments/assets/824633ea-2435-4a51-abd5-b61b89b64f86)

</p>
Log into dc-1 as peter_admin. In the taskbar, search for "eventvwr". Then go to Windows logs > security. Right-click on securtiy > find and find your user. Now all the logs from this user are able to be seen. Now log in as client-1 with the user. In the taskbar, search for "eventvwr.msc" and open it as an admin. Log in as Peter_admin. Then go to Windows logs > security. Right-click on security. Now all log-ins, outs, and failures are viewable. Click on one that says "audit failure" and see why it failed below.

Congratulations, Active directory has been successfully configured.
</p
<br />








