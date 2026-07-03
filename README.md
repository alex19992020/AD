# Active Directory
hands on work on my own active directory

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
<img width="1069" height="876" alt="Screenshot 2026-07-01 214327" src="https://github.com/user-attachments/assets/947001dd-fd7a-4132-9a7a-c36920d28338" />

STEP 3:
The next step was to promote to a domain controller, in order for me to do that I had to go into the server manager and add a new forest.
