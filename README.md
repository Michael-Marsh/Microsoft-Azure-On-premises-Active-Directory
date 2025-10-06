<p align="center">
<img src="https://www.clarusco.com/wp-content/uploads/active-directory-logo.png" height="35% width="35%"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>

This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.
<br />

<h2>Environments and Technologies Used</h2>

- <b>Microsoft Azure</b> 
- <b>Microsoft Remote Desktop (RDP)</b>
- <b>Active Directory Domain Services</b>
- <b>PowerShell</b>

<h2>Operating Systems Used</h2>

- <b>Windows Server 2025</b>
- <b>Windows 11</b>

<h2>Deployment and Configuration Steps</h2>

<h3>Step 1: Setup Resources in Azure</h3>

Create two virtual machines. The first virtual machine will be the Domain Controller

Name: DC-1
Availabiltiy Zone: No Infrastructure Redundancy Required
Image: Windows Server 2025
Size: Standard_E2s_v3 - 2 vcpus, 16 GiB memory

The second virtual machine will be the Client.

Name: Client-1
Availability Zone: No Infrastructure Redundancy Required
Image: Windows 10-22H2
Size: Standard_E2s_v3 - 2 vcpus, 16 GiB memory
<img src="https://i.imgur.com/62TgaWL.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<h3>Step 2: Ensure Connectivity Between the Client and Domain Controller</h3>

DC-1 has to have a static Private IP Address. Client one will connect to DC-1 to ensure connectivity we will try to ping DC-1 from Client-1. At first the ping will not work correctly. We have to enable ICMPv4 on the firewall on DC-1. Now we can ping DC-1 successfully from Client-1.
<img src="https://i.imgur.com/62TgaWL.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

Now we will log back into DC-1 to install AD Users & Computers. Promote the VM to DC, setup a new forest as "mydomain.com" afterwards restart then log back into DC-1 as user: "mydomain.com\labuser". If you performed the steps properly you should be able to run AD Users & Computers as shown below.
<img src="https://i.imgur.com/62TgaWL.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

Excellent! We can start creating Organizational Units (OU). Let's first create an OU named _EMPLOYEES. Create another OU named _ADMINS. In order to do that right click on the domain area. Select new->Organizational Unit and fill out the field. Then click inside of your OU and right click, select new and select user and fill out the information for your new user. The user should be named Jane Doe, she is going to be an Admin so her username will be Jane_admin. Lastly add Jane to the domain admins security group.
<img src="https://i.imgur.com/62TgaWL.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

From now on you can use Jane_admin as the administrator account. Now we will join Client-1 to the domain (mydomain.com) from the azure portal we will change client-1's DNS settings to the DC's Private IP address. After you do that restart Client-1 from within the Azure portal. Our picture below shows verification that client-1 is on the DC-1 DNS.
<img src="https://i.imgur.com/62TgaWL.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

We have to join Client-1 to the domain in order to do so navigate to your system settings and go to about. Off to the right select rename this pc (advanced). From there select to change the domain. Enter "mydomain.com" after that enter your credentials from mydomain.com\labuser. Your computer will restart and then client-1 will be a part of mydomain.com.
<img src="https://i.imgur.com/62TgaWL.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

Wonderufl Client-1 is now a part of the domain. Now we will set up remote desktop for non-administrative users on Client-1. We have to log into Client-1 as an admin and open system properties. Click on "Remote Desktop", allow "domain users" access to remote desktop. After completing those steps you should be able to log into Client-1 as a normal user.
<img src="https://i.imgur.com/62TgaWL.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

Lastly to verify that noraml users can RDP into Client-1 we will use a script to generate thousands of users into the domain. We will input the script in powershell, after the users are created we will select one and RDP into Client-1.
<img src="https://i.imgur.com/62TgaWL.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

As you can see the Powershell script created a user with the username "bab.hubo" We were able to login to Client-1 with his credentials as a normal user.
<img src="https://i.imgur.com/62TgaWL.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

</p>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
