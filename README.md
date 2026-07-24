# Active Directory
hands on work on my own active directory for IT/Help Desk roles

-7/1/26
#Ive created my very own virtual machine in order to further advance my IT skills as I look to transition into the workforce.
#I installed virtual box for windows because I want to create and set up my own active directory so that I can apply real world skills and be job ready.

STEP 1:
Downloaded VM software, including my iso files, "Microsoft Evaluation Center Windows Server 2022" and "Microsoft Evaluation Center Windows 11 Enterprise"
Once VM was installed, I created my own windows server in VM with the required ram, memory, and cpu's. I then attacted my iso files in the settings and was ready to boot into the VM.
I booted up into my VM, installed it on the disk and set up my admin password. I was now ready to install my active directory and create my domain.

STEP 2:
I first needed to set up a static IP in the VM.
Firstly, I need to look up my home network IP on my windows PC and find the default gateway so in order to do that I used the "ipconfig" command on the command line to do so.
I then went back into my VM and went onto properties on the network adaptor and under the TCP/IPv4 setting I set my VM's IP.
Why I used a static IP was because in a real world environment, computers at a company will need to find the main servers fixed ip addresss whenever we ping to check issues.

<img width="1069" height="876" alt="Screenshot 2026-07-01 214327" src="https://github.com/user-attachments/assets/947001dd-fd7a-4132-9a7a-c36920d28338" />

STEP 3:
The next step was to promote to a domain controller, in order for me to do that I had to go into the server manager and add a new forest. I called my new forest
under the root domain name, corp.local, I then proceeded with creating a DSRM password incase of a disaster recovery need. I left the rest of the setting as default and went on to install the forest. AFter the install, I went by into the server manager and now had an active directory domian server and DNS.
Why I use these naming conventions is because most companies servers are named by their role, so DC stands for domain controller and 01 means its the first one. For file servers we might see names like FS01, or PRINT01 for printing servers. Having these name conventions help us IT workers know what a server does at first glance.
The reason I created a forest when setting up an active directiory is because its a top level container of the AD environment, which most companies have. The forest is like the whole company's AD structure, while the domain(corp.local) is the branch of that company, and many small companies have one forest with one domain. The reason I used .local is because I wanted an internal domain, which means it only exists on my private network, not the internet. Most companies wanting to keep data on their own private networks use .local or .internal for the same reasons.
<img width="1021" height="842" alt="image" src="https://github.com/user-attachments/assets/e8ed2951-2a39-4188-818a-716e3ecd9490" />

STEP 4: Now I will check and vertify that I installed everything correctly. I went to the tools section of my server manager, clicked on active directory users and computers, and saw that I had my corp.local tree with the right folders underneath it. Another thing I did to check that I installed everything correctly, I clicked on the domain controllers folder and saw that I had DC01 listed inside, which means that I successfully built an active directory domain from scratch!!!
<img width="1023" height="853" alt="image" src="https://github.com/user-attachments/assets/568c0779-99b9-47da-b4be-d09a5af391a6" />

STEP 5: Now my next step was to create a windows client VM, but this time instead of the windows server iso, we will use the windows 11 pro iso key. So I went on to create my client01 user and install windows 11 pro on it. I had a lot of issues and errors come up when trying to install windows 11 pro. For example, it told me that I didn't meet windows 11 system requirements on my virtual computer. It told me that I needed more memory, that I needed TMP 2.0 or higher, and that I needed secure boot enabled. Luckily, I've gone through these issues before with my own pc since I built it myself and installed all the right software/drivers. I knew to fix these issues in the bios so I did. Once those issues were solved, I was able to install windows 11 pro on the client01s virtual computer. 

STEP 6: My next step was to join client01 to the corp.local domain. In order to do this, in my client01 account, I had to set a static IP so that it could point to DC01 so we could connect to doamin controller.

<img width="1492" height="844" alt="image" src="https://github.com/user-attachments/assets/b5f64fef-142f-4cbd-8af6-338f3aa4bf70" />

As you can see from the picture above, we put our preferred DNS server to the DC01 so that client01 can find corp.local when we join it to the domain. We then went on to rename the pc using the advanced settings because that would let us rename and join a domain at the same time. I renamed it to Client01 and under memeber of we selected domain corp.local. It asked for the admin username and password and it welcomed us to the domain as seen below.
<img width="1495" height="859" alt="Screenshot 2026-07-09 230841" src="https://github.com/user-attachments/assets/3aa03770-b40e-4e00-a558-a9870315320b" />

Why did I do this? Well now that we were welcomed into the domain, it's pretty much trusting DC01 to manage this user, so any domain user created on DC01 can log into this machine. All companies go through this when a new employee pc has to be set up.

Now to log in as a domain user, on the client01 login screen, I went onto other user and logged in as CORP\Administrator, and now I was logged in as domain user, NOT local user.
Why I used the CORP\ prefix is to vertify this login against the CORP domain on DC01, NOT the local machine. This is important because this is how all employees log into their work computer, you want their credentials stored on the domain controller, not the local computer. A good example of this is if a user forgets their password, I as an IT worker cab reset it on the server and it would work on any domain computer instantly.

SUMMARY:
After downloading and setting up my virtual machine lab, I created a DC01 user which will be my server(domain controller) or brain of the network. It's the machine that will hold all users or employees, will vertify logins, enfoce the rules on the network, and keep track of every computer that joins the domain. In a real company, there will be one or more of these in a server room where regualr employees don't touch. The corp.local is the Domain and it is essentially the "workspace" that DC01 manages. Think of it like a building — DC01 is the security desk at the front, and corp.local is the building itself. Anyone who wants to work inside that building has to be registered with the security desk. Client01 is the other machine we created in my VM and it represents the employees computer, what a regular employee's computer would look like in a company. When we joined it to corp.local we were simulating what IT does when they set up a new employee's PC. From that point on, the employee logs in with credentials stored on DC01, not on their own machine, IT can manage that computer remotely from the server, and the rules and policies from DC01 apply to that computer automatically. 

I HAVE NOW FULLY CREATED A WORKING ACTIVE DIRECTORY WITH A DOMAIN CONTROLLER AND CLIENT MACHINE JOINED TO THE DOMAIN FROM SCRATCH.

Next step in my project will be to simulate everyday tasks that an IT/help desk employee would be tasked. So I will go into my newly created active directory and create user accounts and organizational units.

In order to create new users and OU's, I need to access the server manager, go to tools, then go into active directory users and computers. I know to go here because this is the main tool to use when dealing with mostly everything in the AD. I know to go into server manager because this is the control panel for my entire domain. I created 3 OU's here, HR, IT, and Sales. They will live here in the domain. Below you can see the 3 OU's that I've created.
<img width="1024" height="850" alt="image" src="https://github.com/user-attachments/assets/b1fd7747-ba7a-4aa1-abd7-97e64d8c7eaf" />

Why did I create OU's? Well because in a real company, users are organized by department. Having OU's allows me working in IT to apply different rules to different departments. For example, HR might have stricter password policies, or the Sales team might get a shared network drive. You can't do any of that without OUs.

I have now created users under each OU with the same naming format as seen in the picture below. I kept the naming format consistent because in a real company, the format is always the same/consistant.
<img width="1021" height="853" alt="image" src="https://github.com/user-attachments/assets/53cc5f76-09cd-44bf-9cd2-e6adaa36cd21" />

Now that I have OUs and users, I will start to practice common IT/help desk everyday tasks.

A common IT/help desk issue is password resets and account lockouts. Below in the picture, a user forgot her password so as you can see, I'm resetting her password by creating a temporary one and enabling "user must change password at next logon" so that the next time she logs in, it forces her to make a new password that I won't know. As IT/help desk I should never know a users password because it's a security risk.
<img width="1029" height="775" alt="image" src="https://github.com/user-attachments/assets/771e2cdc-90d9-4345-addd-fdda8ab9492a" />

Now I will practice disabling and enabling a user account.

In order to disable and enable a user, I must rick-click the user, press disable account and now you should see a small down arrow on the users icon, which means we have disabled the account, you can easily enable the account by rick clicking the user again and pressing enable account. Below you can see the small down arrow in the user jane smith, showing that her account is disabled at the moment. It's important to know how to disable and enable user accounts because when an employee goes on leave or gets terminated, IT doesn't immediately delete their account — they disable it first. This preserves their data and settings while blocking access. Deleting comes later after everything is confirmed.
<img width="1027" height="283" alt="Screenshot 2026-07-20 194759" src="https://github.com/user-attachments/assets/640d042e-77b9-4d2a-9616-c949965c7dd9" />

Now what would happen if a user forgot his/her account password, was inputting the wrong password and after 5 attempts got locked out of her account? As a help desk/IT employee, in my admin account, I would right click the user, go to properties, then click on account tab, there will be an "unlock account" checkbox, it will be checked because the user used all 5 password attempts and got locked out. As IT support, I would uncheck that box and go about resetting the password for the user. It's important to know where to unlock the user account because in real life, companies set a policy that locks accounts after a certain number of failed login attempts — usually 3 to 5. This prevents hackers from guessing passwords. But it also means users who forget their password get locked out, which is where help desk like myself comes in. Below you can see the unchecked checkbox.
<img width="1059" height="653" alt="Screenshot 2026-07-20 204152" src="https://github.com/user-attachments/assets/ede42cba-f85d-4d4f-9201-e9acee57807e" />

Now I will showcase what I know about group policy (GPO), I know that GPO is how IT pushes settings and rules to computers and users across the whole domain automatically. Instead of going to every single computer and changing settings manually, you set it once on DC01 and it applies everywhere. Here below, I have my default group policy, which is the baseline policy that applies to everything in the doamin, I got here by going into the server manager, going into tools, then group policy management, from there I expanded the forest, expanded domains, then expanded my corp.local. 
<img width="863" height="791" alt="image" src="https://github.com/user-attachments/assets/bc7e2b8c-205c-40e4-934a-7917d2ce81e3" />

Now I will create a password policy GPO. In order to do that, I must right click my corp.local forest, click on "create a GPO in this domain and link it here", I will name it password policy. Now that I have that I will right click password policy, hit edit, then navigate to Computer Configuration → Policies → Windows Settings → Security Settings → Account Policies → Password Policy. Here is what it looks like before defining the policy, as you can see below, nothing is defined.
<img width="784" height="342" alt="image" src="https://github.com/user-attachments/assets/7df5b066-f66e-43f6-9382-ce0a2bb2e9ce" />

Now this is what my password policy looks like now after defining some policy settings,
<img width="783" height="301" alt="image" src="https://github.com/user-attachments/assets/2f9d1406-35f1-476d-9500-a53bc41dcb5d" />

Why did I do this? Well because I know that every company has a password policy. Without one users would set passwords like abc123 and never change them. As help desk I'll reference this policy constantly when users complain their password isn't being accepted and tell them exactly what the requirements are.

Now I will create a desktop wallpaper GPO by following the same steps as before with the password policy but this time go into user config instead of computer config, I will enable desktop wallpaper, set a local path, and set the style to fill, as seen below,
<img width="679" height="632" alt="image" src="https://github.com/user-attachments/assets/818f5087-6f5b-4539-aafa-2fa6266ff9ff" />

And here below you can see that Desktop Wallpaper is now enabled,
<img width="692" height="169" alt="image" src="https://github.com/user-attachments/assets/73a74962-5f96-4196-8167-84b4313734e2" />

Why did I do this? Well because I know that companies use wallpaper policies to push their corporate branding to all machines. But more importantly this teaches me how GPOs work — I set it once and it applies to every computer in the domain automatically. In the future, I will know that the same concept applies to much more powerful policies like software installation, security settings, and drive mapping.

Now I will show how to force apply a policy. In order to do that, I will log into my client01 account, then I will open the command prompt as admin, then type "gpupdate /force" Why use that command? Because in real help desk when I make a policy change and need it to apply immediately without waiting for a restart, this is the command I'll run. You can see below that forcing the policy worked.  
<img width="663" height="253" alt="image" src="https://github.com/user-attachments/assets/02a89630-5107-49a0-868c-2706779dee7e" />

The final skill I will showcase is security groups and shared folder access. This is important because this is how companies control who can access what. Instead of giving individual users access to folders one by one, I put them in a group and give the group access. An example would be when a new employee can't access a shared folder, I won't just change the folder permissions — I'll just add them to the right security group. Much faster and cleaner. In order to create a shared folder, I opened up my file explorer, right clicked on the C:drive, created a new folder called HR-Files, went into the properties then sharing tab, went into advanced sharing, checked sahre this folder, clicked permissions, then removed everyone from the list. I then added domain admins, and gave them full control as seen below.
<img width="641" height="654" alt="image" src="https://github.com/user-attachments/assets/5183ebb4-4b53-49f7-a6cc-365e484885a9" />

Now why did I remove everyone? Because by default Windows shares are accessible to everyone on the network. That's a security risk. In a real company every shared folder has specific permissions — only the right people can access it.

Now I will create security groups in the active directory. I will go to the HR OU, right click, create a group and name it HR-Team. Make the scope global and type security. As you can see below, I have created the security group.
<img width="751" height="229" alt="image" src="https://github.com/user-attachments/assets/093c444a-5c9b-4354-879e-58546321746f" />

Why did I pick security type over distribution? Well I know that distribution groups are used for email lists only, can't control access to anything, and thats not what we want. We want security group type because they are used to control access to resources like folders and printers and this is what we want.
Why did I use global scope? because global groups can be used anywhere in the domain. This is the most common type I'll use in IT/help desk roles.

Now I will give the HR-Team access to the folder, so I went to file explorer again and went into the security tab of the HR folder I created and gave them read & execute permissions as seen below.
<img width="748" height="642" alt="image" src="https://github.com/user-attachments/assets/8aebbf4a-b588-4d25-9387-a4c19d4e6ef2" />

Why Read & Execute and not Full Control? Because in real companies I'll give users the minimum permissions they need — this is called the principle of least privilege. HR staff need to read files, not delete or modify everything. This will limit damage if an account gets compromised.

I need to test that I gave the permissions to the HR-Team. So I'll log in as a user in the HR-team, jane smith and try and access the folder from there. As you can see below, I'm able to access the folder.
<img width="937" height="655" alt="image" src="https://github.com/user-attachments/assets/e8011431-9294-4d2b-b5dc-dc5b1ce6de71" />

Now I will log in as a member of the sales team, mike jones, and try the access the HR-Folder, it should give me a permission denied pop up which means I successfully created a shared folder with proper security access. As seen below it worked!
<img width="1024" height="851" alt="image" src="https://github.com/user-attachments/assets/66702157-f45b-4d2d-acc6-378a1eacab12" />

Why do I do these tests? Because this proves my security group is working correctly. jsmith is in HR-Team and can access the folder. mjones is in Sales and can't. In real help desk this is exactly how you'd verify permissions are set correctly after adding someone to a group.

Summary of everything I did:
I built a home Active Directory lab from scratch with Windows Server 2022 and Windows 11. I practiced creating and managing user accounts, resetting passwords, unlocking accounts, building Group Policy Objects, and controlling folder access with security groups. I also troubleshot common issues like DNS misconfigurations, permission problems, and group membership token refreshes.

Now I will showcase my knowledge of one of the most important tools in IT/help desk, that being the event viewer. I know that its basically a log of everything that happens on a Windows machine — logins, errors, warnings, system events. When something goes wrong, Event Viewer is usually the first place I'll look. For me to access event viewer, I'll press the windows key + R and type "eventvwr.msc" to open it. Below is me opening the event viewer.
<img width="879" height="550" alt="image" src="https://github.com/user-attachments/assets/9fe4ce81-50c1-4f46-85be-51ff35c440f9" />
I know that the left side has my window logs, which is the main logs I'll use, then I have the application, which has my errors from software and apps, then my security, which has my login attempts, account changes, and permission changes, lastly my system, which has the hardware and windows errors.

Why do I use event viewer? Because at a real company, when a user calls saying "I can't log in" or "something weird is happening on my computer," Event Viewer tells you exactly what happened and when.

Below is my security events, I know that each event has an event ID, which is a number that tells you what happened, the date/time, which tells me when it happened, and a level, which tells me information, warning, or error.
<img width="878" height="545" alt="image" src="https://github.com/user-attachments/assets/ddb1793b-b30b-4bc8-8ddf-fdd94e97b2bb" />

Below are the most important event ID's that I need to know as a IT/Help desk employee,
<img width="404" height="281" alt="image" src="https://github.com/user-attachments/assets/07059cdf-ad19-43b7-a491-b8e5b19f6ad9" />

Now I will simulate a failed login by logging in as a user on the client01 VM and typing the wrong password for user jsmith 4 times. I will then go back to the event viewer and refresh it and look for event ID 4625, which will be my failed attempts. Below I have filtered for event ID 4625 and you can see my failed logon attempts.
<img width="1017" height="845" alt="image" src="https://github.com/user-attachments/assets/915e8deb-72ea-427c-a5ee-f6424378b260" />

Why is it important for me to look at the event viewer as a IT/help desk employee? Because in real help desk when a user gets locked out, I'll check Event Viewer to see how many failed attempts there were and where they came from. If someone's account shows 50 failed attempts from an unknown machine at 3am I'll know that that's a security incident, not just a forgotten password. Also, in the image above when I filtered to find the event 4625, it's because in a real environment security logs can have thousands of entries per day. Knowing how to filter quickly is an essential skill. I'll use this when investigating a specific incident or looking for a pattern.

Next I will practice remote desktop skills. It's important to know this because remote desktop is a tech like myself will access user computers without physically walking to them. Instead of going to someone's desk, I'll remote into their computer from theirs and fix the issue while they watch or while they're away. In real help desk environments you'll use this constantly — probably every single day. I will first log in as a user and enable remote desktop because windows by default disables remote desktop for security reasons. In a real company I would enable it through Group Policy automatically on all machines when they join the domain — so every computer is ready to be remoted into without having to touch each one individually.

Below, you can see that I'm about to remote connect to user jsmith from my DC01 computer. I know that client01=jsmith has an IPv4 of 10.0.0.20 so thats what I will use to connect.
<img width="1009" height="852" alt="image" src="https://github.com/user-attachments/assets/6ffc6f9e-3a16-459e-a1e6-299cb1ef73b0" />

Below, you can see that I have successfully remote connected into client01 and veritfied by using command hostname on the terminal.
<img width="1105" height="852" alt="image" src="https://github.com/user-attachments/assets/3cc5491f-06bb-429f-a59d-ea25002880ad" />

Why is this important? Because in a real company when a user calls with a problem, I'll ask for their computer name, open mstsc, type their computer name, and I'll instantly on their machine. I can see exactly what they see, move their mouse, open programs, fix issues — all without leaving my desk. This is the core of remote help desk support.

Now, I will show how to map a network drive via GPO. I'm doing this because right now jsmith can access HR-Files but she has to type "\\DC01\HR-Files" manually every time. In a real company that's not how it works — when an employee logs in, their network drives appear automatically. That's done through Group Policy.

Below, I am going into the GP management and under the HR OU creating a new GPO in the domain called "HR Drive Mapping"
<img width="1069" height="754" alt="image" src="https://github.com/user-attachments/assets/6ad77bb7-9644-4606-9cae-2b260827086c" />
Why did I do this? Because I only want HR users to get this mapped drive, not everyone in the company. By linking the GPO to the HR OU instead of the whole domain, it only applies to users inside that OU. This is one of the most powerful things about OUs — I can target policies to specific departments.

Now below I am creating the new mapped drive for HR and putting the location as we have before which is "\\DC01\HR-FIles"
<img width="1100" height="847" alt="image" src="https://github.com/user-attachments/assets/7c11837d-130b-4318-b22f-cb647587d048" />

Why did I use H? Because In real companies the H: drive traditionally stands for Home or department drive. I'll often hear users say "I can't access my H: drive" — now I know exactly what that means and how it's set up.
Why Reconnect is checked? Because this makes Windows reconnect the drive automatically every time the user logs in. Without it the drive might disappear after a restart.

In order to make sure the policy goes through I forced it in the command terminal as seen below
<img width="435" height="245" alt="image" src="https://github.com/user-attachments/assets/48cce2e3-c5e4-4db9-b6b3-d464b534af11" />

Now I will log back into jsmith, who is a part of the HR department, and when I open up the file explorer, I should go down to this PC and I should see a HR Files(H:) drive there already. As you can see below, we have sucessfully mapped a network drive.
<img width="783" height="587" alt="Screenshot 2026-07-23 211456" src="https://github.com/user-attachments/assets/555aaea9-2d9d-4a50-a45e-c7357c51dc6a" />









































