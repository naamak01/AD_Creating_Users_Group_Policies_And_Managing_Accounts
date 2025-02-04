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

We can log back into client-1 using our newly created user, making sure to specify the context in which we want to log in by adding "mydomain.com\" before the username:

![image](https://github.com/user-attachments/assets/5c61a45b-65ec-4960-bfde-e79b1d7f119e)

Even opening the command line you can tell the user name is there in the C drive

![image](https://github.com/user-attachments/assets/6dc8014a-b017-4dee-80a1-f851b66bdacd)

When we're logged in we can see a user folder for our user if we click on File Explorer> C: > Users. We can also see the folder for the other users that have signed into this client. However, we cannot access them, because we don't have those permissions as a user:

![image](https://github.com/user-attachments/assets/83b3c748-0101-4236-a5f8-1e80baa5fee4)

![image](https://github.com/user-attachments/assets/456abdc8-0fc1-4283-a7b0-bba0168f2630)

With Active SDirectory Opened, Pick a random user from the List of employees. Here, we are going to try to log in several times and fail with the goal of practicing how to reset passwords for users and also setting password limits on how many times a user can fail to log in for security purposes.

We doing this in DC-1

![image](https://github.com/user-attachments/assets/82aa0a8b-ff56-49d5-bcff-52ce144dda55)

Then connect to Client-1. Here use the user you selected

For me, I chose git.rug. 

![image](https://github.com/user-attachments/assets/961b7adc-aa59-4504-ae81-901285c9d328)

![image](https://github.com/user-attachments/assets/3c7bef45-4b39-4c1a-b90f-a394af224141)

But now let's try multiple failed attempts with bad passwords:

But first, we need to set a Lockout policy in Active Directory to strictly allow a handful of password fail

Now, let's log out of this user because we are going to set an account lockout group policy and attempt to lock this user out:

Go back to our DC and right-click the start button > Run > type "gpmc.msc" to bring up the Group Policy Managment Console:

![image](https://github.com/user-attachments/assets/f2b1bfcb-12d6-41cd-9a66-5b6d904a3fc1)

![image](https://github.com/user-attachments/assets/4ccf2512-5329-41a8-9382-a64fb2eef14a)

Expand Domains > mydomain.com > select Default Domain Policy:

![image](https://github.com/user-attachments/assets/3e317450-a9e2-493f-9dd2-148e9aafad08)

Under Computer Configuration expand Policies > Window Settings > Security Settings > select Account Lockout Policy:

![image](https://github.com/user-attachments/assets/d6051435-30c3-41dd-9a25-05355cf28563)

Here we'll select "Account lockout threshold" and choose to make the account lockout after 5 invalid attempts. Make sure to hit Apply:

![image](https://github.com/user-attachments/assets/3b5c0003-68dd-4205-863a-6f362807f27c)

You can double-check this by going to the group policy settings and scrolling down to view the lockout policy:

![image](https://github.com/user-attachments/assets/d250cb14-daea-41ac-9080-d96b9c58f52e)

The policy will update automatically, but it will take some time. We can force the update on client-1 by signing into it as our admin and running "gpupdate /force" in the command line:

![image](https://github.com/user-attachments/assets/6b7538db-1f40-474b-aa98-c78f8561a2e2)

![image](https://github.com/user-attachments/assets/b3e92473-f8c8-44ab-a729-846669e84af9)

Now, we can sign out of client-1 and attempt to sign back into it with our user with an invalid password 5 times. A lockout message will appear:

![image](https://github.com/user-attachments/assets/e5aa2093-d2cb-4aad-8a65-99a594489182)

![image](https://github.com/user-attachments/assets/15d28e5c-d93d-46cf-a370-ef006ad31852)

To unlock the user account, head back to the DC > Active Directory Users and Computers > mydomain.com > _EMPLOYEES > double-click on the locked out user > Account tab > check the "Unlock account" box:

![image](https://github.com/user-attachments/assets/b76ce8ba-e60e-4850-add0-6c212aa94d64)

(Alternatively you could right-click on the user > Reset Password and there will be a "Unlock users account" option. That way you can change the password at the same time you unlock it):

![image](https://github.com/user-attachments/assets/b5203b54-86a1-4ded-8785-882dcdbd7692)

Now our user can sign into clinet-1 as normal:

To disable users, go to the DC and in Active Directory Users and Computers > mydomain.com > _EMPLOYEES > right-click on user you want to disable > Disable:

![image](https://github.com/user-attachments/assets/96102d37-a36a-4fb0-95ef-804e56fc8161)

![image](https://github.com/user-attachments/assets/983f59fc-0f69-4fb6-9a3c-47e479c12ba7)

If we sign out of client-1 now, and try to sign back in using the disabled account, this message will appear:

![image](https://github.com/user-attachments/assets/aaa180a1-33c3-4350-b1f6-1c11d2fafc8b)

To enable the user again go back to the DC and right-click again on the user and select Enable:

![image](https://github.com/user-attachments/assets/67c8917d-c8c3-4f62-aa52-e0886c5d6821)

The user can now log into client-1, so we'll do that and once we are logged in we'll view some logs through the event viewer. To do so, search for "Event Viewer" in the start search bar > Windows Logs > Security. You'll notice we cannot view these:

![image](https://github.com/user-attachments/assets/7798a145-0791-4d56-aca1-b4c94c294f85)

![image](https://github.com/user-attachments/assets/3f17c1d8-3539-4edb-a854-8966f52a4f73)

![image](https://github.com/user-attachments/assets/a333c4c5-da21-46cb-aa57-b7175e715f7d)

This is because we are not an admin. Not to worry, we don't need to sign out of this whole client and sign in as Jane (our admin). We already know her credentials. We just need to close out of this window and open it again, but this time, as an administrator. It will have a pop-up asking for admin credentials:

![image](https://github.com/user-attachments/assets/e9f09409-01f0-4fa8-bbf9-eefebaa4982e)

![image](https://github.com/user-attachments/assets/60aba427-b514-4af7-9bfd-8ae23b16814f)

Now, if we navigate back to the security log page, we can view them:

![image](https://github.com/user-attachments/assets/0401022e-ae29-4c2a-927e-b241bacdd5ec)

Here we can see a bunch of different information like successful and failed log on attempts, who attempted them and from what IP address, along with the date and time they happened:

![image](https://github.com/user-attachments/assets/e4422dc9-5c6e-4431-933c-c4818f1e6963)

## Active Directory: Creating Users, Group Policy and Managing Accounts in Azure is Now Complete

**We've successfully configured Remote Desktop for non-administrative users, automated user creation with PowerShell, and managed group policies. Additionally, we covered account lockouts and log monitoring to simulate a real-life IT environment!**



