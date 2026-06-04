# Active Directory Home Lab

I built this project to create a fully working Active Directory environment from scratch using VirtualBox on my personal machine. The goal was to simulate what a real company network looks like — a server managing users, computers, and security policies — and to document the whole process including the problems I ran into and how I fixed them.

-----

## What I Built

A Windows domain called `lab.local` running on two virtual machines. One machine is the Domain Controller (DC01) running Windows Server 2019, which handles everything — Active Directory, DNS, and DHCP. The second machine is a Windows 10 client (CLIENT01) that joined the domain and logs in using domain accounts, just like a real employee workstation would.

-----

## Tools and Software Used

- Oracle VirtualBox — to run both virtual machines on my personal computer
- Windows Server 2019 (Evaluation) — operating system for the Domain Controller
- Windows 10 Pro — operating system for the client machine
- Active Directory Domain Services (AD DS) — manages users, groups, and the domain
- DNS Server — handles name resolution inside the lab network
- DHCP Server — automatically assigns IP addresses to client machines
- Group Policy Management — pushes security settings to computers on the domain
- PowerShell — used for network configuration and troubleshooting throughout the build

-----

## Network Setup

```
DC01 (Domain Controller)
  OS: Windows Server 2019
  IP: 192.168.10.1 (static, set on internal network adapter)
  Roles: AD DS, DNS, DHCP
  Domain: lab.local

CLIENT01 (Windows 10 Workstation)
  OS: Windows 10 Pro
  IP: 192.168.10.50 (assigned automatically by DHCP)
  Domain joined: lab.local

Both VMs connected via VirtualBox Internal Network (intnet)
DC01 also has a NAT adapter for internet access
```

-----

## Active Directory Structure

I modeled the OU structure after an oil and gas company to make it feel like a real environment.

```
lab.local
└── oil and gas company
      ├── IT
      │     ├── Dwanyne d. Rock Johnson (therock@lab.local)
      │     └── IT-STAFF (Security Group)
      ├── HR
      │     └── HR-STAFF (Security Group)
      ├── Finance
      │     └── FINANCE-STAFF (Security Group)
      └── Disabled Users
```

-----

## What I Configured

**Active Directory Domain Services**
Set up a brand new forest with the domain name `lab.local`. DC01 was promoted to Domain Controller which automatically configured DNS for the domain.

**DHCP**
Created a scope covering `192.168.10.50` to `192.168.10.150`. Authorized the DHCP server in Active Directory. CLIENT01 picked up an IP address automatically once the network adapters were configured correctly.

**Users and Groups**
Created domain user accounts inside the department OUs. Each department has its own security group. Users were added to their department groups to simulate real access management.

**Group Policy**
Created a GPO called IT POLICY and linked it to the oil and gas company OU. Configured password policy (minimum 10 characters, complexity enabled), account lockout after 5 failed attempts, and screen saver timeout at 15 minutes.

-----

## Screenshots

### 1. Server Manager dashboard on DC01 — VirtualBox switching to fullscreen mode

![Server Manager dashboard open on DC01 with VirtualBox information dialog about switching to fullscreen mode](https://github.com/user-attachments/assets/e02cb69f-57f3-4253-80fd-03be480279a4)

-----

### 2. Running ipconfig in PowerShell on DC01 to verify the static IP assignment

![PowerShell window showing ipconfig output confirming IP address 192.168.10.1 assigned on DC01](https://github.com/user-attachments/assets/f74cafbb-a5d1-44b1-8afa-0cf1f511e688)

-----

### 3. Server Manager dashboard showing all four server roles installed and healthy

![Server Manager Roles and Server Groups view showing AD DS, DHCP, DNS, and File and Storage Services all active with green status indicators](https://github.com/user-attachments/assets/d7ec0d3a-901c-4937-b8c3-97f044b48d27)

-----

### 4. Creating a new domain user account inside the IT Organizational Unit

![New Object User dialog in Active Directory Users and Computers showing Dwanyne d. Rock Johnson being created in lab.local/oil and gas company/IT with logon name [therock@lab.local](mailto:therock@lab.local)](https://github.com/user-attachments/assets/db2d1406-7fea-47de-b679-7bb86bd7c5a2)

-----

### 5. User creation complete — account summary before finishing

![New Object User confirmation screen showing full name Dwanyne d. Rock Johnson, logon name [therock@lab.local](mailto:therock@lab.local), and the note that the user must change their password at next logon](https://github.com/user-attachments/assets/635fe336-382a-4611-9b84-eed47253b17c)

-----

### 6. Using PowerShell to identify both network adapters and their IP assignments on DC01

![PowerShell showing Get-NetAdapter output listing Ethernet and Ethernet 2, followed by Get-NetIPAddress -AddressFamily IPv4 showing the IP address assigned to each adapter](https://github.com/user-attachments/assets/2647702f-9bc1-4ee7-9eda-8ba90f3ff84e)

-----

### 7. Reassigning the static IP from the wrong adapter to the correct Internal Network adapter

![PowerShell showing Remove-NetIPAddress removing 192.168.10.1 from Ethernet, then New-NetIPAddress assigning it to Ethernet 2, followed by Set-DnsClientServerAddress setting DNS to 127.0.0.1 on Ethernet 2, and a failed Restart-Sevice command due to a typo](https://github.com/user-attachments/assets/041b1aa1-fe42-4aa6-9245-ecbf085db78c)

-----

### 8. DHCP service successfully restarted after correcting the command spelling

![PowerShell showing Restart-Service dhcpserver completing successfully with a warning that it waited for the DHCP Server service to start](https://github.com/user-attachments/assets/c376e70d-910a-4901-a6e0-4fa2acb10597)

-----

### 9. CLIENT01 successfully logged in as a domain user after joining lab.local

![Windows 10 Settings Accounts page on CLIENT01 showing the domain user Monkey D. Luffy logged in under the account LAB\Captain confirming the domain join was successful](https://github.com/user-attachments/assets/52d602d6-324c-479b-83a8-f5a7bedbe3dd)

-----

### 10. Group Policy Management console open showing the lab.local forest and domain structure

![Group Policy Management console showing Forest lab.local expanded with lab.local domain, Default Domain Policy, Domain Controllers, and the oil and gas company OU visible in the left panel](https://github.com/user-attachments/assets/037a7aaf-3eb0-4bdc-ad56-c5baa976c332)

-----

### 11. Right-clicking the oil and gas company OU to create and link a new Group Policy Object

![Group Policy Management right-click context menu on the oil and gas company OU with Create a GPO in this domain and Link it here highlighted, showing sub-OUs Disabled Users, Finance, and HR in the left panel](https://github.com/user-attachments/assets/500a25c0-a7b7-492c-bfc7-203e2a1ee4c4)

-----

### 12. IT POLICY GPO editor open — navigating the Security Settings to configure policies

![Group Policy Management Editor showing IT POLICY linked to DC01.LAB.LOCAL with Computer Configuration expanded to Policies, Windows Settings, and Security Settings revealing Account Policies, Local Policies, Event Log, and Windows Firewall options](https://github.com/user-attachments/assets/b09b11d7-e5cf-43dd-893a-3534e47c9960)

-----

### 13. Configuring advanced security policies — Application Control and IP Security on Active Directory

![Group Policy Management Editor showing the full Security Settings tree expanded inside IT POLICY including Software Restriction Policies, Application Control Policies, and IP Security Policies on Active Directory under Computer Configuration](https://github.com/user-attachments/assets/ad797683-93e4-48a7-b3c5-639e60d68abe)

-----

## Problems I Ran Into and How I Fixed Them

**The installer said it could not find the license terms**
The ISO was not attaching correctly and EFI was enabled in VirtualBox. Fixed by disabling EFI in the VM settings, re-attaching the ISO manually, and moving Optical to the top of the boot order.

**CLIENT was getting an APIPA address (169.254.x.x) instead of a DHCP address**
The static IP `192.168.10.1` had been set on the wrong network adapter. DC01 has two adapters — one NAT for internet and one Internal Network for the lab. Used `Get-NetAdapter` and `Get-NetIPAddress` in PowerShell to identify the correct adapter, removed the IP from the wrong one, and reassigned it to Ethernet 2. DHCP was then restarted and CLIENT got its IP immediately.

**CLIENT could not join the domain — “request is not supported”**
Windows 10 Home edition does not support domain join. Used `changepk.exe` with a generic Pro key to upgrade to Windows 10 Pro, then joined the domain via PowerShell using `Add-Computer`.

**Group Policy computer policy failing with a clock error**
Kerberos authentication requires the client and server clocks to be within 5 minutes of each other. VirtualBox offline VMs do not always sync time automatically. Configured DC01.lab.local as the NTP time server for CLIENT. User policy confirmed applying successfully throughout. The computer policy clock issue is documented as a known limitation of offline lab environments.

-----

## What I Learned

- How to build a Windows domain from scratch including all the roles that make it work together
- How Active Directory, DNS, and DHCP depend on each other and need to be configured in the right order
- How Group Policy works and how settings flow from the domain controller down to workstations
- How to use PowerShell to diagnose and fix network adapter configuration issues
- How Kerberos authentication works and why clock sync matters in a domain environment
- How to read error messages and troubleshoot systematically instead of guessing

-----

## What is Next

- Add a PowerShell script to bulk create users from a CSV file
- Set up folder redirection via GPO so user documents save to the server
- Add a second domain controller to practice redundancy
- Use this lab as the foundation for TryHackMe attack and defense scenarios

-----

## Files in This Repo

```
Active-Directory-Home-Lab/
├── README.md
├── screenshots/
└── scripts/
    └── New-LabUser.ps1 — coming soon
```

-----

*Built as part of my IT portfolio. Completed June 4, 2026.*
