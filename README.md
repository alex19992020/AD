Active Directory Home Lab: corp.local

A Windows Server 2022 domain built from scratch in VirtualBox to practice the work a Tier 1 help desk technician does every day: onboarding and offboarding users, password resets and lockouts, Group Policy, file share permissions, log investigation, and remote support.

Why I built it: I'm moving into IT support from a software development background. I wanted hands-on experience with the system most corporate help desk tickets touch, and I wanted to understand why things are configured the way they are, not just which buttons to click.

🔗 Part of a series: I later automated many of these tasks with PowerShell. See my PowerShell Help Desk Toolkit 

<img width="706" height="623" alt="image" src="https://github.com/user-attachments/assets/be8f5da1-f1d6-4bff-b73c-7703bbdda75b" />

Domain structure: corp.local → OUs: HR, IT, Sales · Security group: HR-Team · Test users: jsmith (HR), mjones (Sales)

Help desk scenarios I can handle

Ticket:  ||  What I did in the lab:
"I forgot my password"	||  Reset with a temporary password + User must change password at next logon
"My account is locked"	||  Unlocked in ADUC, then confirmed the failed attempts in Event Viewer (ID 4625)
Employee leaving / on leave	 ||  Disabled the account instead of deleting it, which preserves data and allows rollback
"My password isn't being accepted"	||  Know the domain password policy requirements because I configured them
"I can't open the HR folder"	||  Granted access through a security group, then tested with an allowed and a denied user
"My H: drive is missing"	||  Drive mapped by a GPO linked to the HR OU, so I know to check OU placement, group membership, and gpresult /r
"Something's wrong with my PC"	||  Connected over Remote Desktop and verified the session with hostname
New policy isn't applying	 ||  Forced it with gpupdate /force

Troubleshooting log

Things that broke and how I fixed them, written up the way I'd write a ticket.

1. Windows 11 wouldn't install on the client VM
Symptom: Setup said the VM didn't meet Windows 11 requirements: not enough memory, no TPM 2.0, Secure Boot off.
Investigation: Compared the error against the Windows 11 hardware requirements. I'd seen the same thing building my own PC.
Fix: In the VM's settings, raised RAM, enabled EFI with Secure Boot, and added a TPM 2.0 device. 
Takeaway: On a VM, the "BIOS" is the hypervisor's settings. Same checklist as real hardware, different place to fix it.

2. Client01 couldn't join the domain
Symptom 1: In System Properties, the *Member of* options were greyed out, so I couldn't select Domain.
Cause and fix: I had opened System Properties as a standard user. Changing domain membership requires local admin rights, so I reopened `sysdm.cpl` with **Run as administrator**, and the options unlocked.
Symptom 2: The join then failed with *"An Active Directory Domain Controller (AD DC) for the domain 'corp.local' could not be contacted."*
Investigation: Confirmed DC01 was powered on and ran `ping 10.0.0.10` from Client01. It replied, so the network path between the VMs was fine. Client01's preferred DNS was already set to DC01 (`10.0.0.10`).
Fix: Retried the join from the elevated `sysdm.cpl`, and it succeeded.
Root cause: Not confirmed. I didn't run `ipconfig /all` or `nslookup corp.local` while the join was failing, so I can't tell whether it was a timing issue (for example, DC01's DNS service still starting up) or something else.
Takeaway:"Can ping it but can't join" rules out basic connectivity and points to name resolution or the DC's services. Next time I'd run `nslookup corp.local` and `nltest /dsgetdc:corp.local` before retrying, so the fix is backed by evidence instead of a retry that happened to work.

3. jsmith couldn't open HR-Files (two problems at once)
Symptom:Signed in to Client01 as `jsmith` (HR), opening `\\DC01\HR-Files` returned *Windows cannot access*. She should have had access through the HR-Team group.
Problem A: no share permission.** The Security (NTFS) tab gave HR-Team Read & Execute, but Sharing → Advanced Sharing → Permissions listed only Domain Admins. I had removed Everyone when creating the share and never added HR-Team. Over the network, Windows checks both share and NTFS permissions and applies the more restrictive one. With no share entry, HR-Team was blocked before NTFS was even evaluated.
Fix A: Added HR-Team with Read to the share permissions.
Still denied. jsmith was still blocked after Fix A, so I ruled things out:
 `net share` on DC01 listed HR-Files, so the folder was shared.
`ping DC01` from Client01 replied, so name resolution worked.
Both permission tabs showed HR-Team correctly.
Signing out and back in and running `gpupdate /force` changed nothing.
Problem B: she was never in the group.** jsmith's *Member Of* tab in Active Directory Users and Computers listed only Domain Users. My earlier "Add to a group" step hadn't saved. Every permission I'd checked was correct, but they all applied to a group she wasn't in.
Fix B: Added HR-Team from her Member Of tab, applied the change, and had her sign out and back in. Group membership is captured in the user's access token at sign-in, so the change needed a new sign-in to take effect. After that she could open the folder, and `mjones` (Sales) was still denied.
Takeaways: Check the user's actual group membership first, in their Member Of tab or with `whoami /groups` in their session, before touching folder permissions. I spent most of the time verifying permissions for a group she wasn't in.

Key decisions and why

Static IPs, with the client's DNS pointed at DC01 - Clients find the domain controller through DNS records that DC01 hosts. If a client used my router's or a public DNS server, it couldn't locate corp.local to join or log on. A DC's address can't change underneath its clients.
Role-based server names (DC01) - You can tell a server's job at a glance; a file server would be FS01. This is common practice in real environments.
Departmental OUs - GPOs can't be linked to the default Users container. OUs let me target policy by department. The HR drive mapping applies only to HR.
Disable, don't delete - Offboarding starts with disabling so data and group memberships are preserved and the change can be reversed.
Access through security groups, not individual users - Onboarding becomes "add to group" instead of editing folder permissions. Security type, not distribution, because distribution groups can't grant access to anything.
Read & Execute for HR-Team (least privilege) - Users get the minimum access they need, which limits damage if an account is compromised.
Removed Everyone from the share - New shares grant Everyone read access by default. Access should be explicit.
Drive mapping GPO linked to the HR OU, with Reconnect enabled - Only HR gets the H: drive, and it comes back at every logon.
Filtered Event Viewer by ID 4625 - Real security logs have thousands of events a day. Filtering is how you answer "how many failed attempts, when, and from where?"

What I'd do differently

Use a subdomain of a real domain instead of .local. I picked .local because it felt "internal only." Microsoft recommends a subdomain of a domain you own (e.g., ad.example.com), because .local collides with multicast DNS (Bonjour) and can't get public certificates.
Stop using the built-in Administrator for daily work. I logged into the client as CORP\Administrator. In production, admins use a normal account for daily work and a separate admin account only when needed.
Put the file share on a member server, not the DC. HR-Files lives on DC01's C: drive. A dedicated file server (FS01) keeps the DC's attack surface small.
Enforce password policy correctly per group. I originally assumed I could give HR a stricter password policy by linking a GPO to the HR OU. Domain account password policy only applies at the domain level. Per-group policies require Fine-Grained Password Policies.
Enable RDP through Group Policy instead of turning it on manually on each machine.
Use the right tool for "watch while I fix it." RDP takes over the session, so the user can't watch. For side-by-side help I'd use Quick Assist or Remote Assistance.
Add a second DC. One DC is a single point of failure for every login in the domain.

Full build walkthrough:

Build the server. Downloaded Windows Server 2022 and Windows 11 Enterprise evaluation ISOs from the Microsoft Evaluation Center, created the DC01 VM in VirtualBox, attached the ISO, and installed the OS.

Set a static IP. Checked my host's network with ipconfig, then set DC01's IPv4 address manually in the adapter's TCP/IPv4 properties.

<img width="1069" alt="DC01 static IPv4 configuration" src="https://github.com/user-attachments/assets/947001dd-fd7a-4132-9a7a-c36920d28338" />

Promote to domain controller. In Server Manager, added the AD DS role and created a new forest with the root domain corp.local. Set a DSRM password for disaster recovery. AD DS and DNS were installed together.

<img width="1021" alt="Promoting DC01 and creating the corp.local forest" src="https://github.com/user-attachments/assets/e8ed2951-2a39-4188-818a-716e3ecd9490" />

Verify. In Active Directory Users and Computers, confirmed the corp.local tree and DC01 in the Domain Controllers OU.

<img width="1023" alt="ADUC showing corp.local and DC01 in Domain Controllers" src="https://github.com/user-attachments/assets/568c0779-99b9-47da-b4be-d09a5af391a6" />

Join the client. Gave Client01 a static IP with DC01 (10.0.0.10) as its preferred DNS server, then renamed the PC and joined corp.local in one step through System Properties.

<img width="1492" alt="Client01 DNS pointed at DC01" src="https://github.com/user-attachments/assets/b5f64fef-142f-4cbd-8af6-338f3aa4bf70" /> <img width="1495" alt="Welcome to the corp.local domain" src="https://github.com/user-attachments/assets/3aa03770-b40e-4e00-a558-a9870315320b" />

Log in as a domain user. Signed in as CORP\<user>. The prefix authenticates against DC01 instead of the local machine. That's why a password reset on the server works on every domain computer instantly.

</details> <details> <summary><b>Phase 2: OUs, users, and account management</b></summary>

Created HR, IT, and Sales OUs and added users with a consistent naming format.

<img width="1024" alt="HR, IT and Sales OUs in ADUC" src="https://github.com/user-attachments/assets/b1fd7747-ba7a-4aa1-abd7-97e64d8c7eaf" /> <img width="1021" alt="Users created in each OU" src="https://github.com/user-attachments/assets/53cc5f76-09cd-44bf-9cd2-e6adaa36cd21" />

Password reset: temporary password + User must change password at next logon, so IT never knows the user's real password.

<img width="1029" alt="Resetting a user's password" src="https://github.com/user-attachments/assets/771e2cdc-90d9-4345-addd-fdda8ab9492a" />

Disable / enable: a disabled account shows a down-arrow icon.

<img width="1027" alt="Disabled account in ADUC" src="https://github.com/user-attachments/assets/640d042e-77b9-4d2a-9616-c949965c7dd9" />

Unlock: Properties → Account → Unlock account. ✏️ If you configured an Account Lockout Policy (threshold of 5), say so here. Lockout only happens when that policy is set.

<img width="1059" alt="Unlock account checkbox" src="https://github.com/user-attachments/assets/ede42cba-f85d-4d4f-9201-e9acee57807e" /> </details> <details> <summary><b>Phase 3: Group Policy</b></summary>

Opened Group Policy Management → Forest → Domains → corp.local.

<img width="863" alt="Group Policy Management console" src="https://github.com/user-attachments/assets/bc7e2b8c-205c-40e4-934a-7917d2ce81e3" />

Password policy: created and linked a GPO at the domain level, then configured Computer Configuration → Policies → Windows Settings → Security Settings → Account Policies → Password Policy.

<img width="784" alt="Password policy before configuration" src="https://github.com/user-attachments/assets/7df5b066-f66e-43f6-9382-ce0a2bb2e9ce" /> <img width="783" alt="Password policy after configuration" src="https://github.com/user-attachments/assets/2f9d1406-35f1-476d-9500-a53bc41dcb5d" />

Desktop wallpaper (User Configuration): a low-risk way to prove a GPO reaches clients. The same mechanism deploys software, security settings, and drive maps.

<img width="679" alt="Desktop wallpaper policy settings" src="https://github.com/user-attachments/assets/818f5087-6f5b-4539-aafa-2fa6266ff9ff" /> <img width="692" alt="Desktop wallpaper policy enabled" src="https://github.com/user-attachments/assets/73a74962-5f96-4196-8167-84b4313734e2" />

Force an update: gpupdate /force on Client01 applies changes immediately.

<img width="663" alt="gpupdate /force output" src="https://github.com/user-attachments/assets/02a89630-5107-49a0-868c-2706779dee7e" /> </details> <details> <summary><b>Phase 4: Security groups and shared folder access</b></summary>

Created HR-Files on DC01 and shared it, removing Everyone from the share permissions.

<img width="641" alt="Share permissions for HR-Files" src="https://github.com/user-attachments/assets/5183ebb4-4b53-49f7-a6cc-365e484885a9" />

Created HR-Team (Global, Security) in the HR OU.

<img width="751" alt="HR-Team security group" src="https://github.com/user-attachments/assets/093c444a-5c9b-4354-879e-58546321746f" />

Granted HR-Team Read & Execute on the NTFS (Security) tab.

<img width="748" alt="NTFS permissions for HR-Team" src="https://github.com/user-attachments/assets/8aebbf4a-b588-4d25-9387-a4c19d4e6ef2" />

Share vs. NTFS permissions: when a folder is accessed over the network, both apply and the more restrictive one wins. HR-Team has **Read** on the share and **Read & Execute** on NTFS; Domain Admins keeps Full Control on the share. Getting this right took some troubleshooting (see Troubleshooting #3).

Test both sides: jsmith (HR) can open the folder; mjones (Sales) gets access denied.

<img width="937" alt="jsmith can access HR-Files" src="https://github.com/user-attachments/assets/e8011431-9294-4d2b-b5dc-dc5b1ce6de71" /> <img width="1024" alt="mjones is denied access to HR-Files" src="https://github.com/user-attachments/assets/66702157-f45b-4d2d-acc6-378a1eacab12" /> </details> <details> <summary><b>Phase 5: Event Viewer, Remote Desktop, and drive mapping</b></summary>

Event Viewer (eventvwr.msc): Application, Security, and System logs; each event has an ID, timestamp, and level.

<img width="879" alt="Event Viewer overview" src="https://github.com/user-attachments/assets/9fe4ce81-50c1-4f46-85be-51ff35c440f9" /> <img width="878" alt="Security log events" src="https://github.com/user-attachments/assets/ddb1793b-b30b-4bc8-8ddf-fdd94e97b2bb" /> <img width="404" alt="Key event IDs for help desk" src="https://github.com/user-attachments/assets/07059cdf-ad19-43b7-a491-b8e5b19f6ad9" />

Simulated failed logons: entered a wrong password for jsmith 4 times, then filtered the Security log for Event ID 4625. Fifty failures from an unknown machine at 3 a.m. is a security incident, not a forgotten password.

<img width="1017" alt="Event ID 4625 failed logons filtered" src="https://github.com/user-attachments/assets/915e8deb-72ea-427c-a5ee-f6424378b260" />

Remote Desktop: enabled RDP on Client01, connected from DC01 with mstsc, and verified the session with hostname.

<img width="1009" alt="Connecting to Client01 with mstsc" src="https://github.com/user-attachments/assets/6ffc6f9e-3a16-459e-a1e6-299cb1ef73b0" /> <img width="1105" alt="hostname confirms the RDP session on Client01" src="https://github.com/user-attachments/assets/3cc5491f-06bb-429f-a59d-ea25002880ad" />

Drive mapping by GPO: created HR Drive Mapping linked to the HR OU, mapping \\DC01\HR-Files to H: with Reconnect enabled.

<img width="1069" alt="Creating the HR Drive Mapping GPO" src="https://github.com/user-attachments/assets/6ad77bb7-9644-4606-9cae-2b260827086c" /> <img width="1100" alt="Mapped drive settings for H:" src="https://github.com/user-attachments/assets/7c11837d-130b-4318-b22f-cb647587d048" /> <img width="435" alt="gpupdate /force on Client01" src="https://github.com/user-attachments/assets/48cce2e3-c5e4-4db9-b6b3-d464b534af11" /> <img width="783" alt="H: drive appears for jsmith" src="https://github.com/user-attachments/assets/555aaea9-2d9d-4a50-a45e-c7357c51dc6a" /> </details>

Tools & skills: Active Directory Domain Services · DNS · Group Policy · NTFS & share permissions · Security groups · Event Viewer · Remote Desktop · VirtualBox · Windows Server 2022 · Windows 11













































