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


