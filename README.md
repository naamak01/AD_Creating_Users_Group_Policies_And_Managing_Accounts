![image](https://github.com/user-attachments/assets/13d619f0-7bbd-4a58-8d38-c261f5ba6ae1)

# Creating Users, Group Policy, and Managing Accounts with Active Directory in Azure

## Description

**This project will demonstrate how to configure Remote Desktop for non-administrative users, automate user creation with PowerShell, and manage group policies. Additionally, I’ll cover account lockouts and log monitoring to simulate a real-life IT environment. Join me as I dive deeper into deploying Active Directory and enhancing system management skills!**

This is a continuation of the [Active Directory: Deploying Active Directory](https://github.com/naamak01/AD_Deploying_Active_Directory) in Azure project.

You can find the Powershell script I run in this project [here](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1).

## Environments and Utilities Used
* Microsoft Azure
* Virtual Machines
* Remote Desktop Connection
* Active Directory

## Operating Systems Used
* Windows Server
* Windows 10

## Project Walk-through: 

To start, log into client-1 as the domain admin (jane_admin), using Remote Desktop Connection:
NOTE: It will take some time to load since it will be the first time login in as a client

I want to allow Remote Desktop for non-administrative users. So, right-click on the start button > System > on the right-side click "Remote Desktop" > "Select users that can remotely access this PC":

![image](https://github.com/user-attachments/assets/049ba2e2-22fd-46d0-9287-693ab82ee1e0)

Add "DOMAIN USERS" To allow all users
Here, enter "Domain Users" > Check Names > OK > OK. Now all domain users can access this client via Remote Desktop:

![image](https://github.com/user-attachments/assets/9997bb59-cd73-43d7-aefe-c5e526ad7957)

Next, we'll create a bunch of users in Powershell. To start, log into the DC (Domain Controller) as our admin (jane_admin):

![image](https://github.com/user-attachments/assets/3fbbc7aa-8a44-4c54-a163-cbe80cf28e22)

Use Ctrl + S and save this like so:
Now, we can copy and paste the user creation script in the top section and click the "Run" button which has a green play symbol located in the top bar:

![image](https://github.com/user-attachments/assets/53ce2a93-37c6-402e-91f1-420697867592)

Powershell will begin to run the script and create random users and place them in the OU (Organizational Unit), we created in the previous section of this project, named "_EMPLOYEES". We can check by searching "Active Directory Users and Computers" > mydomain.com > _EMPLOYEES. Here we will pick a user and log into client-1 using this username and the password the script sets for all of these users which is "Password1":

![image](https://github.com/user-attachments/assets/0a077856-fa22-48dd-ada7-710720a756c1)

Now, log out of Jane's account on client-1:

We can log back into client-1 using our newly created user, making sure to specify the context of which we want to log in by adding "mydomain.com\" before the username:

![image](https://github.com/user-attachments/assets/5c61a45b-65ec-4960-bfde-e79b1d7f119e)

Even opening the command line you can tell the user name is there in the C drive

![image](https://github.com/user-attachments/assets/6dc8014a-b017-4dee-80a1-f851b66bdacd)

When we're logged in we can see a user folder for our user if we click on file explorer > C: > Users. We can also see the folder for the other users that have signed into this client. However, we cannot access them, because we don't have those permissions as a user:

![image](https://github.com/user-attachments/assets/83b3c748-0101-4236-a5f8-1e80baa5fee4)





























