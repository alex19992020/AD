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







