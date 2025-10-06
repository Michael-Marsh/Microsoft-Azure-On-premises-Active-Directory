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
Size: Standard_E2s_v3 - 2 vcpus, 16 GiB memory <br/>
<img src="Screenshot (58).png" height="80%" width="80%" alt="Screenshot (58)"/>

The second virtual machine will be the Client.

Name: Client-1
Availability Zone: No Infrastructure Redundancy Required
Image: Windows 10-22H2
Size: Standard_E2s_v3 - 2 vcpus, 16 GiB memory <br/>
<img src="Screenshot (59).png" height="80%" width="80%" alt="Screenshot (59)"/>

<h3>Step 2: Ensure Connectivity Between the Client and Domain Controller</h3>

Login to Client-1 using Microsoft Remote Desktop
Search for Command Prompt and open it
Ping DC-1's private IP Address (for example, 10.1.0.4)
Type "ping -t 10.1.0.4" into the command-line interface
The ping request continually times out due to the firewall settings
To fix this, we need to enable ICMPv4 on DC-1's local Windows firewall <br/>
<img src="Screenshot (28).png" height="80%" width="80%" alt="Screenshot (28)"/>

Login to DC-1 using Microsoft Remote Desktop
Start > Windows Administrative Tools > Windows Defender Firewall with Advanced Security > Inbound Rules
Sort the list by protocols
Find "Core Networking Diagnostics" and "ICMPv4" and enable these two inbound rules <br/>
<img src="Screenshot (31).png" height="80%" width="80%" alt="Screenshot (31)"/>

Log back into Client-1 and the command line will automatically begin pinging DC-1 successfully <br/>
<img src="Screenshot (32).png" height="80%" width="80%" alt="Screenshot (32)"/>

<h3>Step 3: Install Active Directory</h3>

Log back into DC-1
Open Server Manager
Select "Add Roles and Features" > Follow the prompts
At Server Roles, check "Active Directory Domain Services."
Ignore how the picture below already says "Installed"
Select Add Features > select Next
Complete the installation <br/>
<img src="Screenshot (34).png" height="30%" width="30%" alt="Screenshot (34)"/>

At the top right of the Server Manager Dashboard, click on the flag
Select "Promote This Server to a Domain Controller" <br/>
<img src="Screenshot (35).png" height="50%" width="50%" alt="Screenshot (35)"/>

Select "Add a New Forest"
Root domain name: mydomain.com
Select Next
Create a password
Select Next and follow the prompts
Select Install to complete the installation <br/>
<img src="Screenshot (36).png" height="50%" width="50%" alt="Screenshot (36)"/>

DC-1 will automatically restart
Log back into DC-1 as user: mydomain.com\labuser <br/>
<img src="Screenshot (37).png" height="50%" width="50%" alt="Screenshot (37)"/>

<h3>Step 4: Create an Admin and Normal User Account in Active Directory</h3>

On DC-1, open Server Manager
Click Tools at the top-right of the screen
Select Active Directory Users and Computers <br/>
<img src="Screenshot (38).png" height="50%" width="50%" alt="Screenshot (38)"/>

Right-click mydomain.com > New > Select Oranizational Unit (OU)
Create two OUs
Name the first "_EMPLOYEES"
Name the second "_ADMINS" <br/>
<img src="Screenshot (39).png" height="80%" width="80%" alt="Screenshot (39)"/>

Right-click mydomain.com and click Referesh to sort the new organizational units to the top
Go to the _ADMINS OU
Right-click the name of the OU > New > User
First/Last name: Jane Doe
User login name: jane_admin
Select Next
Create a password
Uncheck all boxes
Select Next and then select Finish <br/>
<img src="Screenshot (40).png" height="80%" width="80%" alt="Screenshot (40)"/>
<img src="Screenshot (41).png" height="50%" width="50%" alt="Screenshot (41)"/>

Go to the _ADMINS OU
Right-click Jane Doe > select Properties
Click the tab named "Member of" > select Add
Type in the names of your domain administrators
Select "Check Names" > OK > Apply
Log out of DC-1 as "labuser" and log back in as “mydomain.com\jane_admin” <br/>
<img src="Screenshot (42).png" height="50%" width="50%" alt="Screenshot (42)"/>
<img src="Screenshot (43).png" height="50%" width="50%" alt="Screenshot (43)"/>

<h3>Step 5: Join Client-1 to your domain (mydomain.com)</h3>
Go back to the Azure portal
Navigate to the Client-1 Virtual Machine
On the left-hand side of the screen select Networking
Select the link next to the NIC > select DNS Server > Custom
Type in DC-1's private IP address
Click Save
After it is done updating, select Restart and select Yes <br/>
<img src="Screenshot (45).png" height="80%" width="80%" alt="Screenshot (45)"/>
<img src="Screenshot (46).png" height="80%" width="80%" alt="Screenshot (46)"/>

Log back into Client-1 using Microsoft Remote Desktop as the original local admin (labuser)
Right-click the Start menu and select System
On right-hand side of the screen, select Rename This PC (Advanced) > Change
Under "Member of," select Domain
Type "mydomain.com" and select OK
Username: mydomain.com\jane_admin
Type in password and press OK
Restart the computer <br/>
<img src="Screenshot (48).png" height="80%" width="80%" alt="Screenshot (48)"/>

<h3>Step 6: Setup Remote Desktop for non-administrative users on Client-1</h3>
Log back into Client-1
Use mydomain.com\jane_admin
Right-click the Start menu and select System
On the right-hand side of the screen, select Remote Desktop
Under User Accounts, click "Select Users That Can Remotely Access This PC > select Add
Type in the name of your domain users
Select "Check Names" > OK > OK <br/>
<img src="Screenshot (55).png" height="50%" width="50%" alt="Screenshot (55)"/>

<h3>Step 7: Create as many additional users as you would like and attempt to log into Client-1 with one of the users' profiles</h3>

Log back into DC-1 as jane_admin
Search for "Powershell_ise,"
Right-click on Powershell_ise and open it as an administrator <br/>
<img src="Screenshot (49).png" height="80%" width="80%" alt="Screenshot (49)"/>

At the top-left of the screen select New Script and paste the contents of the following script into it
You can find the script here
Click the green arrow button near the top-middle of the screen
This will run the script
Once the users have been created, go back to Active Directory Users and Computers > mydomain.com > _EMPLOYEES - You will see all the accounts that were created
You can now log into Client-1 with one of the accounts that were created.
Try logging into Client-1 as any user using the password "Password1" <br/>
<img src="Screenshot (50).png" height="80%" width="80%" alt="Screenshot (50)"/>
<img src="Screenshot (52).png" height="80%" width="80%" alt="Screenshot (52)"/>

Congratulations! You have implementated on-premises Active Directory and created users within an Azure virtual machine!

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
